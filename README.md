# Letter to Myself

A Claude Code plugin for **context persistence between sessions**.

When sessions end, chats get compacted, or days pass between work blocks, Claude forgets everything. This plugin solves that by writing structured "handoff letters" to a local `.memory/` folder that Claude reads on startup.

**Result:** Less re-explaining, fewer repeated mistakes, smoother "back into flow" experience.

---

## Features

### Core: Session Memory
Save structured session summaries with a single command:
- What we did (high-signal summary)
- Why we did it (decisions + reasoning)
- Pain Log (critical errors, root causes, workarounds)
- State that matters (variables, constraints, risks)
- Next steps (actionable, ranked)

### Letter to Blog Pipeline
Automatically transform session memories into public-ready blog posts via GitHub Actions + Claude API. Perfect for "building in public" with zero friction.

---

## Quick Start

### 1. Install the Plugin

**Option A: Install from the Claude Marketplace (recommended)**

1. Open Claude Code
2. Run `/marketplace`
3. Search for **letter-for-my-future-self**
4. Select the plugin and follow the prompts to install

Or install directly via CLI:

```bash
claude plugin install --from mrwind-up-bird/letter-for-my-future-self
```

**Option B: Install from source**

```bash
git clone https://github.com/mrwind-up-bird/letter-for-my-future-self.git
cd letter-for-my-future-self
claude plugin install . --scope user
```

### 2. Add to Your Project

Copy the template to your project:

```bash
cp /path/to/letter-for-my-future-self/CLAUDE_TEMPLATE.md /path/to/your-project/CLAUDE.md
```

If you already have a `CLAUDE.md`, merge the relevant sections.

### 3. Save Your First Checkpoint

Start Claude Code in your project:

```bash
cd /path/to/your-project
claude
```

When ending a session, run:

```
/checkpoint
```

Or say "wrap up", "exit", or "end session".

A memory file appears in `.memory/letter_YYYYMMDDHHMMSS.md`.

### 4. Resume Next Session

```bash
claude
```

Claude reads the latest letter and picks up where you left off.

---

<!-- AUTO-GENERATED: commands -->
## Commands

| Command | Description |
|---------|-------------|
| `/checkpoint` | Save a session memory to `.memory/` |
| `/letter-init` | Set up the Letter to Blog CI/CD pipeline |

### blog_gen.py CLI

| Flag | Description |
|------|-------------|
| `--file`, `-f` | Convert a specific memory file (filename or path) |
| `--setup` | Configure global API key (`~/.config/letter-for-my-future-self/`) |
| `--setup-project` | Configure project-specific API key (`.letter-config.json`) |
| `--status` | Show current API key configuration status |
<!-- /AUTO-GENERATED: commands -->

---

## Letter to Blog Pipeline

Transform private session memories into polished blog posts automatically.

### How It Works

```
.memory/letter_03.md  →  GitHub Actions  →  Claude API  →  drafts/blog_2026-01-29_letter_03.md
```

1. You work normally — Claude saves memories to `.memory/`
2. Git push triggers — GitHub Actions detects `.memory/**` changes
3. Claude transforms — blog_gen.py calls Anthropic API
4. PR created — Generated blog post appears in `drafts/`
5. You review & publish — Edit if needed, then merge

### Setup

Initialize the pipeline:

```
/letter-init
```

This creates:
- `.github/scripts/blog_gen.py` — Python generator script
- `.github/scripts/vibe_requirements.txt` — Dependencies
- `.github/workflows/vibe_publisher.yml` — GitHub Actions workflow
- `drafts/` — Output directory

Add your API key to GitHub Secrets:
1. Go to **Settings → Secrets and variables → Actions**
2. Add secret named `ANTHROPIC_API_KEY`
3. Value: Your key from https://console.anthropic.com

Commit and push:

```bash
git add .github/ drafts/
git commit -m "feat: add letter to blog pipeline"
git push
```

See [LETTER_TO_BLOG.md](./LETTER_TO_BLOG.md) for full documentation.

---

## Memory File Format

Each checkpoint creates a file like `.memory/letter_20260130143200.md`:

