File Signatures Database

A portable, framework-agnostic dataset for identifying file types from their actual content (magic bytes / file signatures). The same data is provided in four formats: signatures.csv, signatures.json, signatures.yaml, signatures.db (SQLite).

What is this for?

Used to verify what a file actually is by looking at its bytes, rather than trusting its extension (.jpg, .pdf, etc.). Common use cases:


Upload validation: Checking whether a user-uploaded file really is what it claims to be (e.g. catching a file with a .jpg extension that's actually an executable)
Digital forensics / data recovery: Identifying files whose extensions have been stripped or corrupted, based on their content
File type detection: For projects that want magic-byte checking as plain data, without a libmagic/python-magic binary dependency (especially useful in JS/web environments that can't rely on native binaries)


Data source and license

The data is derived from Wikipedia's List of file signatures article and is licensed under CC BY-SA 4.0. If you use or redistribute the data files in this repo, you must comply with this license and attribute the source.


This project is a derivative of Wikipedia content and is not officially affiliated with Wikipedia or the Wikimedia Foundation.



How the data was produced

The data was extracted (manually and semi-automatically) from the table in the Wikipedia article above and converted into structured formats. Several rounds of cleanup and verification specifically addressed:


Correctly expanding table cells with rowspan/colspan (cells spanning multiple rows), which otherwise caused columns to shift and misalign.
Splitting cells that listed multiple extensions at once (e.g. "jpg, jpeg") into separate records, instead of merging them into a single concatenated string.
Removing stray words that had leaked into the extension field from surrounding description text (generic terms like "others", "designed", "typically" were cleaned out).
Deliberately leaving the extension field empty for entries that don't correspond to any file extension by nature — such as Byte Order Mark (BOM) signatures. This is not an error; the source table itself has no extension listed for these rows.


The dataset was manually re-scanned multiple times against known error patterns (generic/meaningless extension values, malformed hex, overly long or merged extension strings) before being finalized. That said, since the source is periodically updated and the extraction process was manual/semi-automatic, some errors may still have slipped through — please open an issue if you spot one.

Schema

Each record contains the following fields:

FieldDescriptionextensionFile extension (may be an empty string for extension-less signatures, e.g. BOM markers)mime_typeMIME type (application/octet-stream if unknown)magic_bytesFile signature in hex, space-separated bytes (e.g. FF D8 FF DB)offsetStarting position of the signature within the file, in byteshas_wildcardWhether the signature contains wildcard/variable bytes (TRUE/FALSE)signature_indexSequence number when a file type has multiple signature variantsdescriptionShort description of the signature/formatgroupGeneral category (e.g. archives, web, other)execution_riskRough label for whether the file type is executable (e.g. none)

Example record (CSV row):

cwk,application/octet-stream,02 00 5A 57 52 54 00 00 00 00 00 00 00 00 00 00,0,FALSE,1,Claris Works word processing doc,other,none

Note on empty extension fields: Some signatures, such as Byte Order Marks (BOMs), don't correspond to any real file extension. For these records, extension is intentionally empty — make sure your code handles this empty/null case rather than assuming every record has an extension.

Record count

The dataset currently contains 626 records (identical data across all four formats, just different presentations).

Contributing

If you spot an incorrect or missing entry, feel free to open an issue or pull request. When proposing a change, please reference the corresponding row in the source Wikipedia article.

License


Data: CC BY-SA 4.0 (derived from Wikipedia — see "Data source and license" above)
This repository/compilation: [state your own license here, e.g. MIT — this only applies to the compilation/organization; the underlying data remains CC BY-SA]
