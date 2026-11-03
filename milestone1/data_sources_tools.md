
# Data Sources & Tools

## 1. Entry Discovery
- **Source:** arXiv API (`https://export.arxiv.org/api/query`)
- Retrieves paper metadata and revision dates using known arXiv IDs.
- Uses sequential requests, rate limiting, retries, and checkpoints.

## 2. Full-Text Source Download
- **Source:** arXiv source endpoint (`https://export.arxiv.org/src/{id}`)
- Downloads source files using `urllib` or `requests`.
- Extracts `.tex` and `.bib` files using `tarfile`.
- Handles PDF-only submissions and single-file sources.

## 3. Reference Extraction
- **Source:** Semantic Scholar Graph API
- Retrieves reference metadata and extracts arXiv IDs from `externalIds`.
- Applies rate limiting and retries.

## 4. Library Summary

| Library / Tool | Purpose |
|---|---|
| `urllib` | Send HTTP requests to arXiv |
| `xml.etree.ElementTree` | Parse arXiv API responses |
| `json`, `hashlib`, `pathlib` | Store metadata and manage checkpoints |
| `tarfile` | Extract source archives |
| Semantic Scholar Graph API | Retrieve reference metadata |
