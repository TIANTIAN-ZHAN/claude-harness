# bootstrap-harness — mechanical gates (day-one templates)

Why gates ship on day one: a 10-day natural experiment (LinkPea 2026-07, three doc-overhaul rounds) — **every CI-gated invariant stayed correct; every ungated prose area rotted.** Prose only stays true when something checks it. Reference implementation of the grown-up form: LinkPea `scripts/check_harness.mjs` (11 gates, each with a "why it exists" header and a fix inside the error message).

Two templates below. Adapt paths in the config block — don't change the shape:
1. `scripts/check-harness.mjs` — 4 starter gates. Wire into the existing PR gate/CI (`node scripts/check-harness.mjs`); runs standalone too.
2. `.github/workflows/weekly-harness-check.yml` — weekly full check + green-AND-red notification (generate only if the repo has CI).

---

## Template: `scripts/check-harness.mjs`

```js
#!/usr/bin/env node
// check-harness.mjs — mechanical harness gates. Zero deps (node:fs + git). CI: `node scripts/check-harness.mjs`.
// Every gate states WHY it exists; every error carries its fix. Grow gates over time:
// each NEW rot class found → first ask "can this become a gate?" (see Growing more gates).
import { execFileSync } from 'node:child_process';
import { existsSync, readFileSync, readdirSync } from 'node:fs';
import { fileURLToPath } from 'node:url';
import { dirname, join, resolve } from 'node:path';

// ── config: adjust to your repo ──
const ROOT = fileURLToPath(new URL('..', import.meta.url));
const ACTIVE = 'docs/exec-plans/active';
const COMPLETED = 'docs/exec-plans/completed';
const INDEX = 'docs/exec-plans/README.md';
const STALE_DAYS = 21;               // active plan silent longer than this → push / park / move out
const ARCHIVE_SEGMENT = '/archive/'; // gitignored local-only area — tracked docs must not LINK into it
const STATUS_RE = /^>\s*\*{0,2}\s*(?:状态|Status)\s*\*{0,2}\s*[:：]\s*(\S+)/mu;
const VOCAB = ['📋', '🔄', '⏸️', '📌', '✅']; // planned / in-progress / parked(+Revisit) / standing / done

const errors = [];
const norm = (p) => p.replaceAll('\\', '/');
const lsMd = (dir) => existsSync(join(ROOT, dir)) ? readdirSync(join(ROOT, dir)).filter((f) => f.endsWith('.md')) : [];
const git = (...a) => { try { return execFileSync('git', a, { cwd: ROOT }).toString('utf8').trim(); } catch { return ''; } };
const activeFiles = lsMd(ACTIVE);
const completedFiles = lsMd(COMPLETED);

// ── Gate 1 · status line ── Why: a plan with no machine-readable status silently escapes
// every other gate; an uncontrolled status word can't be parsed by anyone, human or CI.
// ── Gate 2 · lifecycle ── Why: ✅ left in active/ is the #1 rot ("move later" = never);
// a park (⏸️) without a Revisit is an unbounded sink; a stale non-parked plan is dead work hiding as live.
for (const f of activeFiles) {
  const txt = readFileSync(join(ROOT, ACTIVE, f), 'utf8');
  const m = txt.match(STATUS_RE);
  if (!m) { errors.push(`Gate1 · ${ACTIVE}/${f}: no status line — add "> 状态:<emoji> …" / "> Status: <emoji> …" at top (vocab: ${VOCAB.join(' ')})`); continue; }
  const s = m[1];
  if (!VOCAB.some((v) => s.startsWith(v))) errors.push(`Gate1 · ${ACTIVE}/${f}: status "${s}" outside controlled vocab ${VOCAB.join(' ')} — pick one`);
  if (s.startsWith('✅')) errors.push(`Gate2 · ${ACTIVE}/${f}: marked done → git mv to ${COMPLETED}/ in the SAME commit (don't keep as "reference")`);
  const line = (txt.match(/^>.*(?:状态|Status).*$/mu) || [''])[0];
  if (s.startsWith('⏸️') && !/Revisit/iu.test(line)) errors.push(`Gate2 · ${ACTIVE}/${f}: parked (⏸️) without "Revisit:<condition|date>" — a park with no revisit never returns`);
  if (!s.startsWith('📌') && !s.startsWith('⏸️') && !s.startsWith('✅')) {
    const ts = git('log', '-1', '--format=%ct', '--', `${ACTIVE}/${f}`);
    if (ts) { // soft-skip: no git / uncommitted / shallow clone (weekly run uses fetch-depth:0 for truth)
      const days = Math.floor((Date.now() / 1000 - Number(ts)) / 86400);
      if (days > STALE_DAYS) errors.push(`Gate2 · ${ACTIVE}/${f}: ${days} days without a commit and not 📌/⏸️ — push it, park it (⏸️ + Revisit), or move it out`);
    }
  }
}

// ── Gate 3 · two-way index ↔ directories ── Why: an index that drifts from the directory is
// worse than none — agents trust it. Both directions, BOTH buckets (an unwatched completed
// index drifted twice in 10 days while the gated active index stayed clean).
if (existsSync(join(ROOT, INDEX))) {
  const idx = readFileSync(join(ROOT, INDEX), 'utf8');
  for (const [dir, files] of [[ACTIVE, activeFiles], [COMPLETED, completedFiles]]) {
    const base = dir.split('/').pop();
    const refs = new Set([...idx.matchAll(new RegExp(`\\(([^)]*/)?${base}/([A-Za-z0-9._-]+\\.md)\\)`, 'g'))].map((m) => m[2]));
    for (const f of files) if (!refs.has(f)) errors.push(`Gate3 · ${dir}/${f}: exists but not listed in ${INDEX} — add a row (same commit as the file move)`);
    for (const r of refs) if (!files.includes(r)) errors.push(`Gate3 · ${INDEX} → ${base}/${r}: dangling row (file moved/deleted) — fix or drop the row`);
  }
}

