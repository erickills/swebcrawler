### swebcrawler
automates the process of fetching the global rank of a list of domains from the SimilarWeb website, saves the results to an Excel file, and handles any errors that may occur during the process.

performs the following tasks:

- Initializes logging to capture errors and save them to an error.log file.
- Defines functions to extract the domain name from a URL, extract the rank text from the SimilarWeb website, and fetch the global rank of a given URL.
- Implements a caching mechanism to store previously fetched ranks to avoid unnecessary requests.
- Reads a list of domain names from a text file.
- Iterates over each domain name, extracts its domain, and fetches its global rank using Selenium and BeautifulSoup.
- Logs any errors that occur during the process to the error.log file.
- Outputs the domain name and its global rank to the console.
- Saves the domain names, their global ranks, and any remarks to an Excel file.
- Prints the duration of the process and the filename of the saved Excel file.
- Waits for user input to exit the script.

