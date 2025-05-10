# LLM-Powered Literature Review Assistant

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT) 

This repository contains Python notebooks designed to semi-automate the initial screening phase of a Systematic Literature Review (SLR) or general research paper discovery. By leveraging Large Language Models (LLMs), these tools help researchers quickly identify relevant papers from large datasets, significantly reducing manual effort.

## Overview

Conducting thorough literature reviews is crucial but time-consuming. These scripts aim to streamline the process by:

1.  **Fetching paper data:**
    * From Semantic Scholar API based on a search query.
    * From an exported file (BibTeX) from databases like the ACM Digital Library.
2.  **AI-Powered Screening:** Utilizing a Large Language Model (e.g., Google's Gemini) to assess the relevance of each paper against user-defined research objectives and criteria.
3.  **Structured Output:** Generating a CSV file with the LLM's decision, thoughts, and notes for each paper, allowing for further manual review and refinement.

## Features

* **Dual Data Source Support:**
    * `notebooks/semantic_scholar_screener.ipynb`: Searches and screens papers directly from the Semantic Scholar API.
    * `notebooks/acm_export_screener.ipynb`: Screens papers from a user-provided CSV export (e.g., from ACM Digital Library).
* **Customizable Screening:** Define your specific research objectives and decision criteria for the LLM.
* **LLM Integration:** Utilizes Google's Gemini API (easily adaptable for other models).
* **API Key Rotation:** Basic mechanism to rotate through multiple API keys to help manage rate limits.
* **Resumable Processing:** Scripts can check for already processed papers in the output CSV to avoid redundant work.
* **Detailed Output:** Saves paper metadata along with the LLM's screening results (decision, thoughts, and notes) in a CSV format.
* **Error Handling & Retries:** Implements basic retry logic with exponential backoff for API calls.

## How It Works

### 1. `notebooks/semantic_scholar_screener.ipynb`

1.  **Input Configuration:**
    * `search_query`: Your query for the Semantic Scholar API.
    * `gemini_api_keys`: A list of your Google Gemini API keys.
    * `research_objectives`: A clear statement of your research goals.
    * `decision_criteria`: Specific rules for the LLM to decide if a paper is relevant.
2.  **Data Collection:**
    * The script queries the Semantic Scholar bulk search API.
    * It then fetches detailed information (title, abstract, tldr, etc.) for the retrieved paper IDs.
3.  **AI Screening:**
    * Papers are processed in batches.
    * For each batch, a prompt is constructed containing the paper details, research objectives, and decision criteria.
    * The Gemini API is called to evaluate the papers. The LLM provides:
        * `thoughts`: General overview of the paper's relevance.
        * `decision`: A boolean (true/false) indicating if the paper meets the criteria.
        * `note`: Optional additional comments.
4.  **Output:**
    * `screened_papers.csv`: Contains the LLM's screening output (paperId, thoughts, decision, note).
    * `papers.csv`: Contains detailed metadata for all fetched papers.
    * `output.csv`: A merged CSV combining the screening results and paper metadata.

### 2. `notebooks/acm_export_screener.ipynb` (Assuming similar structure)

1.  **Input Configuration:**
    * `input_bib_path`: Path to your exported BibTeX file from ACM Digital Library (or similar).
    * `gemini_api_keys`: A list of your Google Gemini API keys.
    * `research_objectives`: Your research goals.
    * `decision_criteria`: Rules for LLM relevance assessment.
    * *Column Mapping:* Specify which columns in your input file correspond to 'title', 'abstract', etc. (This will need to be added to your script if not already present).
2.  **Data Loading:**
    * The script reads the provided BibTeX into a pandas DataFrame. For other data sources, you'll need to map the columns (id, title, abstract) appropriately.
3.  **AI Screening:**
    * Similar to the Semantic Scholar script, papers are processed in batches.
    * Prompts are constructed using data from the CSV (title, abstract).
    * The Gemini API evaluates the papers.
4.  **Output:**
    * A CSV file (e.g., `output.csv`) containing the original paper data from your CSV, augmented with the LLM's screening results.

## Setup and Installation

1.  **Clone the repository:**
    ```bash
    git clone https://github.com/ParthJain18/LLM-Literature-Review-Assistant.git
    ```
2.  **API Keys:**
  * You will need API keys for Google Gemini.
  * Update the `gemini_api_keys` list in the scripts with your keys.

3.  **Use the notebooks on Jupyter, Kaggle, or Google Colab!**

## Usage

### For `notebooks/semantic_scholar_screener.ipynb`

1.  Open the notebook in a Jupyter environment (like JupyterLab, Colab, or Kaggle).
2.  **Configure Inputs:** In the first code cell, set:
    * `search_query`: Your desired Semantic Scholar search query.
        *Example: `'(LLM | "Large Language Model") AND ("systematic review" | "literature screening")'`
    * `gemini_api_keys = ["YOUR_GEMINI_API_KEY_1", "YOUR_GEMINI_API_KEY_2"]`
    * `research_objectives = "To identify studies that use LLMs to automate or assist in any phase of systematic literature reviews."`
    * `decision_criteria = "ONLY accept papers that explicitly discuss or implement the use of LLMs for tasks within a systematic review process. Exclude papers that only mention LLMs in passing or use them for unrelated tasks like code generation unless that code is for SLR automation."`
3.  Run the notebook cells sequentially.
4.  The output files (`screened_papers.csv`, `papers.csv`, `output.csv`) will be generated in the `/kaggle/working/` directory (if on Kaggle) or your local directory (remember to change the output paths according to your needs).

### For `notebooks/acm_export_screener.ipynb`

1.  Open the notebook.
2.  **Prepare your Data:** Ensure you have an exported BibTeX file from ACM (or other source) with columns like 'Title', 'Abstract', 'DOI', etc.
3.  **Configure Inputs:**
    * `input_bib_path = "/path/to/your/acm_export.bib"`
    * Update `gemini_api_keys`, `research_objectives`, and `decision_criteria` as above.
4.  Run the notebook cells.

## Output Description

The primary output is a CSV file (e.g., `output.csv`) containing:

* **Original Paper Data:** All relevant fields fetched from Semantic Scholar or your input CSV (e.g., `paperId`, `title`, `abstract`, `url`, `DOI`, `authors`, `publicationDate`, etc.).
* **LLM Screening Results:**
    * `thoughts`: The LLM's reasoning or summary related to the paper's relevance.
    * `decision`: `True` if the paper is deemed relevant, `False` otherwise.
    * `note`: Any additional notes from the LLM.

This file can then be easily imported into spreadsheet software or reference managers for further manual screening and data extraction.

## Contributing

Contributions are welcome! If you have suggestions for improvements, new features, or bug fixes, please:

1.  Fork the repository.
2.  Create a new branch (`git checkout -b feature/AmazingFeature`).
3.  Make your changes.
4.  Commit your changes (`git commit -m 'Add some AmazingFeature'`).
5.  Push to the branch (`git push origin feature/AmazingFeature`).
6.  Open a Pull Request.

Some areas for potential contribution:

* Support for more academic databases (e.g., IEEE Xplore, PubMed).
* Integration with other LLMs (e.g., OpenAI models, open-source models via Hugging Face).
* Improved UI/UX (e.g., a simple Streamlit or Gradio app).
* More sophisticated prompt engineering techniques.
* Advanced de-duplication strategies.
* Batch processing and progress tracking enhancements.

## License

This project is licensed under the MIT License - see the [LICENSE.md](LICENSE.md) file for details.

## Acknowledgements

* Semantic Scholar API
* Google Gemini API
* The broader research and open-source communities.

---

Happy Researching!
