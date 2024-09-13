![JobSearch](JobSearch.jpg)
# Flexible Web Scraper for Job Listings

This Python script is a job scraper that retrieves job listings from an API and stores them in a CSV file. It is highly configurable and can adapt to different job sites by using different attribute mappings. The script dynamically reads job attributes from a text file based on the domain of the job site URL provided by the user.

## Table of Contents

1. [Features](#features)
2. [Requirements](#requirements)
3. [Installation](#installation)
4. [Usage](#usage)
5. [Configuration](#configuration)
6. [How It Works](#how-it-works)
7. [Troubleshooting](#troubleshooting)
8. [License](#license)

## Features

- Scrapes job listings based on job titles provided in a text file.
- Dynamically adapts to different job sites by reading attribute mappings from a CSV file.
- Retrieves 16 common job attributes such as Job Title, Company Name, Location, Job URL, and more.
- Supports the use of a fallback attribute file when a site-specific file is not available.
- Saves results to a CSV file for easy data analysis.
- Includes anti-bot measure handling with randomized delays between API requests.

## Requirements

- Python 3.6 or later
- `httpx` for HTTP requests
- `fake_useragent` for generating random user-agent strings

## Installation

1. **Clone the repository:**
    ```bash
    git clone https://github.com/yourusername/job-scraper.git
    cd job-scraper
    ```

2. **Install the required Python packages:**
    ```bash
    pip install httpx fake-useragent
    ```

3. **Obtain an API Key:**
    - This script uses the [RapidAPI Job Search API](https://rapidapi.com/letscrape-6bRBa3QguO5/api/job-search) to retrieve job listings. You need to sign up for a RapidAPI account and get an API key.

4. **Create the necessary files:**
    - `job_titles.txt`: A text file containing job titles to search for, one per line.
    - `job_attributes_default.txt`: A CSV file that maps common job attributes to API response fields. This file is used if a site-specific attributes file is not available.

## Usage

1. **Run the script:**
    ```bash
    python job_scraper.py
    ```

2. **Enter the job site URL when prompted:**
    - Example: `www.monster.com`

3. **Output:**
    - The script generates a CSV file named `scraped_jobs_<domain>.csv` with the scraped job data.

## Configuration

### Job Titles File (`job_titles.txt`)

The `job_titles.txt` file should contain a list of job titles to search for, one per line. Example:

```plaintext
Software Engineer
Data Scientist
Product Manager

<!-- python code block -->
```python
replaceme
```
