# 📜 Stream Transcript Extractor

> One-click bookmarklet to extract full transcripts from Microsoft Stream recordings hosted on SharePoint. Defeats lazy-loaded transcript panes. Search, filter by speaker, and download in multiple formats.

[![GitHub Pages](https://img.shields.io/badge/Install-GitHub%20Pages-00ff88?style=for-the-badge&logo=github)](https://dog-broad.github.io/stream-transcript-extractor/)
[![License: MIT](https://img.shields.io/badge/License-MIT-blue.svg?style=for-the-badge)](LICENSE)



## 🎯 The Problem

Microsoft Stream (on SharePoint) recordings with transcription enabled have a **Transcript pane** in the video player. However:

1. **Download is permission-locked** — Only the recording owner can download the transcript file. If they haven't shared download access, you see:  
   > *"You don't have permission to download the transcript. Contact [Owner] to request access."*

2. **Lazy-loaded / Virtualized list** — The transcript pane renders only ~10-15 entries visible in the viewport at any given time. As you scroll, old entries are **destroyed** and new ones are rendered. This means:
   - You can never `Ctrl+A` → `Ctrl+C` to copy the full transcript
   - Browser "Select All" only grabs the currently rendered slice
   - Even scrolling manually and copying in chunks is tedious and error-prone for long recordings

3. **No API access** — There's no public endpoint to fetch transcript data for recordings you can view but don't own.

**This is a massive pain point** for teams that rely on KT (Knowledge Transfer) recordings and need searchable transcript text for notes, documentation, onboarding, and reference.



## ✅ The Solution

A **browser bookmarklet** that:

1. **Auto-scrolls** the transcript pane programmatically — forcing the virtualized list to render every entry
2. **Extracts** speaker names, timestamps, and text from the DOM on each scroll step
3. **Opens a rich results page** in a new tab with the complete transcript

No extensions to install. No dependencies. No data sent anywhere. Runs 100% in your browser.



## 🚀 Install

### 👉 [**Click here to install →**](https://dog-broad.github.io/stream-transcript-extractor/)

Visit the GitHub Pages site and **drag the green button** to your Bookmarks Bar.

### Alternative: Manual Install

1. Show your Bookmarks Bar (`Ctrl+Shift+B`)
2. Right-click the Bookmarks Bar → **"Add page..."**
3. Set name: `📜 Extract Transcript`
4. Set URL:

```
javascript:void((async()=>{const n=document.createElement('script');n.src='https://gist.githubusercontent.com/dog-broad/6f9c99d3e3d11a5af9ba198bab9e2e6d/raw/transcript-extractor.js?v='+Date.now();n.onload=()=>console.log('Transcript extractor loaded');n.onerror=()=>alert('Failed to load extractor script. Check network/URL.');document.head.appendChild(n);})())
```

5. Save. Done.



## 📖 Usage

1. Open the Microsoft Stream recording in your browser
2. Click the **"Transcript"** button on the video player to open the transcript pane
3. Wait 2-3 seconds for initial entries to load
4. Click **"📜 Extract Transcript"** in your bookmarks bar
5. Wait for extraction to complete (progress overlay shown)
6. A **new tab** opens with the full transcript — ready to search, filter, and download



## ✨ Features

### Extraction
| Feature | Description |
|---------|-------------|
| 🔄 Defeats lazy loading | Auto-scrolls the virtualized list to force all entries to render |
| 🔁 Multi-pass extraction | 3 scroll passes to handle re-virtualization of previously rendered items |
| 👥 Speaker detection | Extracts speaker names and associates them with their entries |
| ⏱️ Timestamps | Preserves all timestamps from the transcript |

### Results Page
| Feature | Description |
|---------|-------------|
| 🔍 Full-text search | Instant search with highlighted matches. Overrides Ctrl+F |
| 👥 Speaker filter | Toggle speakers on/off with filter chips |
| 🎨 Color-coded speakers | Each speaker gets a unique color for easy visual scanning |
| 📋 Copy All | One-click copy of entire formatted transcript to clipboard |
| 📄 Download .txt | Plain text format with speaker headers and timestamps |
| 🗂️ Download .json | Structured JSON with metadata and entries array |
| 🎬 Download .srt | Subtitle format compatible with video players |
| 📊 Download .csv | Spreadsheet format with columns: Index, Timestamp, Speaker, Text |
| 📊 Stats bar | Shows total entries, unique speakers count, and recording duration |

### Architecture
| Feature | Description |
|---------|-------------|
| ⚡ No install needed | Bookmarklet runs in any modern browser — Chrome, Edge, Firefox |
| 🔒 100% client-side | No data leaves your browser. No server calls. No tracking |
| 🔁 Auto-updates | Bookmarklet fetches the latest script from Gist on every click |
| 🪶 Tiny footprint | Bookmarklet is just a loader (~200 bytes). Heavy logic lives in Gist |



## ⚙️ How It Works

### Architecture

```
┌─────────────────────────────────────────────────────────────────┐
│  Bookmarklet (in your Bookmarks Bar)                            │
│  → Tiny loader, fetches main script on click                    │
└──────────────────────────────┬──────────────────────────────────┘
                               │ fetches at runtime
                               ▼
┌─────────────────────────────────────────────────────────────────┐
│  GitHub Gist (transcript-extractor.js)                          │
│  → Main extraction logic. Update here = everyone gets latest    │
└──────────────────────────────┬──────────────────────────────────┘
                               │ executes on Stream page
                               ▼
┌─────────────────────────────────────────────────────────────────┐
│  Extraction Process                                             │
│  1. Find scrollable transcript container via data-testid        │
│  2. Scroll top → bottom in 280px increments (160ms delay)       │
│  3. On each step, read DOM for [data-list-index] cells          │
│  4. Extract speaker, timestamp, text from each cell             │
│  5. Store in Map (deduplicates by index)                        │
│  6. Repeat for 3 passes to fill virtualization gaps             │
└──────────────────────────────┬──────────────────────────────────┘
                               │ opens
                               ▼
┌─────────────────────────────────────────────────────────────────┐
│  Results Page (new tab — self-contained HTML blob)              │
│  → Formatted transcript with search, filter, export             │
│  → All data embedded in the page. No external dependencies      │
└─────────────────────────────────────────────────────────────────┘
```

### Technical Details

**Why lazy loading is the problem:**

Microsoft Stream's transcript pane uses a **virtualized list** (similar to `react-window` or Office UI Fabric's `List` component). It renders only the items that fit in the visible scroll viewport, plus a small buffer. As you scroll:
- New items are rendered into the DOM
- Items scrolled past are **removed from the DOM**
- This means at any point, only ~10-20 of potentially hundreds of entries exist in the page

**How the script defeats it:**

```javascript
// Simplified logic:
while (canScrollFurther) {
    collectVisibleEntries();         // Read DOM for currently rendered items
    scrollContainer.scrollTop += 280; // Scroll down to trigger new renders
    await sleep(160);                 // Wait for virtualization to catch up
}
```

The multi-pass approach handles a subtlety: since scrolling down causes top items to be destroyed, a single pass from top-to-bottom won't capture everything if the list re-virtualizes aggressively. Three passes ensure complete coverage.

**DOM selectors used:**

| Selector | Purpose |
|----------|---------|
| `[data-testid="scroll-to-target-targeted-focus-zone"]` | Scrollable transcript container |
| `[data-list-index]` | Individual transcript entry cells |
| `[class*="itemDisplayName"]` | Speaker name element |
| `[id^="Header-timestamp-"]` | Timestamp element |
| `[id^="sub-entry-"]` | Transcript text element |
| `[class*="eventSpeakerName"]` | Speaker name (alternate, for system events) |



## 🛠️ Troubleshooting

| Issue | Solution |
|-------|----------|
| Nothing happens on click | Chrome strips `javascript:` prefix when pasting into address bar. Must be saved as a **bookmark**, not typed in URL bar. Edit bookmark and confirm it starts with `javascript:` |
| "Transcript pane not found" alert | Open the Transcript pane first (click Transcript button on player). Wait for entries to appear before clicking bookmarklet |
| Only partial entries | For very long recordings, try clicking the bookmarklet a second time. Each run does 3 passes but extremely long transcripts may need another go |
| New tab blocked | Browser popup blocker may interfere. Allow popups for the Stream/SharePoint domain. Look for the blocked popup icon in address bar |
| Script fails to load | Network/VPN may block GitHub Gist. Workaround: Copy the [raw script](https://gist.githubusercontent.com/dog-broad/6f9c99d3e3d11a5af9ba198bab9e2e6d/raw/transcript-extractor.js) and paste directly into browser console (F12 → Console → Paste → Enter) |
| Works in Chrome but not Firefox | Ensure bookmark URL starts with `javascript:`. Firefox may also require allowing the popup in the notification bar |
| Entries show wrong speaker | The script inherits speaker from the nearest header. If Stream groups multiple entries under one speaker header without repeating the name, this is expected behavior |



## 🤔 FAQ

### Is this safe to use?

Yes. The bookmarklet and script run **entirely in your browser**. No data is sent to any server. No cookies are read. No credentials are accessed. The script only reads the transcript pane's DOM elements (speaker names, timestamps, text) and constructs a local HTML page.

### Will this work on any Microsoft Stream recording?

It works on Microsoft Stream recordings hosted on SharePoint (the modern Stream experience) that have transcription enabled. The URL typically looks like:
```
https://[your-org].sharepoint.com/personal/[user]/_layouts/15/stream.aspx?id=...
```

It will **not** work on:
- Classic Microsoft Stream (stream.microsoft.com) — different DOM structure
- Recordings without transcription enabled
- Pages where the transcript hasn't loaded at all

### Does the recording owner get notified?

No. The bookmarklet reads DOM elements that are already rendered in your browser. It doesn't make any API calls or access anything beyond what you can already see on screen.

### Can I use this if the transcript pane shows entries but download is disabled?

Yes — that's exactly the use case this solves. If you can see entries scrolling in the transcript pane, this tool can extract them regardless of download permissions.

### How long does extraction take?

Depends on transcript length:
| Recording Length | Approximate Extraction Time |
|-----------------|---------------------------|
| 5-15 minutes | ~10-20 seconds |
| 30-60 minutes | ~30-60 seconds |
| 1-2 hours | ~60-120 seconds |

### Can I customize the scroll speed?

Yes — edit the Gist directly. Key variables:
- `scrollStep = 280` — pixels per scroll increment (lower = more thorough, slower)
- `scrollDelay = 160` — milliseconds between scrolls (higher = more reliable on slow connections)
- `totalPasses = 3` — number of complete scroll passes (more = higher coverage)



## 📁 Repository Structure

```
stream-transcript-extractor/
├── index.html                 ← GitHub Pages installer page
├── README.md                  ← This file
└── (Gist - external)
    └── transcript-extractor.js  ← Main extraction script
```

The main script lives in a [separate Gist](https://gist.github.com/dog-broad/6f9c99d3e3d11a5af9ba198bab9e2e6d) so it can be updated independently without redeploying the GitHub Pages site. The bookmarklet fetches the script fresh (with cache-busting `?v=timestamp`) on every invocation.



## 🤝 Contributing

Contributions welcome! If you encounter issues with:
- New DOM structures (Microsoft updates Stream's UI periodically)
- Different transcript pane layouts
- Edge cases with speaker detection

Please [open an issue](https://github.com/dog-broad/stream-transcript-extractor/issues) with:
1. Browser + version
2. Screenshot of the transcript pane (optional)
3. Any console errors (F12 → Console)

To contribute code, edit the [Gist](https://gist.github.com/dog-broad/6f9c99d3e3d11a5af9ba198bab9e2e6d) (fork and suggest changes) or open a PR on this repo for the installer page.



## 📄 License

MIT License. Use freely, modify freely, distribute freely.



## ⭐ Star This Repo

If this saved you time, consider starring the repo — it helps others discover the tool when searching for Microsoft Stream transcript solutions.



<p align="center">
  <a href="https://dog-broad.github.io/stream-transcript-extractor/">
    <img src="https://img.shields.io/badge/Install%20Now-00ff88?style=for-the-badge&logo=googlechrome&logoColor=black" alt="Install Now">
  </a>
</p>
