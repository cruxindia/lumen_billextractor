# Lumen — Document Intelligence

Turns billing workbooks into an auditable database. **Everything runs in your browser.
No document is ever uploaded, and the page cannot send data anywhere.**

Live: `https://<org>.github.io/<repo>/`

---

## For the team

1. Open the link. Nothing to install.
2. Drop a folder of Excel files, or a ZIP, on the **Ingest** page.
3. **Ledger** shows every document with its proof strip — 13 segments, one per check.
4. **Review** lists what needs a look, ranked by value at risk.
5. **Report** builds the columns you want; export to Excel or CSV.

Every document you ingest stays on your own machine. Two people opening this link do **not**
see each other's data — to share, use **Save** in the sidebar and send the workspace file.

**On a shared computer, press Clear when you finish.**

RAR archives cannot be opened by any browser. Extract first, then use **Choose folder**.

---

## Deploying

```bash
git init && git add . && git commit -m "Lumen"
git branch -M main
git remote add origin git@github.com:<org>/<repo>.git
git push -u origin main
```

Then **Settings → Pages → Source: Deploy from a branch → main / (root)**.
Live in about a minute.

## What's in here

| File | Purpose |
|---|---|
| `index.html` | The whole application, self-contained (~1.1 MB) |
| `manifest.webmanifest` | Makes it installable as an app |
| `sw.js` | Service worker — works offline after the first visit |
| `icon.svg` | App icon (the proof strip) |
| `.nojekyll` | Stops GitHub's Jekyll from rewriting paths |

## Why the libraries are bundled, not loaded from a CDN

SheetJS and JSZip are included in `index.html` rather than fetched at runtime. This means:

- **It works offline**, from the first load, with no network at all
- **No CDN can break it** — some corporate networks block them
- **No supply-chain risk** — a compromised CDN cannot inject code into a page handling financial data
- **The Content-Security-Policy can forbid all outbound connections**, which makes "your data never leaves this machine" a property the browser enforces, not a promise in a README

## Security posture

The CSP in `index.html` sets `default-src 'none'` and permits `connect-src` only to
`localhost:8420` (the optional local engine). The page has no way to transmit a document
anywhere. You can verify this in DevTools → Network: after load, there is no traffic.

Data is stored in your browser's IndexedDB, scoped to this origin and your profile only.

**A public repository publishes the code, not any data.** No client names, GSTINs, rate cards
or documents are in this repository — the validation vocabulary is generic.

## Known limits

- Excel (`.xlsx .xlsm .xls .xlsb`), CSV, TSV and ZIP. **No PDF or OCR yet.**
- RAR and 7z must be extracted first.
- The optional local engine cannot be reached from an `https://` page (browsers block
  plain-http calls from secure pages). Use the offline copy if you run the engine.
- Entity resolution is not implemented, so STATE and ZONE columns are blank.

## Licences

Lumen is yours. Bundled: [SheetJS](https://sheetjs.com) 0.18.5 (Apache-2.0),
[JSZip](https://stuk.github.io/jszip/) 3.10.1 (MIT).
