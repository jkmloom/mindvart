# 🧠 Mindvart

### *Free learning, everywhere.*

**Mindvart is a free, open-source search engine for study materials.** No paywalls. No accounts. No API keys. No payment. Ever. Just search for something you want to learn, and get real results from real open databases — research papers, books, articles, wiki pages, and YouTube videos — instantly.

It's a single `.html` file. That's it. Open it in any browser and it works.

---

## Why does this exist?

Studying online is broken. You search for something, and you get ads, sponsored results, paywalled journal articles, and content farms designed to keep you clicking — not learning. Even the tools meant to help you study are built around keeping you distracted.

Mindvart was built with one belief: **knowledge should be free and accessible to everyone.** Not just people with university library access. Not just people who can afford subscriptions. Everyone.

So we built the simplest possible thing — a search engine that only shows you free study material, and gets out of your way.

---

## ✨ What it does

- 🔍 **Searches 10 real open databases in parallel** — results start appearing in seconds
- 📂 **Categorizes results** into Research, Articles, Wiki, Books, Videos, Courses, News, and Tools
- ▶️ **Real YouTube results** with thumbnails, channel names, view counts, and duration — no API key needed
- ✅ Every single result is **free to access** — no exceptions
- 🚫 **Zero paywalls, zero ads, zero sponsored content**
- 💻 **Works completely offline-ready** — just one `.html` file, no build step, no dependencies, no server

---

## 📚 Sources it searches

| Source | What you get | Type |
|---|---|---|
| **Wikipedia** | Encyclopedia articles on almost everything | Articles / Wiki |
| **Wikiversity** | Study resources and learning materials | Courses |
| **Wikibooks** | Free, open textbooks on hundreds of subjects | Books |
| **arXiv** | Preprint research papers (physics, math, CS, biology, economics) | Research |
| **Semantic Scholar** | Open-access academic papers with abstracts | Research |
| **CrossRef** | Journal articles with freely available full text | Research |
| **PubMed Central** | Biomedical and life sciences research papers | Research |
| **Open Library** | Millions of digitized books, freely available | Books |
| **Internet Archive** | Digitized texts, historical documents, books | Books |
| **YouTube** *(via Invidious)* | Educational lectures, tutorials, documentaries | Videos |

Plus — the "Also explore" section links out to **Khan Academy, MIT OpenCourseWare, Coursera (free audit), PhET Simulations, Google Scholar, Project Gutenberg,** and **OpenStax** for even more free material.

---

## 🚀 Getting started (it takes 30 seconds)

No npm. No pip. No Docker. No config files. Just a file.

**Option 1 — Just use it:**
1. Download `mindvart.html`
2. Open it in your browser (double-click, or drag it into Chrome/Firefox/Edge)
3. Search for anything

**Option 2 — Clone the repo:**
```bash
git clone https://github.com/yourusername/mindvart.git
cd mindvart
# Open mindvart.html in your browser — done!
```

**Option 3 — Use VS Code:**
```bash
git clone https://github.com/yourusername/mindvart.git
code mindvart
# Right-click mindvart.html → "Open with Live Server"
# Or just open the file directly in your browser
```

No `npm install`. No environment variables. No `.env` file. Nothing to configure. It just works.

---

## 🛠 How the YouTube search works (no API key!)

YouTube's official API requires a paid Google Cloud key. We don't use it.

