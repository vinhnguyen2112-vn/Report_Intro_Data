# arXiv Project 

## About this book

This book is the report of out project. It grows with the project: each milestone adds new section to the same book, so early results stay visible and accessible and the whole story can be read in one place.

The project builds a pipeline that collects arXiv papers together with the metadata of their references. In this project, we focus on the papers of arXiv. The pipeline consists of three main stages: (1) crawling arXiv papers and extracting their references, (2) parsing the LaTeX source files into structured tables and standardizing the metadata, and (3) matching citations to build a citation network and releasing the final dataset for downstream machine learning tasks.

The dataset we are building is a large-scale, structured, and standardized dataset of arXiv papers and their references. It will be used to examine the publication trends, recommend related papers, analyze citation networks, and support various data analysis and machine learning tasks in the field of scientific research.

## How the numbers in this book are produced
 
Nothing in this book is a pasted screenshot. Every figure and table is generated
when the book is compiled:
 
- The scraper writes its measurements (per-paper results, resource samples, raw
  `/usr/bin/time -v` and `du` output) to the `data/` folder of this repository.
- The notebooks read those files and compute every statistic and plot from them.
  Plots are interactive (hover, zoom, toggle series in the legend) and large
  tables are sortable and searchable.
- The scraper itself is not re-run while the book builds. To update a number,
  replace the files in `data/` and rebuild.
- Each notebook ends with an integrity check that cross-validates the data files
  against each other. If the check fails, the build fails.


## Team Members

| Full Name | Student ID 
| :--- | :--- 
| Nguyễn Vĩnh Nguyên | 24127470
| Trương Phan Minh Hằng | 24127033 
| Huỳnh Thị Yến Vy| 24127594 
| Nguyễn Trần Trọng Nhân | 24127092 

## Repository Links

* **Source Code Repository:** [GitHub - Group Code Repo](https://github.com/bamibosua/Project_IntroData.git)
* **Jupyter Book Repository:** [GitHub - Report Pages Repo](https://github.com/vinhnguyen2112-vn/Report_Intro_Data)

---

## Milestone Status

| Milestone | Description | Status | Key Deliverables |
| :--- | :--- | :--- | :--- |
| **Milestone 1** | arXiv Data Crawling & Reference Extraction | Completed | LaTeX source extraction, version tracking, Semantic Scholar reference harvesting. |
| **Milestone 2** | Structural Parsing & Corpus Standardization | In Progress | Parsing TeX files into structured tables, metadata cleaning. |
| **Milestone 3** | Citation Matching & Final Dataset Release | Upcoming | Citation network analysis, downstream ML tasks, Hugging Face dataset publication. |
