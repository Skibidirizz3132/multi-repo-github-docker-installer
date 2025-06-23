# Multi-Repo GitHub Docker Installer

Automate cloning, dependency installation, and test execution for multiple GitHub repositories using Docker and OpenAI. Supports Python and Node.js projects, with error tracking and optional cloud sandboxing. Ideal for reproducible, large-scale repo analysis.

---

## Features

- **Bulk GitHub Repo Cloning:** Extracts and clones repositories from a bookmarks HTML file or a provided list.
- **Automated Dependency Installation:** Installs Python and Node.js dependencies using both static analysis and LLM inference.
- **Containerized Execution:** Runs each repository in its own Docker container for isolation and reproducibility.
- **Test Code Generation:** Uses OpenAI to synthesize minimal test scripts for each repo.
- **Error Logging & Tracking:** Maintains detailed logs of installation and execution results for each repository.
- **Parallel and Sequential Modes:** Supports both sequential and parallel processing of repositories.
- **Cloud Sandbox Support:** Optionally runs code in E2B sandboxes for ephemeral, cloud-based execution.
- **Extensible:** Easily add support for more languages or dependency managers.

---

## Table of Contents

- [Installation](#installation)
- [Usage](#usage)
  - [1. Extract GitHub Links](#1-extract-github-links)
  - [2. Clone and Install Repos in Docker](#2-clone-and-install-repos-in-docker)
  - [3. Run Test Code in Containers](#3-run-test-code-in-containers)
  - [4. (Optional) Use Cloud Sandboxes](#4-optional-use-cloud-sandboxes)
- [Customization](#customization)
- [Troubleshooting](#troubleshooting)

---

## Installation

1. **Clone this repository:**
   ```bash
   git clone https://github.com/nsourlos/multi-repo-github-docker-installer.git
   cd multi-repo-github-docker-installer
   ```

2. **Install Python dependencies:**
   ```bash
   pip install -r requirements.txt
   ```
   - Required: `docker`, `openai`, `tqdm`, `python-dotenv`, `bs4`, `gitingest`, `e2b_code_interpreter` (for sandboxes)

3. **Install Docker:**
   - [Docker Desktop](https://www.docker.com/products/docker-desktop/) (required for local container runs)

4. **Set up environment variables:**
   - Create an `.env` file in the project root with your OpenAI and (optionally) E2B API keys:
     ```
     OPENAI_API_KEY=your-openai-key
     E2B_API_KEY=your-e2b-key
     ```

---

## Usage

### 1. Extract GitHub Links

- Place your bookmarks HTML file (e.g., `bookmarks_6_3_25.html`) on your Desktop.
- The notebook will parse this file and extract all GitHub repository links from the "Tutorials" folder.

### 2. Clone and Install Repos in Docker

- The main workflow will:
  - Clone each repo into a `repos/` directory.
  - Use GPT to extract or synthesize a minimal test script.
  - Create a Docker container for each repo.
  - Install Python and/or Node.js dependencies (from `requirements.txt`, `package.json`, and LLM inference).
  - Run the test script and log results.

- **To run the full pipeline:**
  - Open the notebook in Jupyter or VSCode.
  - Set your API keys.
  - Run the cell containing:
    ```python
    await run_all_pipelines()
    ```

- **Outputs:**
  - Logs and results are saved in the `repos/` directory as JSON files:
    - `all_container_ids.json`
    - `error_log.json`
    - `requirements_successful.json`
    - `code_successful.json`
    - `requirements_successful_code_failed.json`

### 3. Run Test Code in Containers

- You can interactively run additional test scripts inside any created container using the provided code snippets.
- Example:
  ```python
  import docker
  client = docker.from_env()
  container = client.containers.get('repo_fireducks')
  result = container.exec_run("python test_fireducks.py")
  print(result.output.decode())
  ```

### 4. (Optional) Use Cloud Sandboxes

- The notebook supports running code in E2B sandboxes for ephemeral, cloud-based execution.
- To use, set your `E2B_API_KEY` and run the relevant cells in the "Sandboxes" section.

---

## Customization

- **Add More Dependency Managers:** Extend the `install_all_dependencies` function to support other languages (e.g., Ruby, Go).
- **Change Test Script Generation:** Modify the LLM prompt to generate different types of test scripts.
- **Parallel/Sequential Execution:** Choose between parallel and sequential repo processing for speed or rate-limit avoidance.

---

## Troubleshooting

- **Docker Permission Errors:** Ensure Docker Desktop is running and your user has permission to run Docker commands.
- **API Rate Limits:** OpenAI and E2B have rate limits; adjust the number of repos or add delays as needed.
- **Missing Dependencies:** The LLM may not always infer all dependencies; check logs for errors and update requirements as needed.

---