# Snapshots

Dated exports of the Machinations diagram, kept next to [CHANGELOG.md](../CHANGELOG.md) so each change can be compared with the model before it.

## Status

Raw diagram JSON snapshots (route 2 below) taken from a signed-in session, owner email redacted:

- [2026-11-10-after-play-677](2026-11-10-after-play-677/): raw diagram JSON and XML after the import-pool fix.
- [2026-11-10-before-fix4-raw.json](2026-11-10-before-fix4-raw.json): state after Play 677, before Fix 4.
- [2026-11-10-after-fix4-raw.json](2026-11-10-after-fix4-raw.json): latest, state after Fix 4 and Play 678. The `xml` field matches the live diagram byte for byte (sha256 `1e03dea7...af67e`). The file was rebuilt from the before snapshot plus the live changes, so non-model metadata may differ slightly from the live response.

Google Sheets CSV export is still pending: the Deploy tab needs at least one Lever marked before it exports, which changes the diagram.

## How to take one

Name files `YYYY-MM-DD-<short-note>` and add a CHANGELOG entry in the same commit.

1. Google Sheets export (documented): Deploy tab, Export to Google Sheets, pick a Drive folder. Open the sheet, File, Download, CSV (one CSV per tab). The sheet lists nodes (Pools, Gates, Registers...) with label, value and parameters. Unknown whether it keeps layout and layers.
2. Raw diagram (undocumented): while signed in, open the diagram and load `https://my.machinations.io/diagram/open/<diagram-id>` (the diagram id begins `08d3afb5`, full id in the editor URL). About 70 KB, the full model with nodes, connections, layers and positions. Save as `<date>-diagram.json` (or `.xml`, whichever it returns).
3. Re-import: only route 1 is documented. Title menu, Import from Google Sheets, Import in new diagram or Overwrite diagram. Route 2 has no documented import.

Also keep the Google Sheet `economy` variable values for the same date, since percentages live there.
