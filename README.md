![JobSearch](JobSearch.jpg)
# Flexible Web Scraper for Job Listings

This Python script is a job scraper that retrieves job listings from an API and stores them in a CSV file. It is highly configurable and can adapt to different job sites by using different attribute mappings. The script dynamically reads job attributes from a CSV file based on the domain of the job site URL provided by the user.

## Table of Contents

1. [Features](#features)
2. [Requirements](#requirements)
3. [Installation](#installation)
4. [Usage](#usage)
5. [Configuration](#configuration)
6. [How It Works](#how-it-works)
7. [Troubleshooting](#troubleshooting)
8. [Credits](#credits)
9. [Python Code](#python-code)
 
## Features

- Scrapes job listings based on job titles provided in a text file.
- Dynamically adapts to different job sites by reading attribute mappings from a text file.
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
    - This script uses the [RapidAPI Job Search API](https://rapidapi.com/letscrape-6bRBa3QguO5/api/job-search) to retrieve job listings. You need to sign up for a RapidAPI account and get an API key. The key would need to be placed in the following code block as shown:

<!-- python code block -->
```python
# API information
API_HOST = "jsearch.p.rapidapi.com"
API_KEY = "replace key here"
```

4. **Create the necessary files:**
    - `job_titles.txt`: A text file containing job titles to search for, one per line.
    - `job_attributes_default.txt`: A CSV file that maps common job attributes to API response fields. This file is used if a site-specific attributes file is not available.
  

## Usage

1. **Run the script:**
    ```bash
    python job_scraper.py
    ```

2. **Enter the job site URL when prompted:**
    - Example: `https://www.monster.com`

3. **Output:**
    - The script generates a CSV file named `scraped_jobs_<domain>.csv` with the scraped job data where <domain> is the name of the domain of the job site URL entered. Example: `scraped_jobs_monster.com`

## Configuration

### Job Titles File (`job_titles.txt`)

The `job_titles.txt` file should contain a list of job titles to search for, one per line. Example:

```plaintext
Software Engineer
Data Scientist
Product Manager
Cybersecurity Analyst
Information Security Analyst
Security Operations Center (SOC) Analyst
Network Security Associate
Junior Cybersecurity Specialist
IT Security Intern
Cybersecurity Internship
```

### Job Attributes File (`job_attributes_default.txt`)

Domain-Specific File: Named job_attributes_domain.txt (e.g., job_attributes_monster.txt).
Default File: job_attributes_default.txt
These files should be CSV files with two columns: common_attribute_label and common_attribute_value. Each row represents a mapping of a common job attribute to its potential keys in the API response. Example content:

```plaintext
"common_attribute_label","common_attribute_value"
"Job Title","job_title"
"Company Name","employer_name"
"Location","location"
"Job URL","job_apply_link"
"Job Description","job_description"
"Job Type","job_employment_type"
"Salary","job_min_salary"
```

## How It Works ##
1. Read Job Titles: Reads job titles from job_titles.txt.
2. Determine Attribute File: Checks if a domain-specific attributes file exists; if not, uses job_attributes_default.txt.
3. Read Attribute Mappings: Reads common job attribute mappings from the selected CSV file.
4. API Request: Sends HTTP requests to the job search API using the httpx library.
5. Parse Results: Parses the JSON response and retrieves job attributes based on the attribute mappings.
6. Save to CSV: Saves the parsed job data to a CSV file.

## Troubleshooting ##

### Common Errors: ###

- FileNotFoundError: Ensure job_titles.txt and job_attributes_default.txt are present in the directory.
- API Key Error: Make sure your RapidAPI key is correct and you have access to the Job Search API.
- Network Issues: Ensure you have a stable internet connection.
### Debugging Tips: ###

- Use print statements to log the API response and parsed data.
- Check if the attribute mappings are correct in the CSV files.

## Credits ##
This project is created by, and the property of, Lance Faraday.

## Python Code ##

<!-- python code block -->
```python
import httpx
import json
import csv
import os
import random
import time
from fake_useragent import UserAgent

# API information
API_HOST = "jsearch.p.rapidapi.com"
API_KEY = "replace key here"

# Read job titles from a text file
def read_job_titles(file_path):
    with open(file_path, "r") as file:
        job_titles = [line.strip() for line in file.readlines()]
    return job_titles

# Read common attribute mappings from a CSV file
def read_common_attributes(file_path):
    attributes = {}
    with open(file_path, mode='r', encoding='utf-8') as csvfile:
        reader = csv.DictReader(csvfile)
        for row in reader:
            common_label = row["common_attribute_label"]
            attribute_values = row["common_attribute_value"].split(',')
            attributes[common_label] = attribute_values
    return attributes

# Function to call the API and get job listings
def get_job_listings_from_api(job_title, location="USA"):
    conn = httpx.get(f"https://{API_HOST}/search?query={job_title}%20in%20{location}&page=1&num_pages=1&date_posted=all",
                     headers={
                         'x-rapidapi-key': API_KEY,
                         'x-rapidapi-host': API_HOST
                     })

    if conn.status_code == 200:
        data = conn.json()
        return data
    else:
        print(f"Failed to retrieve data from API for {job_title}. Status code: {conn.status_code}")
        return None

# Utility function to get the first non-null value based on multiple keys
def get_non_null_value(job, keys):
    """Returns the first non-null value from a list of keys in the job dictionary."""
    for key in keys:
        value = job.get(key)
        if value is not None:
            return value
    return "N/A"  # Default value if all keys are None

# Function to scrape job details and write to CSV
def scrape_jobs(url, job_titles, output_file, attributes_file):
    # Read the common attributes from the file
    common_attributes = read_common_attributes(attributes_file)

    ua = UserAgent()
    headers = {
        'User-Agent': ua.random,
        'Accept': 'application/json',
        'Referer': url,
        'Connection': 'keep-alive',
    }

    with httpx.Client(headers=headers, timeout=10.0, follow_redirects=True, http2=True) as client:
        results = []

        for job_title in job_titles:
            print(f"Searching for: {job_title}")
            job_data = get_job_listings_from_api(job_title)

            if job_data:
                for job in job_data.get('data', []):
                    job_info = {
                        common_label: get_non_null_value(job, attribute_values)
                        for common_label, attribute_values in common_attributes.items()
                    }
                    results.append(job_info)

                # Introduce a random delay to avoid triggering anti-bot measures
                delay = random.uniform(1, 5)
                print(f"Waiting for {delay:.2f} seconds before the next request...")
                time.sleep(delay)

        # Write to CSV
        with open(output_file, "w", newline='', encoding='utf-8') as csvfile:
            fieldnames = list(common_attributes.keys())
            writer = csv.DictWriter(csvfile, fieldnames=fieldnames)
            writer.writeheader()
            writer.writerows(results)
        print(f"Job listings saved to {output_file}")

# Main function to run the scraper
def main():
    # Prompt user for input
    url = input("Enter the job site URL to scrape (e.g., https://www.monster.com): ").strip()
    domain_name = url.split("//")[-1].split("/")[0].split('.')[-2]  # Extract domain for output filename

    # Determine the attributes file to use
    attributes_file = f"job_attributes_{domain_name}.txt"
    if not os.path.exists(attributes_file):
        print(f"Attributes file '{attributes_file}' not found. Using default attributes file.")
        attributes_file = "job_attributes_default.txt"

    # Read job titles from text file
    job_titles_file = "job_titles.txt"  # Ensure this file exists with job titles listed line by line
    if not os.path.exists(job_titles_file):
        print(f"Job titles file '{job_titles_file}' not found.")
        return

    job_titles = read_job_titles(job_titles_file)

    # Generate output filename based on the domain
    output_file = f"scraped_jobs_{domain_name}.csv"

    # Start scraping
    scrape_jobs(url, job_titles, output_file, attributes_file)

if __name__ == "__main__":
    main()

```
