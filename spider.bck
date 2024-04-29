import csv
import time
from selenium import webdriver
from selenium.webdriver.chrome.options import Options
from bs4 import BeautifulSoup
import tldextract
from openpyxl import Workbook
import datetime
import re
import logging

# Configure logging to write errors to the error.log file
logging.basicConfig(filename='error.log', level=logging.ERROR, format='%(asctime)s - %(levelname)s - %(message)s')

# Dictionary to store previously fetched ranks
rank_cache = {}

def get_domain(url):
    extracted = tldextract.extract(url)
    return extracted.registered_domain

def extract_rank_text(rank_text):
    if "#" in rank_text:
        rank = rank_text.split('#')[1]
        if rank == "--" or rank.lower() == "null":
            return ""
        else:
            return f"#{rank}"
    else:
        rank = rank_text
        if rank == "--" or rank.lower() == "null" or ',' in rank or int(rank.replace(",", "")) > 1000000:
            return ""
        else:
            return f"{rank}"

def get_global_rank(url):
    driver = None
    try:
        # Check if the rank is already cached
        if url in rank_cache:
            return rank_cache[url]

        options = Options()
        options.headless = True
        options.add_argument("--window-size=1920,1200")
        options.add_argument('--ignore-certificate-errors')  # Ignore SSL certificate errors
        
        driver = webdriver.Chrome(options=options)

        driver.get(f"https://www.similarweb.com/website/{url}")
        time.sleep(5)  # Let the page load

        html_content = driver.page_source
        soup = BeautifulSoup(html_content, 'html.parser')

        # Find the global rank using the provided HTML structures
        rank_div_sm = soup.find('div', class_='wa-rank-list wa-rank-list--sm')
        rank_div_md = soup.find('div', class_='wa-rank-list wa-rank-list--md')
        
        if rank_div_sm:
            rank_p = rank_div_sm.find('div', class_='wa-rank-list__item wa-rank-list__item--global').find('p', class_='wa-rank-list__value')
            if rank_p:
                rank_text = rank_p.get_text(strip=True)
                rank = extract_rank_text(rank_text)
                rank_cache[url] = rank  # Cache the rank
                return rank
        
        if rank_div_md:
            rank_p = rank_div_md.find('div', class_='wa-rank-list__item wa-rank-list__item--global').find('p', class_='wa-rank-list__value')
            if rank_p:
                rank_text = rank_p.get_text(strip=True)
                rank = extract_rank_text(rank_text)
                rank_cache[url] = rank  # Cache the rank
                return rank

        # If all methods fail, return "No Rank"
        rank_cache[url] = ""
        return ""
    except Exception as e:
        logging.error(f"Error occurred while fetching global rank for {url}: {e}")
        return ""
    finally:
        if driver:
            driver.quit()

def main():
    print("#" * 50)
    print("#    solely developed by Ericson Santiago        #")
    print("#            github.com/erickills                #")
    print("#                                                #")
    print("#" * 50)

    # Read domains from text file
    with open('domains.txt', 'r') as file:
        domains = file.read().splitlines()

    print("Fetching global rankings...")
    start_time = time.time()
    wb = Workbook()
    ws = wb.active
    ws.title = "Rankings"
    ws.append(['Domain', 'Global Rank', 'Remarks'])
    for url in domains:
        if url.strip() == "":
            continue
        domain = get_domain(url)
        if domain == "":
            logging.error(f"{url} has no TLD, skipping...")
            print(f"{url} has no TLD, skipping...")
            ws.append([url, "", "no TLD"])
            continue
        if re.search(r'[^\w\d.-]', domain):
            logging.error(f"{domain} with unique characters, skipping...")
            print(f"{domain} with unique characters, skipping...")
            ws.append([url, "", "with unique characters"])
            continue
        if "." not in domain and re.search(r'[^\w\d.-]', url):
            logging.error(f"{url} has no TLD and with unique characters, skipping...")
            print(f"{url} has no TLD and with unique characters, skipping...")
            ws.append([url, "", "no TLD / with unique characters"])
            continue
        rank = get_global_rank(domain)
        if rank == "":
            rank = "No Rank"
        ws.append([url, rank, ""])
        if rank != "":
            print(f"{url}: Global Rank - {rank}")
        else:
            print(f"{url}: No global rank found")

        # Sleep to avoid overloading the server
        time.sleep(1)

    end_time = time.time()
    duration = end_time - start_time
    duration_text = ""
    if duration < 60:
        duration_text = f"{duration:.2f} seconds"
    else:
        duration_text = f"{duration/60:.2f} minutes"

    now = datetime.datetime.now()
    filename = now.strftime("%Y-%m-%d_%I-%M-%S_%p.xlsx")
    ws['A1'].value = 'Domain'
    print(f"Data saved to {filename}. Duration: {duration_text}")
    wb.save(filename)

    input("Press Enter to exit...")

if __name__ == "__main__":
    main()
