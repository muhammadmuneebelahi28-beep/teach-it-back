# Teach It Back

**A Feynman-technique study companion that pokes holes in your explanations until you actually understand the thing you're studying.**

🔗 **Live app:** [ADD YOUR DEPLOYED URL HERE, e.g. https://teach-it-back.vercel.app](#)
📦 **Repo:** [ADD YOUR GITHUB REPO URL HERE](#)

---

## The problem, and who has it

There's a well-known study technique called the **Feynman Technique**: to check if you really understand something, try explaining it in plain language to someone else — usually you discover the gaps the moment you have to put it into words. The catch is that it requires a listener who is (a) available whenever you're studying, at 11pm before an exam, and (b) actually pushes back with sharp questions instead of nodding along.

Most students don't have that person on hand. What they have instead is a re-read of their notes, which *feels* like understanding but mostly measures familiarity, not comprehension. Flashcard apps and AI "explain this to me" tools make the same mistake in reverse — they hand you the explanation, so you never find out whether you could have produced it yourself.

**Teach It Back** flips the interaction: you write the explanation, in your own words, and an AI classmate — instructed to *never* explain the topic back to you — cross-examines the specific words and claims you used, the way a genuinely curious peer would. It tracks a clarity score and a running list of unresolved gaps per topic, so you can see, concretely, what you still don't know before the exam does.

Built for: students revising for exams, people self-teaching a new subject, or anyone using active recall who's tired of grading their own homework.

---

## Features

- **Topic notebook** — start a new topic, write your own explanation from scratch (no leading questions, no hints)
- **Socratic follow-up AI classmate** — asks 1–3 sharp, specific questions per turn about exactly what you wrote; never lectures, never gives away the answer
- **Live clarity gauge** — a hand-drawn-style meter that updates every turn with the AI's honest estimate of how well you currently understand the topic
- **Gap tracker ("sticky notes")** — a running list of the specific unresolved weak points in your explanation, so you know exactly what to go re-read
- **Clarity-over-time sparkline** — see whether you're actually converging on understanding across the conversation
- **Per-topic notebook history** — every topic you've explained is saved with its own thread and clarity trend, so you can revisit or resume
- **Bring-your-own AI key, your choice of provider** — Anthropic (Claude) or OpenAI, entered once and stored only in your own browser's `localStorage`; it is never written into the code or sent anywhere except straight to the provider you picked
- **Fully static, zero backend** — the whole app is HTML/CSS/JS; it runs from any static host and there is no server to go down or database to manage

---

## The AI feature

**What it does:** after you type an explanation of your topic, the AI reads it and responds *only* with pointed follow-up questions that reference the specific words and claims you used — it is explicitly instructed never to supply the explanation itself. Every reply also silently includes a machine-readable clarity score (0–100) and a short list of the concrete gaps still open, which the app parses out of the response and uses to drive the gauge, the sticky-note list, and the sparkline. The conversation continues, turn by turn, until your explanation is airtight or you decide to stop.

**The exact system prompt** (in `app.js`, `SYSTEM_PROMPT` constant):

```
You are playing the role of a sharp, relentlessly curious classmate who is helping a student practice the Feynman Technique: the student explains a concept in their own words, and you test whether they actually understand it or are just repeating memorized phrases.

Ground rules, follow all of them every single turn:
1. NEVER explain the concept yourself, correct them outright, or supply the missing information. Your only tool is asking questions.
2. Read their latest message closely and find the SPECIFIC weak spots: undefined jargon, circular definitions, hand-wavy phrases ("it just happens", "basically"), examples that don't obviously fit, logical leaps, or claims that sound rehearsed rather than understood.
3. Ask 1-3 short, pointed follow-up questions that quote or reference the specific words THEY used. Do not ask generic questions like "can you elaborate?" — ask "you said X causes Y, but what actually connects them?"
4. If a specific sub-point they made is genuinely clear and correct, say so in one short sentence, then move on to probing the next weak spot. Don't be needlessly harsh, but don't hand out unearned praise either.
5. Keep your tone like a curious peer, not a teacher grading an exam. Short sentences. No lecturing.
6. Every single reply, with no exceptions, must end with a fenced code block labeled json containing your current assessment of THIS topic based on everything explained so far in the conversation:
```json
{"clarity": <integer 0-100, your honest estimate of how well they currently understand the topic>, "gaps": [<up to 5 short strings, each a specific unresolved gap, phrased as a short label like "why voltage and current differ" — not full sentences>]}
```
7. The clarity score should move meaningfully turn to turn based on whether they resolved previous gaps or introduced new confusion. Do not default to round numbers out of laziness — use the full 0-100 range honestly.
8. If clarity reaches roughly 85+ and there are no serious remaining gaps, say so plainly and congratulate them briefly — you can stop probing at that point.
```

**Model/provider:** your choice, set in the in-app Settings panel. Defaults to `claude-sonnet-5` for Anthropic or `gpt-4o-mini` for OpenAI, but the model name field is free text so you can point it at any chat-completions-compatible model you have access to.

---

## Tools, services, and AI models used to build it

- **Built with:** Claude (Anthropic) as a coding assistant, hand-written HTML/CSS/JavaScript, no framework or build step
- **Fonts:** Google Fonts — Lora (headings), Inter (body), Caveat (handwritten AI marginalia)
- **AI at runtime:** Anthropic Claude API or OpenAI API — whichever the user selects and supplies a key for; no key is bundled with the app
- **Hosting:** deployed as a static site (see below) — works on Vercel, Netlify, GitHub Pages, or literally any static file host
- **Storage:** browser `localStorage` only — no database, no server

---

## Screenshots

> Replace these with real screenshots of your deployed app once it's live — the start screen, an active Socratic session with the clarity gauge and gap sticky-notes populated, and the settings panel are good ones to capture.

1. `screenshots/01-start-screen.png` — topic entry screen
2. `screenshots/02-session-in-progress.png` — an active back-and-forth with the AI classmate, gauge and gaps visible
3. `screenshots/03-settings.png` — the API key / provider settings modal
4. `screenshots/04-notebook.png` — the left-rail topic notebook with multiple saved topics

---

## How to run it

### Locally
No build step, no dependencies to install.

```bash
git clone <your-repo-url>
cd teach-it-back
python3 -m http.server 8080
# then open http://localhost:8080 in your browser
```

(Any static file server works — `npx serve`, VS Code's Live Server extension, etc. Opening `index.html` directly by double-clicking also works in most browsers, though a local server is more reliable for fonts.)

On first load, the Settings panel opens automatically — pick Anthropic or OpenAI, paste in your own API key, and you're ready to go. The key is stored only in that browser's `localStorage`.

### Deploying it live (Vercel, free tier)

```bash
npm install -g vercel   # one-time
cd teach-it-back
vercel --prod
```

Follow the prompts (link/create a project, accept defaults — it's a static site, no environment variables are required since the app has no server-side key). Vercel will give you a public URL; that's what goes in the "Live app" link at the top of this README.

Any other static host works identically — Netlify (`netlify deploy --prod`), GitHub Pages (enable Pages on the repo, serve from the root), Cloudflare Pages, etc. There is nothing to configure because the app never holds a secret of its own — each visitor supplies their own AI API key in-browser.

### Publishing the repo

```bash
git init
git add .
git commit -m "Teach It Back: Feynman-technique study companion"
git branch -M main
git remote add origin <your-empty-github-repo-url>
git push -u origin main
```

Make sure the GitHub repo is set to **Public** in its settings before submitting.

---

## Project structure

```
teach-it-back/
├── index.html      # markup: start screen, session screen, notebook rail, clarity rail, settings modal
├── style.css        # notebook-page visual design (all styling, no framework)
├── app.js           # state, localStorage persistence, AI calls, the system prompt, all UI rendering
└── README.md
```

## Privacy note

Your AI API key is entered once in the Settings panel and stored only in `localStorage` in your own browser. It is sent directly, over HTTPS, to the provider you chose (Anthropic or OpenAI) and nowhere else. It is never included in this repository, never logged, and clearing your browser storage removes it completely.