Instead, Mindvart uses **[Invidious](https://invidious.io)** — a free, open-source, privacy-respecting YouTube frontend that exposes a public search API with zero authentication. We query it for educational content, get real video results with thumbnails and metadata, and all links go directly to `youtube.com`.

We ping multiple public Invidious instances as fallbacks, so if one is down, the next one tries automatically:

```
iv.datura.network
invidious.privacyredirect.com
invidious.nerdvpn.de
```

You can find more public instances at [api.invidious.io](https://api.invidious.io) and swap them in if needed.

---

## 🧩 Project structure

```
mindvart/
└── mindvart.html     ← the entire project. HTML + CSS + JS, all in one file.
```

Yes, really. The whole thing is one file. HTML for structure, CSS for styling, and vanilla JavaScript for all the search logic. No frameworks, no bundlers, no dependencies.

---

## 🍴 Fork it. Break it. Make it yours.

This project is open source because we want people to build on it. Here are some ideas to get you thinking:

### Easy changes (great first contributions)
- **Add a new search source** — find any open API and add a new `searchXYZ()` function following the same pattern as the existing ones
- **Add a new category tab** — add an entry to the `TABS` array and a new badge style in the CSS
- **Change the color scheme** — all colors are CSS variables at the top of the `<style>` block, swap them freely
- **Add more Invidious fallback instances** — just add more URLs to the `instances` array in `searchYouTube()`
- **Change the suggestion chips** — edit the `.chip` elements in the HTML to match your subject area

### Medium changes
- **Subject-specific version** — fork it and hardcode it for one topic (e.g. a medical study tool that only searches PubMed and medical textbooks, or a coding tool that searches Stack Overflow and GitHub)
- **Add a dark mode toggle** — the CSS variable system makes this very doable, just swap the variable values
- **Add local history** — use `localStorage` to remember recent searches
- **Filter by year** — add a date range filter for research papers (arXiv and CrossRef both support this in their APIs)
- **Add more languages** — Wikipedia, Wikiversity, and Wikibooks all support dozens of languages, just change `en.wikipedia.org` to `fr.wikipedia.org` etc.

### Bigger ideas
- **Browser extension** — wrap this as a Chrome/Firefox extension that replaces your new tab page
- **Self-hosted version with a backend** — add a lightweight Node/Python server to proxy requests and cache results
- **Subject-specific fork network** — a "Mindvart for medicine", a "Mindvart for law", a "Mindvart for coding", etc.
- **Offline mode** — cache results with a Service Worker so you can study without internet
- **Reading mode** — add a built-in article reader that fetches and renders Wikipedia/Open Library content inline

---

## ✏️ How to add a new search source

Every search source follows the same simple pattern. Here's the template:

```javascript
async function searchMyNewSource(q) {
  // q is the search query string

  const data = await fj(`https://api.mynewsource.org/search?q=${enc(q)}&limit=5`);
  // fj() is a helper that does fetch() + .json() + error handling

  return (data?.results || []).map(item => ({
    title: item.title,                       // string — the result title
    url: item.url,                           // string — where to open it
    description: item.summary.slice(0, 200), // string — short description
    type: 'article',                         // one of: research, article, book, video, course, news, forum, wiki, tool
    source: 'My New Source',                 // string — display name
    tags: ['free', 'open access'],           // array of short label strings
  }));
}
```

Then add it to the `sources` array in `doSearch()`:

```javascript
{ key: 'mynewsource', label: 'My New Source', fn: () => searchMyNewSource(q) },
```

And add it to `initProgress()` so it shows in the loading bar:

```javascript
['mynewsource', 'My New Source'],
```

That's it! The rest — rendering cards, tabs, filtering, counts — is all handled automatically.

---

## 🌍 Free APIs used

All of these are **free, open, require no authentication**, and allow browser-side requests (CORS enabled):

| API | Docs |
|---|---|
| Wikipedia API | [mediawiki.org/wiki/API](https://www.mediawiki.org/wiki/API:Main_page) |
| Wikiversity API | Same MediaWiki API, different domain |
| Wikibooks API | Same MediaWiki API, different domain |
| arXiv API *(via allorigins.win proxy)* | [arxiv.org/help/api](https://arxiv.org/help/api/) |
| Open Library API | [openlibrary.org/developers/api](https://openlibrary.org/developers/api) |
| Semantic Scholar API | [api.semanticscholar.org](https://api.semanticscholar.org/graph/v1) |
| CrossRef API | [api.crossref.org](https://api.crossref.org) |
| PubMed / NCBI E-utilities | [ncbi.nlm.nih.gov/books/NBK25500](https://www.ncbi.nlm.nih.gov/books/NBK25500/) |
| Internet Archive API | [archive.org/advancedsearch.php](https://archive.org/advancedsearch.php) |
| Invidious API (YouTube) | [docs.invidious.io/api](https://docs.invidious.io/api/) |

> **Note on arXiv:** arXiv blocks direct browser requests (no CORS headers), so we route through [allorigins.win](https://allorigins.win) — a free, open-source CORS proxy. You can self-host allorigins if you want full independence.

---

## 🤝 Contributing

Contributions are very welcome! Whether it's a new data source, a bug fix, a design improvement, or a whole new feature — open a PR.

A few things we care about:
- **Keep it free.** No source that requires payment or a credit card.
- **Keep it simple.** The single-file approach is a feature, not a limitation — it means anyone can download, inspect, and understand the whole thing.
- **Keep it fast.** Searches should start returning results quickly. If your new source is slow, consider adding a timeout.
- **No tracking.** No analytics, no cookies, no fingerprinting.

---

## 📄 License

MIT — do whatever you want with it. Fork it, remix it, deploy it, build a business on it. Just keep the knowledge free.

---

## 💬 A note on why this matters

Education is the one thing in the world that compounds. Every person who learns something can teach someone else. Every paper that gets read can inspire a new idea. Every book that gets opened can change a life.

The internet was supposed to make knowledge free and universal. In a lot of ways, it has — Wikipedia, arXiv, Open Library, YouTube, Khan Academy — there is more free, high-quality educational content available today than at any point in human history.

But it's scattered. It's buried under ads and SEO content and paywalls. It's hard to find, especially if you don't know exactly where to look.

Mindvart is just a small attempt to surface what's already there. To make the free stuff easier to find than the paid stuff. To build a search engine that genuinely has your learning as its only goal.

If this helps even one person study something they couldn't have found otherwise, it was worth building.

**Now go learn something. 📖**

---

*Built with love and a single HTML file. No VC funding, no ads, no agenda — just the belief that knowledge should be free.*