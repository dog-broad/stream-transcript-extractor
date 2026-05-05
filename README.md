# Stream Transcript Extractor

> One-click bookmarklet to extract full transcripts from Microsoft Stream recordings hosted on SharePoint. Defeats lazy-loaded transcript panes. Search, filter by speaker, download in multiple formats.

[![Install](https://img.shields.io/badge/Install-GitHub%20Pages-00ff88?style=for-the-badge&logo=github)](https://dog-broad.github.io/stream-transcript-extractor/)
[![License: MIT](https://img.shields.io/badge/License-MIT-blue.svg?style=for-the-badge)](LICENSE)



## The Problem

Microsoft Stream (SharePoint) recordings with transcription enabled show a **Transcript pane** in the video player — but:

- **Download is permission-locked** — only the recording owner can export the transcript file
- **Virtualized list** — the pane renders only ~10-15 entries at a time; scrolling destroys old items, making `Ctrl+A` → `Ctrl+C` capture only a small slice
- **No public API** — there's no endpoint to fetch transcript data for recordings you can view but don't own



## Install

### One-click (drag & drop)

**[→ Open the installer page](https://dog-broad.github.io/stream-transcript-extractor/)** and drag the green button to your bookmarks bar.

### Manual Install

1. Show your Bookmarks Bar (`Ctrl+Shift+B`)
2. Right-click the Bookmarks Bar → **"Add page..."** or **"Add bookmark"**
3. Set name: `Extract Transcript`
4. Set URL to:

```
javascript:void((async()=>{try{var r=await fetch('https://gist.githubusercontent.com/dog-broad/6f9c99d3e3d11a5af9ba198bab9e2e6d/raw/transcript-extractor.js?v='+Date.now());if(!r.ok)throw new Error('HTTP '+r.status);var t=await r.text();eval(t);}catch(e){alert('Failed to load transcript extractor: '+e.message);}})())
```

5. Save. Done.



## Usage

1. Open the Microsoft Stream recording in your browser
2. Click the **"Transcript"** button on the video player to open the transcript pane
3. Wait 2-3 seconds for entries to appear
4. Click **"Extract Transcript"** in your bookmarks bar
5. A new tab opens with the complete transcript — searchable, filterable, and downloadable



## Features

| | Feature | Description |
|--|---------|-------------|
| 🔄 | Defeats lazy loading | Auto-scrolls the virtualized list across 3 passes to force all entries to render |
| 🔍 | Full-text search | Instant search with highlighted matches. Overrides Ctrl+F |
| 👥 | Speaker filter | Toggle speakers on/off with filter chips |
| 📋 | Multiple export formats | Download as .txt, .json, .srt, or .csv |
| 🔒 | 100% client-side | No data leaves your browser. No server. No tracking |
| 🔁 | Auto-updates | Fetches the latest script on every click — no reinstall needed |



## Troubleshooting

| Issue | Solution |
|-------|----------|
| Nothing happens on click | Chrome strips `javascript:` when pasting into the address bar. Must be saved as a **bookmark**. Edit it and confirm the URL starts with `javascript:` |
| "Transcript pane not found" | Open the Transcript pane first (click the Transcript button on the player). Wait for entries to appear before clicking the bookmarklet |
| Only partial entries | For very long recordings, click the bookmarklet a second time. Each run does 3 scroll passes |
| New tab blocked | Allow popups for the SharePoint domain. Look for the blocked popup icon in the address bar |
| Script fails to load | VPN/proxy may block GitHub Gist. Copy the [raw script](https://gist.githubusercontent.com/dog-broad/6f9c99d3e3d11a5af9ba198bab9e2e6d/raw/transcript-extractor.js) and paste into the browser console (F12 → Console → Enter) |

For FAQs and detailed troubleshooting, see the [installer page](https://dog-broad.github.io/stream-transcript-extractor/).



## Contributing

Issues welcome! If you hit a problem, please [open an issue](https://github.com/dog-broad/stream-transcript-extractor/issues) with:
- Browser + version
- Any console errors (F12 → Console)
- Screenshot of the transcript pane (optional)

The main extraction script lives in a [separate Gist](https://gist.github.com/dog-broad/6f9c99d3e3d11a5af9ba198bab9e2e6d) — fork and suggest changes there, or open a PR on this repo for the installer page.



## License

MIT — use freely, modify freely, distribute freely.

<p align="center">
  <a href="https://dog-broad.github.io/stream-transcript-extractor/">
    <img src="https://img.shields.io/badge/Install%20Now-00ff88?style=for-the-badge&logo=googlechrome&logoColor=black" alt="Install Now">
  </a>
</p>
