# Milestone 1: Data Crawling 
## Milestone 1 Overview

Milestone 1 consists of three main areas: resolving the assigned range of arXiv paper IDs, downloading the full-text source files, and extracting structured reference information from the papers. Through this milestone, we aim to establish a base corpus of arXiv papers and their references, which will serve as the foundation for subsequent milestones in the project. 

## Objectives & Scope
* **Assigned Scope:** Crawling assigned arXiv IDs and discover all available versions of each paper (initial submissions and subsequent revisions, e.g., `v1`, `v2`,...).
* **Full-Text Source Download:** Retrieve full source archives (`.tar.gz`) or another format, without modifying their contents, extracting only `.tex` and `.bib` files while dismissing binary figure files to save storage and keep the corpus clean.
* **Citation Harvesting:** Query the Semantic Scholar API to build a comprehensive reference network mapped to each paper's external arXiv identifiers.
* **Corpus Foundation:** Establish a local working corpus structure that serves as the direct input for Milestone 2 (parsing) and Milestone 3 (citation matching & final dataset release).

---

## 2. Requirement Traceability Matrix

To facilitate efficient evaluation and grading, the table below maps each requirement specified in the Milestone 1 guidelines directly to its corresponding section and documentation page within this report.

| Requirement ID | Requirement Description | Target Deliverable / Metric | Corresponding Report Page |
| :--- | :--- | :--- | :--- |
| **REQ-01** | **Entry Discovery & Version Tracking** | Resolve assigned arXiv IDs, discover all revision suffixes (`v1`, `v2`,...). | [Data Sources & Tools](./data_sources_tools.md) |
| **REQ-02** | **Full-Text Source Download** | Fetch `.tar.gz` packages from the official mirror (`export.arxiv.org`) with proper pacing. | [Implementation Process](./implementation.md) |
| **REQ-03** | **Figure-Free File Filtering** | Keep only `.tex` and `.bib` files; handle PDF-only edge cases with empty records. | [Implementation Process](./implementation.md) |
| **REQ-04** | **Reference Harvesting** | Extract paper references and external arXiv IDs via Semantic Scholar API. | [Data Sources & Tools](./data_sources_tools.md) |
| **REQ-05** | **Corpus Organization** | Structure local working folders per publication containing metadata and code. | [Appendix](./appendix.md) |
| **REQ-06** | **Statistical Analysis** | Dynamic computation of success rates, average paper size, and citation counts. | [Data Statistics and Analysis](./data_statistics.ipynb) |
| **REQ-07** | **Performance Benchmarking** | Measure wall-clock running time, peak/average RAM, and disk storage usage. | [Performance Benchmarking](./performance.ipynb) |

---

## 3. Report Structure Roadmap

* **[Data Sources & Tools](./data_sources_tools.md):** Detailed overview of arXiv API, how they are queried, data formats, and the libraries used for crawling.
* **[Implementation Process](./implementation.md):** Show the step-by-step scraping workflow, including version discovery, source download, and reference extraction.
* **[Data Statistics & Analysis](./data_statistics.ipynb):** Contains the corpus statistics, including the number of papers, version distribution, the overall success rate, average paper size, and citation counts.
* **[Performance Benchmarking](./performance.ipynb):** Execution benchmarks evaluating runtime efficiency and memory footprint.
* **[Discussion & Limitations](./discussion.md):** Analysis of challenges encountered, API rate limits, and mitigation strategies.
* **[Appendix](./appendix.md):** Data schemas (`papers`, `references`), measurement methods, and configuration .