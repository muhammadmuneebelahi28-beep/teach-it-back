# Teach It Back

**A Feynman-technique study companion that pokes holes in your explanations until you actually understand the thing you're studying.**

🔗 **Live app:** [https://muhammadmuneebelahi28-beep.github.io/teach-it-back/](https://muhammadmuneebelahi28-beep.github.io/teach-it-back/)
📦 **Repo:** [https://github.com/muhammadmuneebelahi28-beep/teach-it-back](https://github.com/muhammadmuneebelahi28-beep/teach-it-back)

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
- **Bring-your-own AI key, your choice of provider** — Groq (free, no card required), Google Gemini, Anthropic (Claude), or OpenAI — entered once and stored only in your own browser's `localStorage`; it is never written into the code or sent anywhere except straight to the provider you picked
- **Fully static, zero backend** — the whole app is HTML/CSS/JS; it runs from any static host and there is no server to go down or database to manage

---

## The AI feature

**What it does:** after you type an explanation of your topic, the AI reads it and responds *only* with pointed follow-up questions that reference the specific words and claims you used — it is explicitly instructed never to supply the explanation itself. Every reply also silently includes a machine-readable clarity score (0–100) and a short list of the concrete gaps still open, which the app parses out of the response and uses to drive the gauge, the sticky-note list, and the sparkline. The conversation continues, turn by turn, until your explanation is airtight or you decide to stop.

**The exact system prompt** (in `index.html`, `SYSTEM_PROMPT` constant):

```
You are playing the role of a sharp, relentlessly curious classmate who is helping a student practice the Feynman Technique: the student explains a concept in their own words, and you test whether they actually understand it or are just repeating memorized phrases.

Ground rules, follow all of them every single turn:
1. NEVER explain the concept yourself, correct them outright, or supply the missing information. Your only tool is asking questions.
2. Read their latest message closely and find the SPECIFIC weak spots: undefined jargon, circular definitions, hand-wavy phrases ("it just happens", "basically"), examples that don't obviously fit, logical leaps, or claims that sound rehearsed rather than understood.
3. Ask 1-3 short, pointed follow-up questions that quote or reference the specific words THEY used. Do not ask generic questions like "can you elaborate?" — ask "you said X causes Y, but what actually connects them?"
4. If a specific sub-point they made is genuinely clear and correct, say so in one short sentence, then move on to probing the next weak spot. Don't be needlessly harsh, but don't hand out unearned praise either.
5. Keep your tone like a curious peer, not a teacher grading an exam. Short sentences. No lecturing.
6. Every single reply, with no exceptions, must end with a fenced code block labeled json containing your current assessment of THIS topic based on everything explained so far in the conversation. The JSON object must have two fields: "clarity" (an integer 0-100, your honest estimate of how well they currently understand the topic) and "gaps" (an array of up to 5 short strings, each a specific unresolved gap, phrased as a short label like "why voltage and current differ" — not full sentences).
7. The clarity score should move meaningfully turn to turn based on whether they resolved previous gaps or introduced new confusion. Do not default to round numbers out of laziness — use the full 0-100 range honestly.
8. If clarity reaches roughly 85+ and there are no serious remaining gaps, say so plainly and congratulate them briefly — you can stop probing at that point.
```

**Model/provider:** your choice, set in the in-app Settings panel. Three providers are supported out of the box:
- **Groq** (recommended — genuinely free, no credit card, no regional restrictions) — default model `llama-3.3-70b-versatile`
- **Google Gemini** — has a free tier, though it's not available without billing in every region
- **Anthropic (Claude)** or **OpenAI** — for anyone who already has a paid key

The model name field is free text, so you can point it at any chat-completions-compatible model you have access to.

---

## Tools, services, and AI models used to build it

- **Built with:** Claude (Anthropic) as a coding assistant, hand-written HTML/CSS/JavaScript, no framework or build step
- **Fonts:** Google Fonts — Lora (headings), Inter (body), Caveat (handwritten AI marginalia)
- **AI at runtime:** Anthropic Claude API or OpenAI API — whichever the user selects and supplies a key for; no key is bundled with the app
- **Hosting:** deployed as a static site (see below) — works on Vercel, Netlify, GitHub Pages, or literally any static file host
- **Storage:** browser `localStorage` only — no database, no server

---

## Screenshots

**The AI classmate in action** — clarity gauge and gap tracker updating live:
![AI classmate session](screeen%20tech.png)

**Start screen** — with a saved topic in the notebook rail:
![Start screen](teach%20it%20back.png)

**Settings panel** — choosing a free AI provider:
![Settings panel](empty%20api%20key%20ss.png)

---

## How to run it

### Locally
No build step, no dependencies to install — it's a single HTML file.

```bash
git clone https://github.com/muhammadmuneebelahi28-beep/teach-it-back.git
cd teach-it-back
```

Then just double-click `index.html` to open it in your browser (a local server isn't required since everything, including styling and scripts, is bundled into that one file).

On first load, the Settings panel opens automatically — pick a provider (**Groq is recommended**: free, no credit card required, no regional restrictions), paste in your own API key, and you're ready to go. The key is stored only in that browser's `localStorage`.

Getting a free Groq key: sign in at [console.groq.com](https://console.groq.com) with Google or GitHub, go to **API Keys**, click **Create API Key**.

### Deploying it live (this project uses GitHub Pages, free)

1. Push the repo to GitHub (public).
2. In the repo, go to **Settings → Pages**.
3. Under **Build and deployment → Source**, choose **Deploy from a branch**.
4. Set **Branch** to `main` and folder to `/ (root)`, then **Save**.
5. GitHub publishes the site at `https://<your-username>.github.io/<repo-name>/` within a minute or two.

No environment variables or server configuration are needed, because the app never holds a secret of its own — each visitor supplies their own AI API key directly in the browser.

Any other static host works identically too — Vercel (`vercel --prod`), Netlify (`netlify deploy --prod`), Cloudflare Pages, etc.

---

## Project structure

```
teach-it-back/
├── index.html      # everything: markup, styling, and app logic (state, localStorage,
│                    # the AI calls for Groq/Gemini/Anthropic/OpenAI, the system prompt,
│                    # and all UI rendering) bundled into a single self-contained file
├── README.md
└── .gitignore
```

The app was originally built as three separate files (`index.html`, `style.css`, `app.js`) and was later bundled into one file for simpler, more reliable static hosting — there is no build step either way.

## Privacy note

Your AI API key is entered once in the Settings panel and stored only in `localStorage` in your own browser. It is sent directly, over HTTPS, to the provider you chose (Anthropic or OpenAI) and nowhere else. It is never included in this repository, never logged, and clearing your browser storage removes it completely.
