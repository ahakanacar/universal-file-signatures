# File Signatures Database

A portable, framework-agnostic dataset for identifying file types from their actual content (magic bytes / file signatures). The same data is provided in four formats:
* `signatures.csv`
* `signatures.json`
* `signatures.yaml`
* `signatures.db` (SQLite)

---

## What is this for?

Used to verify what a file actually is by looking at its bytes, rather than trusting its extension (`.jpg`, `.pdf`, etc.). Common use cases:

* **Upload validation:** Checking whether a user-uploaded file really is what it claims to be (e.g. catching a file with a `.jpg` extension that's actually an executable).
* **Digital forensics / data recovery:** Identifying files whose extensions have been stripped or corrupted, based on their content.
* **File type detection:** For projects that want magic-byte checking as plain data, without a `libmagic` / `python-magic` binary dependency (especially useful in JS/web environments that can't rely on native binaries).

---

## Data source and license

The data is derived from Wikipedia's [List of file signatures](https://en.wikipedia.org/wiki/List_of_file_signatures) article and is licensed under **CC BY-SA 4.0**. If you use or redistribute the data files in this repo, you must comply with this license and attribute the source.

> [!NOTE]
> This project is a derivative of Wikipedia content and is not officially affiliated with Wikipedia or the Wikimedia Foundation.

---

## How the data was produced

The data was extracted (manually and semi-automatically) from the table in the Wikipedia article above and converted into structured formats. Several rounds of cleanup and verification specifically addressed:

* **Rowspan Resolution:** Correctly expanding table cells with rowspan (cells spanning multiple rows), which otherwise caused columns to shift and misalign.
* **Multi-extension Split:** Splitting cells that listed multiple extensions at once (e.g. `jpg, jpeg`) into separate records, instead of merging them into a single concatenated string.
* **Stop-word Cleanup:** Removing stray words that had leaked into the extension field from surrounding description text (generic terms like `others`, `designed`, `typically` were cleaned out).
* **Empty BOM Extensions:** Deliberately leaving the extension field empty for entries that don't correspond to any file extension by nature — such as Byte Order Mark (BOM) signatures. This is not an error; the source table itself has no extension listed for these rows.

---

## Schema

Each record contains the following fields:

| Field | Type | Description |
| :--- | :--- | :--- |
| **extension** | String | File extension (may be an empty string for extension-less signatures, e.g. BOM markers) |
| **mime_type** | String | MIME type (`application/octet-stream` if unknown) |
| **magic_bytes** | String | File signature in hex, space-separated bytes (e.g. `FF D8 FF DB`) |
| **offset** | Integer | Starting position of the signature within the file, in bytes |
| **has_wildcard** | Boolean | Whether the signature contains wildcard/variable bytes (`TRUE` / `FALSE`) |
| **signature_index** | Integer | Sequence number when a file type has multiple signature variants |
| **description** | String | Short description of the signature/format |
| **group** | String | General category (e.g. `archives`, `web`, `other`) |
| **execution_risk** | String | Rough label for whether the file type is executable (e.g. `none`, `high`) |

### Example record (CSV row):
```csv
cwk,application/octet-stream,02 00 5A 57 52 54 00 00 00 00 00 00 00 00 00 00,0,FALSE,1,Claris Works word processing doc,other,none
