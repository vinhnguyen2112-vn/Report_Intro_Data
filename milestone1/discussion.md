# Discussion & Conclusion

This chapter reflects on the execution of **Milestone 1**, evaluating our data acquisition results, reviewing technical challenges encountered during crawling, synthesizing key lessons learned, and outlining existing limitations alongside actionable directions for **Milestone 2**.

---

## 1. Evaluation of Results

### 1.1. Crawl Success Rate & Coverage
- **Target vs. Actual**: 
  <!-- TODO: State your assigned target (e.g., 500 papers, ~1,200 versions) versus the actual number of successfully collected papers and versions. -->
  - *Assigned Target*: 
  - *Successfully Harvested*: [e.g., 482 / 500 papers (96.4% success rate)]
  - *Total Versions Crawled*: 27481 versions 
- **Crawl Success Rate**: 100% of the papers that were scrapped and had LaTeX source were successfully downloaded and processed.
- **Storage Efficiency**:
  <!-- TODO: Mention the reduction percentage after discarding binary/image files (.png, .jpg, .pdf) and keeping only .tex and .bib files. -->
  

### 1.2. Data Completeness & Quality
- **LaTeX Source Quality**:
  <!-- TODO: Comment on whether the extracted .tex and .bib files are complete and ready for parsing. -->
  - The collected `.tex` and `.bib` files maintain original relative project structures, ensuring references and custom macros remain intact.
- **Citation Graph Coverage**:
  <!-- TODO: Mention how many references were retrieved and what percentage contained valid arXiv IDs. -->
  - [e.g., ~65%] of retrieved references were successfully linked back to internal arXiv IDs via Semantic Scholar `externalIds`.

---

## 2. Challenges & Bottlenecks

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