// ── Gate 4 · link health ── Why: broken links rot navigation; links into a gitignored archive
// are worse — they click fine locally and 404 in EVERY clone. Fix for those: inline the 3-5 line
// conclusion, keep the path as backtick text + "local archive" note (do NOT commit the archive).
const SKIP = ['http://', 'https://', 'mailto:'];
const PLACEHOLDERS = new Set(['path', '…', '...']);
for (const rel of git('ls-files', '--', '*.md').split('\n').filter(Boolean)) {
  const abs = join(ROOT, rel);
  if (!existsSync(abs)) continue;
  const txt = readFileSync(abs, 'utf8');
  for (const m of txt.matchAll(/\]\(([^)#\s]+?)(?:#[^)]*)?\)/g)) {
    const t = m[1];
    if (!t || SKIP.some((p) => t.startsWith(p)) || PLACEHOLDERS.has(t)) continue;
    const line = txt.slice(0, m.index).split('\n').length;
    const target = norm(resolve(dirname(abs), t));
    if (!target.startsWith(norm(ROOT))) continue; // cross-repo reference — out of scope
    if (target.includes(ARCHIVE_SEGMENT)) { errors.push(`Gate4 · ${rel}:${line} clickable link into gitignored archive "${t}" — inline the conclusion + backtick path + "local archive" note`); continue; }
    if (!existsSync(target)) errors.push(`Gate4 · ${rel}:${line} broken link → ${t} (fix the link or track the target)`);
  }
}

if (errors.length) {
  console.error(`❌ harness check failed (${errors.length}):`);
  for (const e of errors) console.error('  - ' + e);
  process.exit(1);
}
console.log(`✅ harness check passed — active ${activeFiles.length} (status lines ok, none stale), completed ${completedFiles.length} (index two-way), links healthy`);

// ── Growing more gates ──
// Gates are grown, not designed up front. Every time /review-harness (or anyone) finds a NEW
// rot class, the first question is "can this become a gate?" — only semantic judgment stays in
// the monthly slow lane. Proven next gates (see LinkPea check_harness.mjs for working code):
//   · count literals derived from source of truth (README says "21 capabilities", registry has 15 → red)
//   · lessons index ↔ entries two-way + unique ids (parallel sessions grabbing the same L-051)
//   · ADR "Status:" line with controlled vocab (Accepted/Superseded/Proposed/Deprecated/Amended —
//     catches "overturned in the body, status line never touched")
//   · no brace-glob pseudo-links `](…{a,b}.md)` (unclickable, invisible to grep AND to Gate 4)
```

---

## Template: `.github/workflows/weekly-harness-check.yml`

Generate **only if the repo already has CI** (`.github/workflows/`, `.forgejo/workflows/`, `.gitlab-ci.yml`…— match the local dialect; the YAML below is GitHub/Forgejo-compatible).

```yaml
# Weekly harness check → notification. Why: an audit without cadence is no audit —
# prescriptions left unchecked for 10 days grew into repo-wide rot (LinkPea 2026-07).
# Push BOTH green and red: green = one "no drift" line; a silent week = this check itself
# died (built-in deadman). Slow lane stays /review-harness (monthly + after line closeouts).
name: weekly-harness-check
on:
  schedule:
    - cron: "30 1 * * 1"   # Monday morning — express in UTC for your timezone
  workflow_dispatch: {}     # keep: manual dispatch is how you ACCEPT this chain (see below)

jobs:
  check:
    runs-on: ubuntu-latest  # Forgejo/Gitea: runs-on: docker + container: {image: node:22-bookworm}
    timeout-minutes: 10
    steps:
      - uses: actions/checkout@v4
        with:
          fetch-depth: 0    # full history — the staleness gate needs real per-file commit times
      - name: check + notify (green AND red)
        run: |
          set +e
          OUT=$(node scripts/check-harness.mjs 2>&1)
          CODE=$?
          set -e
          echo "$OUT"
          if [ "$CODE" -eq 0 ]; then TITLE="harness ✅ no drift this week"; BODY=$(echo "$OUT" | tail -1); else TITLE="harness ❌ drift found"; BODY=$(echo "$OUT" | tail -30); fi
          # <NOTIFY: replace with your channel — ONE curl, plain line-based text (no md tables):
          #   Slack:  curl -s -m 10 "$SLACK_WEBHOOK" -H 'Content-Type: application/json' -d "$(jq -nc --arg t "$TITLE" --arg b "$BODY" '{text:($t+"\n"+$b)}')"
          #   ntfy:   curl -s -m 10 -H "Title: $TITLE" -d "$BODY" https://ntfy.sh/<topic>
          #   or any IM webhook (WeCom / PushPlus / Telegram bot) via a repo secret >
          exit $CODE
```

**Acceptance is a real message, not a merged YAML** — after wiring, run `workflow_dispatch` once and confirm the notification *arrives on your phone/channel*. A notification chain that is merely configured has not shipped; every "it was on all along but never fired" incident traces back to skipping this step. Tell the owner this explicitly in the closing report.