```markdown
# Letter to Myself (Session Handoff)

**Date:** 2026-01-30 14:32

## 1. Executive Summary
* **Goal:** Building a REST API for user authentication
* **Current Status:** Stopped at JWT refresh token implementation

## 2. The "Done" List (Context Anchor)
* Implemented user registration endpoint in `src/routes/auth.ts`
* Added password hashing with bcrypt
* Created PostgreSQL schema in `migrations/001_users.sql`

## 3. The "Pain" Log (CRITICAL)
* **Tried:** jsonwebtoken library for JWT signing
* **Failed:** "Algorithm not supported" error with RS256
* **Workaround:** Switched to HS256 with environment secret
* *Note:* Do not retry RS256 without proper key configuration.

## 4. Active Variable State
* PORT=3000, DATABASE_URL in .env
* Test user: test@example.com / password123

## 5. Immediate Next Steps
1. [ ] Implement refresh token rotation
2. [ ] Add rate limiting to auth endpoints
3. [ ] Write integration tests
```

---

## Git Versioning

Track `.memory/` in Git to maintain a timeline of your project's decisions.

### Basic Setup

```bash
mkdir -p .memory
git add .memory
git commit -m "chore(memory): start tracking session memory"
```

### Team Workflow (Shared vs Private)

```bash
mkdir -p .memory/shared .memory/private
echo ".memory/private/" >> .gitignore
```

- **Shared:** Architecture decisions, sync with team
- **Private:** Personal scratchpad, local only

### Security: Scan for Secrets

```bash
rg -n --hidden --glob ".memory/**" \
  -e "AKIA[0-9A-Z]{16}" \
  -e "BEGIN( RSA)? PRIVATE KEY" \
  .memory || echo "Clean"
```

See [MEMORY_VERSIONING.md](./MEMORY_VERSIONING.md) for comprehensive workflows.

---

<!-- AUTO-GENERATED: project-structure -->
## Project Structure

```
.
├── .claude-plugin/
│   ├── plugin.json           # Plugin manifest (v1.0.5)
│   └── marketplace.json      # Marketplace listing metadata
├── skills/
│   ├── letter-checkpoint/
│   │   └── SKILL.md          # /checkpoint command
│   └── letter-init/
│       └── SKILL.md          # /letter-init command
├── agents/
│   └── letter-for-myself.md  # Agent persona
├── hooks/
│   └── hooks.json            # Setup & SessionStart hooks
├── scripts/
│   ├── setup-api-key.sh      # First-time setup (agent + API key)
│   └── check-project-key.sh  # Per-project initialization
├── .github/
│   ├── scripts/
│   │   ├── blog_gen.py       # Blog generator (CLI with --setup/--status/--file)
│   │   └── vibe_requirements.txt
│   └── workflows/
│       └── vibe_publisher.yml
├── CLAUDE_TEMPLATE.md        # Template for user projects
├── QUICK_START.md            # Installation guide
├── LETTER_TO_BLOG.md         # Blog pipeline docs
└── MEMORY_VERSIONING.md      # Git workflow guide
```
<!-- /AUTO-GENERATED: project-structure -->

---

## Documentation

| Document | Description |
|----------|-------------|
| [QUICK_START.md](./QUICK_START.md) | Step-by-step installation guide |
| [LETTER_TO_BLOG.md](./LETTER_TO_BLOG.md) | Blog pipeline documentation |
| [MEMORY_VERSIONING.md](./MEMORY_VERSIONING.md) | Git workflows for `.memory/` |
| [CLAUDE_TEMPLATE.md](./CLAUDE_TEMPLATE.md) | Template for your projects |

---

## Troubleshooting

**Plugin commands don't appear**
- Restart Claude Code
- Check plugin is installed: `claude plugin list`
- Verify `.claude-plugin/plugin.json` exists

**No `.memory/` output**
- Run `/checkpoint` explicitly
- Check `CLAUDE.md` includes the template

**Blog pipeline doesn't trigger**
- Verify pushing to `main` branch
- Check `ANTHROPIC_API_KEY` secret is set
- Look at Actions tab for errors

---

## License

MIT

---

Built for developers who want Claude to remember what matters.
