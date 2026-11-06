# Discussion & Conclusion

This chapter interprets the result of milestone 1, **explains the challanges** we met while crawling and **describes how we solved them**, **states what we learned** from this milestone, **admits the limits** of the corpus, and describes how we will prepare for milestone 2.


---

## 1. Summary of Milestone 1 Results
Every number in the table below have been calculated from the `run_mainifest.json` file and have taken from `data_statistics.ipynb` and `performance.ipynb`. 
| Item | Result |
|---|---|
| Assigned paper IDs |  2609.12834 - 2609.17833 / 2608.09007 - 2608.14006 / 	2601.18156–2601.23155 / 	2511.11709–2511.16708|
| Papers crawled successfully | 20000 papers (Rate success: 100%) |
| Versions crawled in total | 27481 versions |
| Versions with TeX source / PDF-only (empty record) | [N] / [N] |
| Average paper size before / after removing figures | [x] MB / [x] MB ([x]% smaller) |
| Average number of references per paper | [x] |
| Share of references that have an arXiv ID | [x]% |
| Reference-metadata success rate (average) | [x]% |
| Total wall time / average time per paper | [x] h / [x] s |
| Peak RAM / average RAM | [x] MB / [x] MB |
| Peak disk / final output size | [x] GB / [x] GB |

---
## 2. Explanation of Results 

### 2.1. Diverse Source Packaging & Edge Cases
- **PDF-Only Submissions**:
  <!-- TODO: Explain how non-LaTeX submissions (e.g., scanned PDFs or direct PDF uploads) were handled. -->
  - Approximately [e.g., 3–5%] of papers did not contain LaTeX source packages. The crawler had to identify them safely without throwing extraction exceptions.
- **Packaging Inconsistencies**:
  <!-- TODO: Describe how standalone .gz files differed from multi-file .tar.gz archives. -->
  - Variations between standard `.tar.gz` multi-file bundles and standalone single `.gz` compressed files required dynamic format detection based on magic bytes.

### 2.2. Strict Rate Limiting & Blocking Risks
- **arXiv Throttling**:
  <!-- TODO: Discuss the challenge of complying with the >= 3-second delay rule while managing overall crawl duration. -->
  - Adhering strictly to arXiv’s $\ge 3.0$ seconds polite crawling rule made sequential downloads time-intensive, requiring careful timeout and retry handling.
- **Semantic Scholar API Quotas**:
  <!-- TODO: Note occurrences of HTTP 429 (Too Many Requests) and how exponential backoff mitigated them. -->
  - Unauthenticated calls occasionally triggered HTTP 429 status codes during peak hours, necessitating robust backoff routines.

### 2.3. Withdrawn Papers & Missing Metadata
- <!-- TODO: Describe how withdrawn papers (lacking source) or papers unindexed by Semantic Scholar were processed. -->
  - Papers withdrawn by authors had their metadata noted but lacked download links, requiring explicit flags in `discovery_log.json`.

---

## 3. Key Lessons Learned

### 3.1. Defensive Programming & Graceful Degradation
- Designing scrapers with fail-safe boundaries ensures that network drops or malformed archives for an individual paper never terminate the global crawling job.
- Detailed audit logs (`run_manifest.json`) are critical for validating pipeline health without manually inspecting thousands of folders.

### 3.2. Checkpointing & Resumability
- Persistent checkpointing prevents redundant network downloads. If a process is interrupted, it can resume from the exact last unvisited paper ID.

### 3.3. Downstream-Aware Data Organization
- Structuring files early with a clean, standardized folder hierarchy significantly simplifies the input pipeline for Milestone 2 (LaTeX tokenization and parsing).

---

## 4. Current Limitations

### 4.1. Inherent Data Gaps
- **Unrecoverable LaTeX Source**: Papers uploaded exclusively as PDFs cannot yield LaTeX source code without noisy OCR or reverse-compilation.
- **Missing External Identifiers**: Not all citations indexed by Semantic Scholar contain an `externalIds.ArXiv` entry, leaving minor gaps in internal citation graph linkages.

### 4.2. Sequential Crawling Bottleneck
- While strictly respectful of arXiv guidelines, single-threaded sequential execution bounded the total crawling throughput.

---

## 5. Future Improvements & Preparation for Milestone 2

### 5.1. Crawling Infrastructure Improvements
- Implementing asynchronous task queues for independent non-arXiv APIs (e.g., parallelizing citation queries while respecting individual service rate limits).
- Automated health-check alerts if consecutive request failure rates exceed a safety threshold.

### 5.2. Direct Transition to Milestone 2 (Parsing & Analysis)
- **Macro & Preamble Extraction**: Identifying `\def`, `\newcommand`, and custom preamble headers across gathered `.tex` files.
- **Equation & Text Normalization**: Cleaning LaTeX math markup (`$...$`, `equation` environments) into standardized AST representations.
- **Citation Graph Construction**: Ingesting the harvested reference pairs into network analysis libraries (`NetworkX`) for structural graph metrics.
ho Milestone 2 (parsing)
