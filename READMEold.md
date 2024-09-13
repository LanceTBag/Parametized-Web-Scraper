![JobSearch](JobSearch.jpg)
 
# Flexible Web Scraper for Job Listings

## Overview

This Python script is a flexible web scraper designed to search job sites for specific job listings. It reads job titles from a text file, scrapes specified job sites, and extracts detailed job information such as job title, company name, job listing URL, experience required, job level, type (in-person or remote), starting salary, and location. The scraper also utilizes an API for job classification and saves the data in a CSV file. It reads the names of the job attributes from a text file to allow for additional flexibility.

## Requirements

- Python 3.7+
- Libraries: `requests`, `httpx[http2]`, `fake-useragent`,  `csv`
- API Key for RapidAPI's job classifier

## Installation

1. Clone or download the repository.
2. Install the required Python libraries:

    ```bash
    pip install requests beautifulsoup4
    ```

3. Obtain an API key from [RapidAPI](https://rapidapi.com/) for the job classifier API and set it as an environment variable named `RAPIDAPI_KEY`.

## Usage

1. Create a text file named `job_titles.txt` in the same directory as the script. Add job titles you want to search for, one per line.

2  If the job site uses non-standard job attribute labels, create a text file named `job_attributes_<domain>.txt` in the same directory as the script, where <domain> is the domain of the job site, using the file 'job_attributes_default,txt' as a template (do not edit ot delete 'job_sttributes_default.txt'.

3. Run the script using Python:

    ```bash
    python scraper.py
    ```

4 When prompted, enter the URL of the job site you want to scrape.

5. The script will search for job listings matching the job titles, classify each job using the API, and save the results to a CSV file named `scraped_jobs_<domain>.csv`, where <domain> is the domain from the job website.

## Configuration

- Ensure the HTML tags and classes in the `scrape_job_site()` function match the structure of the job site you are scraping. Modify the code accordingly for different websites.

## Notes

- Be mindful of the terms and conditions of the job sites you scrape.
- Adjust the delay (`time.sleep()`) between requests to avoid overloading the job sites and getting blocked.
- The script currently handles static HTML. For dynamic content (e.g., JavaScript-loaded data), consider using Selenium.

## License

This project is licensed under the MIT License.

<!-- python code block -->
```python
replaceme
```
