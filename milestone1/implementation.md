# Implementation Details

This document outlines the technical implementation, end-to-end pipeline architecture, and exception-handling mechanisms for Milestone 1. The data acquisition system is built modularly and carried out collaboratively across three primary responsibilities: **Entry Discovery**, **Full-Text Source Download & Selective Extraction**, and **Citation & Reference Crawling**.

---

## I. System Architecture & End-to-End Pipeline

### 1. Three-Stage Data Pipeline
#### 1.1. End-to-End Data Flow Diagram
```{mermaid}
flowchart TD
    subgraph S1["Stage 1: Entry Discovery (Member 1)"]
        A["Assigned arXiv ID Range"] --> B["Query arXiv API / OAI-PMH"]
        B --> C["Version & Metadata Detection"]
        C --> D["discovery_log.json"]
    end

    subgraph S2["Stage 2: Source Download & Extraction (Member 2)"]
        D --> E["Download Source by Version\n(export.arxiv.org/src/{id}v{n})"]
        E --> F{"Archive Type Check"}
        F -->|"tar.gz / gz"| G["Selective Extraction\n(.tex + .bib only, drop images)"]
        F -->|"PDF-only"| H["Record Empty / Mark PDF-only"]
        G --> I["Corpus Directory Storage"]
        G --> J["run_manifest.json"]
        H --> J
    end

    subgraph S3["Stage 3: Reference Crawling (Member 3)"]
        D --> K["Query Semantic Scholar Graph API"]
        K --> L["Extract References & Map externalIds"]
        L --> M["citation_network / references.json"]
    end
```
#### 1.2. Role Allocation & Team Responsibilities
| Main Task | Assigned Member | Primary Deliverables / Output |
| :--- | :--- | :--- |
| Entry Discovery & Metadata Retrieval | Minh Hằng | `discovery_log.json`, ID range validatio 2**| 
| Source Download & Selective Extraction | Yến Vy | Raw Corpus (`.tex`, `.bib`), `run_manifest.jsoe 3**|
| Citation & Reference Network Crawling | Trọng Nhân | Reference dataset, external arXiv ID mapping |

## II. Detailed Implementation  

### 1. Module 1: Entry Discovery & Metadata Promber 1
#### 1.1. Query Logic & Assigned ID Range Traversal
##### 1.1.1. Purpose
Entry discovery use to identify papers within the assigned ranges, retrieve their metadata, and determine the version history of each paper.

#### 1.2. Version Detection Mechanism
- Algorithms for detecting total published versions ($v_1, v_2, \dots, v_n$) per paper.
- Pre-filtering papers flagged as withdrawn or invalid.

#### 1.3. Output Data Structure: `discovery_log.json`
- Schema definition: `arxiv_id`, `title`, `authors`, `categories`, `num_versions`, `submission_date`, `status`, `discovery_time_sec`.

#### 1.4. Edge Cases & Exception Handling in Module 1
- **Withdrawn Papers**: Identifying notices without source code and setting appropriate status flags.
- **Non-existent / Skipped IDs**: Handling gaps in arXiv ID sequences gracefully.
- **API Request Timeouts**: Implementing timeouts and query retries.

---

### 2. Module 2: Full-Text Source Download & Selective Extraction
#### 2.1. Version-Level Source Download Flow
- Iterating through each `paper × version` tuple.
- Fetching source archives from `https://export.arxiv.org/src/{id}v{n}`.

#### 2.2. Selective Extraction Mechanism
- Magic byte inspection to verify archive formats (`tar.gz`, `gzip`, `pdf`).
- Filtering rules: Retaining solely `.tex` and `.bib` files; purging image files (`.png`, `.jpg`, `.pdf`, `.eps`), build artifacts (`.aux`, `.bbl`, `.log`), and miscellaneous binaries to optimize disk usage.

#### 2.3. Handling Divergent Source Packaging Types
- **Standard Tarball (`.tar.gz`)**: Unpacking multi-file LaTeX projects.
- **Single Gzipped File (`.gz`)**: Uncompressing standalone `.tex` files without a tar wrapper.
- **PDF-only Submissions**: Catching non-LaTeX submissions, skipping uncompress operations, and logging the event without pipeline breakage.

#### 2.4. Execution Audit Logging: `run_manifest.json`
- Record format: `arxiv_id`, `version`, `source_type`, `raw_size_bytes`, `extracted_size_bytes`, `num_tex_files`, `num_bib_files`, `download_time_sec`, `extract_time_sec`.

---

### 3. Module 3: Citation & Reference Network Crawling
#### 3.1. Semantic Scholar Academic Graph API Integration
- API endpoints, query construction, and selected fields (`references.paperId`, `references.title`, `references.externalIds`).

#### 3.2. Reference Extraction & arXiv ID Linking
- Parsing citation metadata and mapping `externalIds.ArXiv` to discover internal citations.
- Calculating reference crawl coverage across the assigned corpus.

#### 3.3. Output Reference Data Format
- Storing directional citation pairs (`source_arxiv_id` $\rightarrow$ `target_arxiv_id`) and fallback titles for external publications.

#### 3.4. Edge Cases & Exception Handling in Module 3
- Papers unindexed or pending indexing by Semantic Scholar.
- Empty reference lists, non-standard DOIs, and missing arXiv external identifiers.

---

## III. Rate Limiting & Error Handling

### 1. Request Delay (Avoiding IP Bans)
#### 1.1. arXiv Delay Policy
- **Minimum Delay**: Wait at least 3.0 seconds between consecutive downloads (strictly following arXiv rules).
- **Sequential Requests**: Send requests one by one (single-threaded) to avoid overloading arXiv servers and getting our IP blocked.

#### 1.2. Semantic Scholar API Quota
- **Call Frequency**: Keep queries within the allowed limit (at most 1 request per second for public unauthenticated access).
- **Efficient Querying**: Request only needed fields (`paperId`, `title`, `externalIds`) to reduce payload size and speed up responses.

### 2. Retry Logic (Handling Network Failures)
#### 2.1. Handling Rate Limits & Busy Servers (HTTP 429 & 503)
- **Issue**: The server returns `429 Too Many Requests` or `503 Service Unavailable` when busy.
- **Solution (Exponential Backoff)**:
  - 1st failure: Wait 5 seconds $\rightarrow$ Retry.
  - 2nd failure: Wait 15 seconds $\rightarrow$ Retry.
  - 3rd failure: Wait 30 seconds $\rightarrow$ Final retry.
  - Add a small random delay (1–2 seconds) so repeated requests do not collide.

#### 2.2. Handling Timeouts & Dropped Connections
- **Request Timeout**: Abort requests that hang for more than 20 seconds.
- **Fail-Safe Logging**: If a paper fails after 3 retries, record the error in `run_manifest.json` and move to the next paper (preventing the crawler from crashing halfway).

---

## IV. Output Storage Organization & Downstream Readiness

### 1. Corpus Directory Layout
- Structured directory tree for raw vs. extracted files per paper and version.

### 2. Papers Data Schema
- Relational/tabular representation connecting metadata, version counts, and file system paths.

### 3. References Data Schema
- Graph edge-list representation (`source_id`, `target_id`, `reference_metadata`).

### 4. Readiness for Milestone 2 (Text Parsing & Processing)
- Integrity verification of harvested `.tex` / `.bib` files, ensuring clean inputs for LaTeX parsing and AST extraction in Milestone 2.

