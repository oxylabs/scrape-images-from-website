# scrape-images-from-website
Scrape Images From a Website with Python 


```bash
pip install beautifulsoup4 selenium pandas
```

```bash
#install the Pillow library (used for image processing)
pip install Pillow
#install the requests library (used to send HTTP requests)
pip install requests
```

```python
import pandas as pd
from bs4 import BeautifulSoup
from selenium import webdriver
driver = webdriver.Chrome(executable_path='/nix/path/to/webdriver/executable')
driver.get('https://your.url/here?yes=brilliant')
results = []
content = driver.page_source
soup = BeautifulSoup(content)
```

```python
# Example on how to define a function and select custom arguments for the
# code that goes into it.
def function_name(arguments):
    # Function body goes here.
```

```python
for a in soup.findAll(attrs={'class': 'class'}):
    name = a.find('a')
    if name not in results:
        results.append(name.text)
```

```python
#picking a name that represents the functions will be useful later on.
def parse_image_urls(classes, location, source):
    for a in soup.findAll(attrs={'class': classes}):
        name = a.find(location)
        if name not in results:
            results.append(name.get(source))
```

```python
parse_image_urls("blog-card__link", "img", "src")
```

```python
import pandas as pd
from bs4 import BeautifulSoup
from selenium import webdriver

driver = webdriver.Chrome(executable_path='/nix/path/to/webdriver/executable')
driver.get('https://your.url/here?yes=brilliant')
results = []
content = driver.page_source
soup = BeautifulSoup(content)


def parse_image_urls(classes, location, source):
    for a in soup.findAll(attrs={'class': classes}):
        name = a.find(location)
        if name not in results:
            results.append(name.get(source))

parse_image_urls("blog-card__link", "img", "src")
```

```python
df = pd.DataFrame("links": results})
df.to_csv('links.csv', index=False, encoding='utf-8')
```

```python
#import library requests to send HTTP requests
import requests
for b in results:
#add the content of the url to a variable
    image_content = requests.get(b).content
```

```python
#io manages file-related in/out operations
import io
#creates a byte object out of image_content and point the variable image_file to it
image_file = io.BytesIO(image_content)
```

```python
#we use Pillow to convert our object to an RGB image
from PIL import Image
image = Image.open(image_file).convert('RGB')
```

```python
#pathlib let's us point to specific locations. Will be used to save our images.
import pathlib
#hashlib allows us to get hashes. We will be using sha1 to name our images.
import hashlib
#sets a file_path variable which is pointed to 
#our directory and creates a file based on #the sha1 hash of 'image_content' 
#and uses .hexdigest to convert it into a string.
file_path = pathlib.Path('nix/path/to/test', hashlib.sha1(image_content).hexdigest()[:10] + '.png')
image.save(file_path, "PNG", quality=80)
```

```python
import hashlib
import io
from pathlib import Path
import pandas as pd
import requests
from bs4 import BeautifulSoup
from PIL import Image
from selenium import webdriver

driver = webdriver.Chrome(executable_path='/nix/path/to/webdriver/executable')
driver.get('https://your.url/here?yes=brilliant')
driver.execute_script("window.scrollTo(0, document.body.scrollHeight);")
results = []
content = driver.page_source
soup = BeautifulSoup(content)


def gets_url(classes, location, source):
   results = []
   for a in soup.findAll(attrs={'class': classes}):
       name = a.find(location)
       if name not in results:
           results.append(name.get(source))
   return results


driver.quit()

if __name__ == "__main__":
   returned_results = gets_url("blog-card__link", "img", "src")
   for b in returned_results::
    image_content = requests.get(b).content
    image_file = io.BytesIO(image_content)
    image = Image.open(image_file).convert('RGB')
    file_path = pathlib.Path('nix/path/to/test', hashlib.sha1(image_content).hexdigest()[:10] + '.png')
    image.save(file_path, "PNG", quality=80)
```

```python
image_content = requests.get(b, headers={'User-agent': 'Mozilla/5.0'}).content
```

```python
import io
import pathlib
import hashlib
import pandas as pd
import requests
from bs4 import BeautifulSoup
from PIL import Image
from selenium import webdriver


def get_content_from_url(url):
   driver = webdriver.Chrome()  # add "executable_path=" if driver not in running directory
   driver.get(url)
   driver.execute_script("window.scrollTo(0, document.body.scrollHeight);")
   page_content = driver.page_source
   driver.quit()  # We do not need the browser instance for further steps.
   return page_content


def parse_image_urls(content, classes, location, source):
   soup = BeautifulSoup(content)
   results = []
   for a in soup.findAll(attrs={"class": classes}):
       name = a.find(location)
       if name not in results:
           results.append(name.get(source))
   return results


def save_urls_to_csv(image_urls):
   df = pd.DataFrame({"links": image_urls})
   df.to_csv("links.csv", index=False, encoding="utf-8")


def get_and_save_image_to_file(image_url, output_dir):
   response = requests.get(image_url, headers={"User-agent": "Mozilla/5.0"})
   image_content = response.content
   image_file = io.BytesIO(image_content)
   image = Image.open(image_file).convert("RGB")
   filename = hashlib.sha1(image_content).hexdigest()[:10] + ".png"
   file_path = output_dir / filename
   image.save(file_path, "PNG", quality=80)


def main():
   url = "https://your.url/here?yes=brilliant"
   content = get_content_from_url(url)
   image_urls = parse_image_urls(
       content=content, classes="blog-card__link", location="img", source="src",
   )
   save_urls_to_csv(image_urls)

   for image_url in image_urls:
       get_and_save_image_to_file(
           image_url, output_dir=pathlib.Path("nix/path/to/test"),
       )


if __name__ == "__main__":  #only executes if imported as main file
   main()
```
