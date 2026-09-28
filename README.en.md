# LinkedIn Agent Skill — Turkish edition

[Türkçe](README.md) · **English**

Eleven Claude Code skills that run a LinkedIn account, adapted for
Turkish-language writing. They write posts, comments and replies, score your
profile out of 100, and plan your week. Free, MIT, no signup, no API key,
nothing to connect.

One of them is the **humanizer**: it strips em dashes, corporate filler and
invisible characters out of a draft, then scores what is left on a
five-check panel. This edition teaches it Turkish.

**Nothing gets posted until you say yes.** These skills write. You post.

## What this edition adds

- **Turkish by default.** Every skill writes in Turkish unless you ask for
  English.
- **Sensitive-info filter.** Each skill checks the "Sınırlar" (off-limits)
  section of your `voice.md` before drafting and stops to ask if anything on
  that list has leaked into a draft. Useful if your employer, clients or an
  NDA limit what you can say.
- **Turkish lexicon.** 47 Turkish corporate clichés and stock openers
  ("sinerji", "ekosistem", "büyük bir gururla paylaşmak isterim", ...) and 5
  Turkish structural tells ("sadece X değil, aynı zamanda Y", "Sizce?", ...)
  added to [`slop.json`](skills/li-human/slop.json).
- **Turkish-aware scoring.** `detect.py` counts Turkish letters as word
  characters, recognises Turkish capitals (İ, Ş, Ğ) for proper nouns,
  measures VOICE with Turkish first-person suffixes (-yorum, -dım, -dik)
  instead of English contractions, and does not penalise pronoun dropping,
  which is normal in Turkish.
- **A Turkish `voice.md` template** in [`templates/`](templates/voice.md).

A real, human-written Turkish technical post scored `HUMAN SCORE 76.9 PASS`;
a deliberately cliché-heavy draft scored `24.3 FLAGGED` and was flagged for
the right reasons.

## Install

Paste this into Claude:

```
https://github.com/furkanbilalakyuz/linkedin-agent-skill-tr

Install this skill pack, then confirm /li-post works.
```

Or by hand (macOS / Linux / Windows Git Bash):

```bash
git clone https://github.com/furkanbilalakyuz/linkedin-agent-skill-tr.git
cd linkedin-agent-skill-tr
cp -r skills/li-* ~/.claude/skills/
mkdir -p ~/.claude/linkedin
cp templates/voice.md ~/.claude/linkedin/voice.md
```

Or as a plugin:

```
/plugin marketplace add furkanbilalakyuz/linkedin-agent-skill-tr
/plugin install linkedin-agent-tr
```

Requires [Claude Code](https://claude.com/claude-code). Python 3 is needed
only for `/li-human` scoring; no packages to install.

Then fill in `~/.claude/linkedin/voice.md`, or paste three of your own posts
into Claude and say "write my voice.md from these". **Fill in the "Sınırlar"
section** — it is what the skills check before every draft.

## The eleven

| command | what it does |
| --- | --- |
| `/li-post` | One idea into a post. Three hook options from [21 formulas](skills/li-post/hooks.json), one full draft, humanized before you see it. |
| `/li-comment` | Comments on other people's posts. Never "Great post!". |
| `/li-reply` | The thread under your own post, sorted by which comments are worth answering. |
| `/li-profile` | Scores your profile against a [12-part rubric](skills/li-profile/rubric.json) out of 100, then rewrites in fix-first order. |
| `/li-plan` | The week: what to post, when, and who to engage with. |
| `/li-human` | The humanizer. Two scripts that actually run. |
| `/li-carousel` | Document posts: slide-by-slide copy and the cover. |
| `/li-repurpose` | One video, newsletter or transcript into a week of posts. |
| `/li-dm` | The 200-character invite note, the first message, two follow-ups. |
| `/li-inbox` | Triages the inbox into lead / recruiter / peer / ask / spam. |
| `/li-audit` | Post-mortem on what you have already published. |

## The humanizer

```bash
python3 humanize.py draft.txt --report      # clean it, show every change
python3 detect.py draft.txt                  # score it, five checks
python3 detect.py before.txt after.txt       # prove the delta
```

Fixed automatically: invisible characters, typography (em dashes, curly
quotes, ellipsis characters) and a 160-entry lexicon (113 English, 47
Turkish). Flagged for you to rewrite: structural tells such as "it's not just
X, it's Y", rule-of-three triads and reflex engagement bait.

| check | what it measures |
| --- | --- |
| BURSTINESS | sentence-length variation |
| SPECIFICITY | numbers, names and concrete markers per 100 words |
| SLOP DENSITY | lexicon hits per 100 words |
| FINGERPRINT | invisible characters, em dashes, curly quotes per 1,000 |
| VOICE | first-person voice and structural tells |

## The fine print

- **These skills do not post to LinkedIn.** There is no official API for
  posting to a personal profile, and automating the site violates
  [LinkedIn's User Agreement](https://www.linkedin.com/legal/user-agreement).
  Every skill ends with a copy-ready block that you paste.
- **The five checks are local heuristics, not detector APIs.** They do not
  call GPTZero, Originality or Turnitin and cannot promise their verdicts.
- **Nothing is fabricated.** If a draft needs a number you have not given,
  it comes back with `{{your number}}` and a flag.

## License

MIT — see [LICENSE](LICENSE). A Turkish adaptation of Jake Schincariol's
[linkedin-agent-skill](https://github.com/Jakeschincariol/linkedin-agent-skill).
