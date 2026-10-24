# Website development

[← Project overview](../README.md)

The site is buildless HTML, CSS, and JavaScript. All predictions are precomputed; browsing the site does not run a model or upload images.

## Preview

```sh
python3 -m http.server 8765 --bind 127.0.0.1 --directory dist
```

Open http://localhost:8765. Stop the server with Ctrl+C.

## Files to edit

- `dist/index.html`: page copy and tables; the opening image uses saved prediction boxes.
- `dist/style.css`: layout and colors.
- `dist/app.js`: image selection, prediction source, labels, and box visibility.
- `dist/gallery.json`: twelve validation cases with ground truth, predictions, counts, and selection notes.
- `dist/assets/`: referenced images, plots, and downloadable evidence.

Keep metric changes tied to recorded experiment artifacts. Do not hand-edit predictions to improve their visual appearance. Keep the final-test results distinct from validation diagnostics.

## Review before publishing

1. Run `node --check dist/app.js` if JavaScript changed.
2. Preview at desktop and narrow widths.
3. Check the three model modes, frame/case selection, boxes, and confidence labels.
4. Open the report downloads and confirm the counts match the analysis.
5. Check that no credentials, personal paths, checkpoints, or unrelated data are added.

The experiment’s tests live with its separately maintained training source; this website repository does not contain or execute that test suite.

## Deployment

| Cloudflare Pages setting | Value |
| :--- | :--- |
| Project | `overwatch-aerial` |
| Production branch | `main` |
| Framework | None |
| Build command | Empty |
| Output directory | `dist` |
| Environment variables | None |

Push a reviewed change to `main`. Cloudflare’s GitHub integration deploys automatically; confirm the **Cloudflare Pages** commit check succeeds before treating the change as live. No credentials belong in this repository. The website uses static hosting with no Functions, database, or server-side inference.

Production: https://overwatch-aerial.pages.dev/
