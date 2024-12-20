# Random Wikipedia walker

Using Selenium, create a small program that, starting from the main page https://www.wikipedia.org/, walks trough a sequence of random links and takes a snapshot of the last page.
The process is as follows:

 1. Navigate to the main page https://www.wikipedia.org/
 2. Select a random link in the page
 3. Navigate to the link
 4. Repeat steps 2 to 3 until you have visited 10 different pages
 5. Take a snapshot of the current page and save it

Include the code of the walker and the snapshot in this document.

## Answer

```python
from selenium import webdriver
from selenium.webdriver.common.by import By
from selenium.webdriver.chrome.options import Options
from selenium.webdriver.support.ui import WebDriverWait
from selenium.webdriver.support import expected_conditions as EC
from selenium.common.exceptions import ElementClickInterceptedException
import random

chrome_options = Options()
chrome_options.add_argument("--start-maximized")

# Set up the Chrome driver
driver = webdriver.Chrome(options=chrome_options)

try:
    driver.get("https://www.wikipedia.org/")
    driver.implicitly_wait(10)

    # Click on the English link
    english_button = driver.find_element(By.ID, "js-link-box-en")
    english_button.click()

    count = 10

    while count > 0:
        # Print current page title and URL
        print(f"Title: {driver.title} | URL: {driver.current_url}")

        # Get valid links
        links = [link for link in driver.find_elements(By.TAG_NAME, "a") if
                 link.get_attribute("href") and link.get_attribute("href").startswith("https://en.wikipedia.org/wiki/")]

        print(f"Number of valid links found: {len(links)}")

        if links:
            random_link = random.choice(links)
            print(f"Next link: {random_link.get_attribute('href')}")

            try:
                WebDriverWait(driver, 10).until(EC.element_to_be_clickable(random_link)).click()
                print(f"\n[{count}]\n")
                count -= 1
            except ElementClickInterceptedException as e:
                print(f"Click issue: {e}")
            except Exception as e:
                print(f"Unexpected error: {e}")
        else:
            print("No valid links found. Exiting...")
            break

    print(f"Final Title: {driver.title} | URL: {driver.current_url}")
    driver.save_screenshot("wikipedia.png")

finally:
    driver.quit()

```

![wikipedia.png](wikipedia.png)