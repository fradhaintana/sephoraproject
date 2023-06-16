# Sephora Project

<picture>
 <img alt="YOUR-ALT-TEXT" src="https://hips.hearstapps.com/hmg-prod/images/766/sephora-beauty-buys-1518567011.jpg">
</picture>

## About Me

Name: Fradha Intan Arassah

NIM: G1501221018

Subject: Management Data Statistics

## About Project

This project is to complete the final assignment for the statistics data management course during the master's degree in statistics and data science at IPB University. After learning about data management in this class, I had to apply what I had learned from the MDS course and optimize this project. So this project will continue to update from time to time. So far what I've done is:

1) Scraping data to get product info from Sephora
2) Connecting data to MongoAtlas

## Scraping Data Sephora

Here are some steps to scape the product information:

### Step 1: Choose the website for scraping

In this project, i want to scrape make up product from sephora. Here's the link of sephora make up 

[Sephora Makeup Link](https://www.sephora.com/shop/makeup-cosmetics)

### Step 2: Scrape the website link

I use python to scraping the product's information. BeautifulSoup package is needed. It can be seen in the script below:
```
from bs4 import BeautifulSoup
import requests
import pandas as pd

url = "https://www.sephora.com/shop/makeup-cosmetics"
HEADERS = ({'User-Agent':'Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/114.0.0.0 Safari/537.36','Accept-Language':'en-US,en;q=0.5'})
webpage = requests.get(url, headers=HEADERS)
soup = BeautifulSoup(webpage.content,'html.parser')
soup
```
after this script is run, we have successfully scraped the url from sephora make up and are ready to retrieve the information we need.

### Step 3: Scrape any product link in this website

on the sephora makeup url, there are tons of products. Each product has its own url, therefore only 10 products will be taken. It can be seen in the script below:
```
links = soup.find_all("a", attrs={'class':'css-klx76'})
for link in links:
        links_list.append(link.get('href'))
links_list
```
after this script is run, the links for each make up product will appear. If you run once, 4 product links will appear. Here's the result:

['https://www.sephora.com/product/ilia-super-serum-skin-tint-spf-40-P455418?skuId=2333607&icid2=products grid:p455418:product',
 'https://www.sephora.com/product/rare-beauty-by-selena-gomez-soft-pinch-liquid-blush-P97989778?skuId=2640241&icid2=products grid:p97989778:product',
 'https://www.sephora.com/product/rare-beauty-by-selena-gomez-soft-pinch-tinted-lip-oil-P505568?skuId=2640159&icid2=products grid:p505568:product',
 'https://www.sephora.com/product/rare-beauty-by-selena-gomez-positive-light-liquid-luminizer-highlight-P38855877?skuId=2362168&icid2=products grid:p38855877:product']

 ### Step 4: Scrape product information from each link

An example will be given for scraping information from 1 product. However, if you want to directly scrape information from all product links, you can use a looping script and define the information scraping function first. Because when doing this task I had problems running looping product links (running was very long, I had been waiting for 2 hours but it didn't finish yet), so I will give an example of just scraping 1 url 1 product to get the information needed.

It can be seen in the script below:
```
p1 = links_list[0] #takefirstproductlink
p1
```

Next, scraping product information using this script:
```
new_webpage1 = requests.get(p1, headers=HEADERS)
new_soup1 = BeautifulSoup(new_webpage1.content, "html.parser")
brand1 = new_soup1.find("a", attrs={"data-at":'brand_name'}).text.strip()
product1 = new_soup1.find("span", attrs={"data-at":'product_name'}).text.strip()
price1 = new_soup1.find("b", attrs={"class":'css-0'}).text.strip()
likes1 = new_soup1.find("span", attrs={"class":'css-jk94q9'}).text.strip()
size1 = new_soup1.find("span", attrs={"data-at":'sku_size_label'}).text.strip()
deskripsi1 = new_soup1.find("p", attrs={"class":'css-1a157mi eanm77i0'}).text.strip()
```

Do the same for the other 3 products.

### Step 5: Make a table from the result

After successfully extracting information from the four product links, a table will be created containing this information. Here's the script:
```
sephora = pd.DataFrame({'Nama Brand':brand1, 'Nama Produk':product1, 'Harga':price1,'Jumlah Likes':likes1,
         'Size': size1,'Deskripsi Produk':deskripsi1}, index=[0])
sephora = sephora.append({'Nama Brand':brand2, 'Nama Produk':product2, 'Harga':price2,'Jumlah Likes':likes2,
         'Size': size2,'Deskripsi Produk':deskripsi2}, ignore_index=True)
sephora = sephora.append({'Nama Brand':brand3, 'Nama Produk':product3, 'Harga':price3,'Jumlah Likes':likes3,
         'Size': size3,'Deskripsi Produk':deskripsi3}, ignore_index=True)
sephora = sephora.append({'Nama Brand':brand4, 'Nama Produk':product4, 'Harga':price4,'Jumlah Likes':likes4,
         'Size': size4,'Deskripsi Produk':deskripsi4}, ignore_index=True)
sephora
```

Save the data frame into cvs, using this script:
```
sephora.to_csv('sephorascrapingdata.csv')
```

## Connect to Mongo Atlas

Create a project in mongo atlas first. To connect scraped data to mongo Atlas, you can use the following script:
```
import pymongo
import json
import pandas as pd
client = pymongo.MongoClient("mongodb+srv://fradhaintana:Fraday0630@cluster0.l0u0ffa.mongodb.net/")
df = pd.read_csv('sephorascrapingdata.csv')
data = df.to_dict(orient="record")
db = client["MDS"]
print(db) 
```
I created the project name "Sephora Data" because I didn't have a Database yet, so I created a Database named "MDS", with collection name "sephora"

![MongoAtlas](https://github.com/fradhaintana/sephoraproject/assets/111562146/b86a2fee-b288-41ca-adb1-7a7c04eac31c)
