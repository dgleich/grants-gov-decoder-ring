# Grants.Gov PDF Secret Decoder Ring

A single-file, browser-only viewer for Federal grant PDFs that ordinary
PDF readers refuse to open.

→ **Live page:** _(GitHub Pages URL once enabled)_

## What's the problem?

The forms you download from [Grants.Gov](https://www.grants.gov/) — R&R Budget,
R&R Senior/Key Person Profile, PHS Human Subjects, etc. — are **XFA** (XML
Forms Architecture) PDFs. XFA is an Adobe technology from 2003 that Adobe
itself killed in 2020 due to security issues, but the federal grant system
still uses it.

The result: most PDF readers (Apple Preview, Chrome, Firefox, Edge, the entire
ecosystem) show a blank page or the message _"Please install Adobe Reader 8 or
higher"_. The data is in the file — your reader just can't render it.

## What does this tool do?

Drop a Grants.Gov PDF on the page. It will:

- **Extract every field value** from the form's XFA datasets stream
  (budget tables, key personnel, organization info, dates, totals)
- **Pull out every embedded PDF attachment** — biosketches, budget
  justifications, current-and-pending support documents — and preview
  each one inline using the browser's native PDF viewer
- **Show specialized layouts** for the common SF-424 R&R forms
  (RR_Budget_3_0, RR_KeyPerson_3_0)
- **Fall back to a generic field-tree view** for any other XFA form

No Adobe Acrobat. No installation. No server.

## Privacy

Everything happens in your browser. The HTML is one file you can read, the JS
libraries load from a CDN (cdnjs), and your PDF never leaves the tab. Open
your browser's Network panel while you use it — you'll see nothing go out.

## How it works

The tool uses three open-source JavaScript libraries:

| library | purpose |
|---|---|
| [pdf-lib](https://pdf-lib.js.org/) | walks the PDF object graph to extract the XFA `datasets` stream and the `/Names/EmbeddedFiles` name tree |
| [pako](https://github.com/nodeca/pako) | inflates the FlateDecoded XFA streams and embedded files |
| [PDF.js](https://mozilla.github.io/pdf.js/) | document metadata |

The `datasets` XML is parsed with the browser's built-in DOMParser.
Embedded PDFs are wrapped in `Blob` URLs and rendered with the browser's
own PDF viewer in an `<iframe>`.

Total payload: ~725 KB gzipped (~2.1 MB raw).

## Supported forms

| form id | name | layout |
|---|---|---|
| `RR_Budget_3_0` | R&R Budget | summary bar + Key Persons / Other Direct / Indirect tables + inline budget justification |
| `RR_KeyPerson_3_0` | R&R Senior/Key Person Profile (Expanded) | profile cards with biosketch + current-and-pending PDFs inline |
| _any other XFA form_ | — | generic field-tree view |

## Local development

It's just one HTML file. Open `index.html` in a browser. That's all.

To run a local web server (so the file isn't loaded as `file://`):

```sh
python3 -m http.server 8000
```

Then visit `http://localhost:8000/`.

## License

MIT. See [LICENSE](LICENSE).

## Acknowledgements

XFA was killed by Adobe in 2020 ([release notes](https://www.adobe.com/devnet-docs/acrobatetk/tools/AcrobatDC/sdk/eulas-disabled.html)),
but the federal grant system continues to use the format. This tool exists
because it shouldn't have to.
