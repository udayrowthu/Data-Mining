import requests
from bs4 import BeautifulSoup as bs
import csv
import nltk
import pandas as pd
from nltk import word_tokenize
from nltk.corpus import stopwords
from sklearn.feature_extraction.text import TfidfVectorizer
from sklearn.metrics.pairwise import cosine_similarity
pd.options.mode.chained_assignment = None


headers = {
    "Accept": "text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,image/apng,*/*;q=0.8,application/signed-exchange;v=b3;q=0.9",
    "Accept-Encoding": "gzip, deflate, br",
    "Accept-Language": "en-GB,en-US;q=0.9,en;q=0.8",
    "Sec-Ch-Ua": "\"Google Chrome\";v=\"105\", \"Not)A;Brand\";v=\"8\", \"Chromium\";v=\"105\"",
    "Sec-Ch-Ua-Mobile": "?0",
    "Sec-Ch-Ua-Platform": "\"macOS\"",
    "Sec-Fetch-Dest": "document",
    "Sec-Fetch-Mode": "navigate",
    "Sec-Fetch-Site": "cross-site",
    "Sec-Fetch-User": "?1",
    "Upgrade-Insecure-Requests": "1",
    "User-Agent": "Mozilla/5.0 (Macintosh; Intel Mac OS X 10_15_7) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/105.0.0.0 Safari/537.36",
    "X-Amzn-Trace-Id": "Root=1-63486521-27ea95a10f3ccd69476b99e1"
  }


def get_amazon_url(search):
    search=search.replace(" ","+")
    def_url="https://www.amazon.com/s?k={}"
    url=def_url.format(search)
    return url


def get_bjs_url(search):
    search=search.replace(" ","%20")
    def_url="https://www.bjs.com/search/{}"
    url=def_url.format(search)
    return url
  

def get_ebay_url(search):
    search=search.replace(" ","+")
    def_url="https://www.ebay.com/sch/i.html?_from=R40&_trksid=p2380057.m570.l1313&_nkw={}"
    url=def_url.format(search)
    return url
  

def get_costco_url(search):
    search=search.replace(" ","+")
    def_url="https://www.costco.com/CatalogSearch?dept=All&keyword={}"
    url=def_url.format(search)
    return url

f = open("/*Path of your current working directory*/results.csv", 'w')
writer = csv.writer(f)
writer.writerow(['Company','name','price','link','image','description_tokens','sim_index'])

def tokenize(des):
    tokens = word_tokenize(des)
    tokens = [t for t in tokens if not t.lower in stopwords.words("english")]
    porter = nltk.PorterStemmer()
    porter_tokens = [porter.stem(t) for t in tokens]
    words = [w.lower() for w in porter_tokens]
    description = sorted(set(words))
    return description


def search_amazon(search):
    
    url=get_amazon_url(search)
    for i in range(5):
        html_text=requests.get(url, headers = headers)
        soup=bs(html_text.content,"lxml")
        soup=bs(soup.prettify(),"lxml")
        items=soup.find_all('div', class_="a-section a-spacing-base")
        if len(items) > 0:
            break
    n = len(items)
    print("Amazon got "+str(n)+" results")
    for count,item in enumerate(items):
        description = ""
        try:
            image = item.find('img', class_="s-image")['src']
        except:
            image = None
        company = "Amazon"
        name = item.h2.a.span.text.strip()
        link = item.find('a', class_="a-link-normal s-underline-text s-underline-link-text s-link-style a-text-normal")['href']
        link = "https://www.amazon.com/" + link
        try:
            try:
                price = item.find('span', class_ = "a-offscreen").text.strip()
                pi = price.find('$')
                price = float(price[pi+1:].strip())
            except:
                price = None
            url = link
            html_text=requests.get(url, headers = headers)
            soup=bs(html_text.content,"lxml")
            soup=bs(soup.prettify(),"lxml")
            des = soup.find('div', class_="a-section a-spacing-medium a-spacing-top-small").find('ul').find_all('li')
            try:
                for i in des:
                    description = description + " " + i.text.strip()
            except: pass
            try:
                tokens = word_tokenize(description)
                tokens = [t for t in tokens if not t.lower in stopwords.words("english")]
                porter = nltk.PorterStemmer()
                porter_tokens = [porter.stem(t) for t in tokens]
                words = [w.lower() for w in porter_tokens]
                description_tokens = sorted(set(words))
            except: print("tokenization error")
            writer.writerow([company, name, price, link, image, description_tokens])
        except:
            description = 'description not found'
            description_tokens = tokenize(description)
            writer.writerow([company, name, price, link, image, description_tokens])
    return n

def search_costco(search):
   
    url=get_costco_url(search)
    html_text=requests.get(url, headers = headers)
    soup=bs(html_text.content,"lxml")
    soup=bs(soup.prettify(),"lxml")
    items=soup.find_all('div', class_="col-xs-6 col-lg-4 col-xl-3 product")
    n = len(items)
    print("costco got "+str(n)+" results")
    for count,item in enumerate(items):
        description = ""
        try:
            image = item.find('img', class_="img-responsive")['src']
        except:
            image = None
        company = "costco"
        link = item.find('span', class_="description").a['href']
        name = item.find('span', class_="description").text.strip()
        try:
            try:
                price = item.find('div', class_="price").text.strip()
                pi = price.find('$')
                price = float(price[pi+1:].strip())
            except:
                price = None
            url = link
            html_text=requests.get(url, headers = headers)
            soup=bs(html_text.content,"lxml")
            soup=bs(soup.prettify(),"lxml")
            des = soup.find('div', class_="features-container form-group").find('ul').find_all('li')
            try:
                for i in des:
                    description = description + " " + i.text.strip()
            except: pass
            try:
                tokens = word_tokenize(description)
                tokens = [t for t in tokens if not t.lower in stopwords.words("english")]
                porter = nltk.PorterStemmer()
                porter_tokens = [porter.stem(t) for t in tokens]
                words = [w.lower() for w in porter_tokens]
                description_tokens = sorted(set(words))
            except: print("tokenization error")
            writer.writerow([company, name, price, link, image, description_tokens])
        except:
            description = "description not found"
            description_tokens = tokenize(description)
            writer.writerow([company, name, price, link, image, description_tokens])
    return n

def search_ebay(search):
    
    url=get_ebay_url(search)
    html_text=requests.get(url, headers = headers)
    soup=bs(html_text.content,"lxml")
    soup=bs(soup.prettify(),"lxml")
    items = soup.find_all('li',class_="s-item s-item__pl-on-bottom")
    n = len(items)
    print("ebay got " + str(n)+" results")
    for count,item in enumerate(items):
        description = ""
        try:
            image = item.find('img')['src']
        except: image = None
        company = "ebay"
        namel = item.find('div', class_="s-item__title").find('span', role = "heading").text.split()
        if namel[0] == 'New':
            name = ' '.join(namel[2:])
        else:
            name = ' '.join(namel)
        link = item.find('a', class_="s-item__link")['href']
        try:
            try:
                price = item.find('span', class_ = "s-item__price").text.strip()
                pi = price.find('$')
                price = float(price[pi+1:].strip())
            except:
                price = None
            url = link
            html_text=requests.get(url, headers = headers)
            soup=bs(html_text.content,"lxml")
            soup=bs(soup.prettify(),"lxml")
            description = soup.find('div', class_="vi-VR-tabCnt").text.strip()
            try:
                tokens = word_tokenize(description)
                tokens = [t for t in tokens if not t.lower in stopwords.words("english")]
                porter = nltk.PorterStemmer()
                porter_tokens = [porter.stem(t) for t in tokens]
                words = [w.lower() for w in porter_tokens]
                words = [w for w in words if not '\n' in w]
                description_tokens = sorted(set(words))
            except: print("tokenization error")
            writer.writerow([company, name, price, link, image, description_tokens])
        except:
            description = "description not found"
            description_tokens = tokenize(description)
            writer.writerow([company, name, price, link, image, description_tokens])
    return n

def search_bjs(search):
    url=get_bjs_url(search)
    html_text=requests.get(url, headers = headers)
    soup=bs(html_text.content,"lxml")
    soup=bs(soup.prettify(),"lxml")
    items =soup.find_all('div', class_="product-cont")
    n = len(items)
    print("bjs got "+str(n)+" results")
    for count,item in enumerate(items):
        description = ""
        try:
            image = item.find('img', class_="img-link")['src']
        except: image = None
        if 'https' not in image:
            image = 'https:'+image
        company = "bjs"
        name = item.find('h2', class_="product-title no-select d-none d-sm-block auto-height").text.strip()
        link = item.find('a', class_="product-link mt-xl-2 mt-xs-3 mt-md-0 mt-3")['href']
        if 'https' not in link:
            link = "https://www.bjs.com"+link
        try:
            try:
                price = item.find('span', class_="price").text.strip()
                pi = price.find('$')
                price = float(price[pi+1:].strip())
            except:
                price = None
            url = link
            html_text=requests.get(url, headers = headers)
            soup=bs(html_text.content,"lxml")
            soup=bs(soup.prettify(),"lxml")
            des = soup.find('div', class_="ProductSummary__StyledContent-sc-10i4965-0 bltyAB").find_all('li')
            try:
                for i in des:
                    description = description + " " + i.text.strip()
            except: pass
            try:
                tokens = word_tokenize(description)
                tokens = [t for t in tokens if not t.lower in stopwords.words("english")]
                porter = nltk.PorterStemmer()
                porter_tokens = [porter.stem(t) for t in tokens]
                words = [w.lower() for w in porter_tokens]
                description_tokens = sorted(set(words))
            except: print("tokenization error")
            writer.writerow([company, name, price, link, image, description_tokens])
        except:
            description = "description not found"
            description_tokens = tokenize(description)
            writer.writerow([company, name, price, link, image, description_tokens])
    return n

#Taking the product name and inserting the data into csv file
print("Enter the product you want to search: ")
search = input()
ac = search_amazon(search)
cc = search_costco(search)
bc = search_bjs(search)
ec = search_ebay(search)
f.close()
total = ac + cc + bc + ec
print("Total number of results are: " +  str(total))


def similarity_computation():
    corpus=[]

    #Taking the query from user and appending query into corpus
    q = input("Enter the query: ")
    tokens = word_tokenize(q)
    tokens = [t for t in tokens if not t.lower in stopwords.words("english")]
    porter = nltk.PorterStemmer()
    porter_tokens = [porter.stem(t) for t in tokens]
    words = [w.lower() for w in porter_tokens]
    qlist = sorted(set(words))
    q = ' '.join(qlist)
    print("query is: "+ q)

    corpus.append(q)

    #Appending the corpus with all product descriptions
    df=pd.read_csv("/*Path of your current working directory*/results.csv")
    des_col=df["description_tokens"]
    print("number of tuples in description column are: "+str(len(des_col)))
    for kcount,i in enumerate(des_col):
        i = i.split(",")
        for count,w in enumerate(i):
            wd=""
            for c in w:
                if c.isalnum():
                    wd = wd + c
            i[count] = wd
        #des_col[kcount] = i
        s_each = ' '.join(i)
        corpus.append(s_each)


    #Calculating TFIDF values of corpus
    tfidf = TfidfVectorizer()
    result = tfidf.fit_transform(corpus)
    
    """
    #Uncomment below piece of code to check 'idf' values of bag of words among all documents
    print('\nidf values:')
    for ele1, ele2 in zip(tfidf.get_feature_names_out(), tfidf.idf_):
        print(ele1, ':', ele2)

    #Uncomment below piece of code to check cosine similarities of all documents
    cosine_sim = cosine_similarity(result)
    print(cosine_sim)
    """
    
    #Computing similarity matrix for all products with respect to user query
    sim_matrix = cosine_similarity(result[0:1],result)
    #print(sim_matrix)
    #print("length of cosine similarity results row is: "+str(len(sim_matrix[0])))


    #Inserting similarity index value of each product into the csv file
    sim_list = sim_matrix[0][1:]
    for count,j in enumerate(sim_list):
        df.loc[count,'sim_index'] = j
    df.to_csv("/*Path of your current working directory*/results.csv", index=False)


    #Sorting the similarity index in decreasing order and saving to CSV file
    df.sort_values(["sim_index"],
                        axis=0,
                        ascending=[False],
                        inplace=True)
    df.to_csv("/*Path of your current working directory*/results.csv", index=False)
    print('csv file updated successfully!')

    with open('/*Path of your current working directory*/results.csv') as f:
        # Create a CSV reader object
        reader = csv.reader(f)
        # Skip the header row
        next(reader)
        # Initialize the HTML table
        table_html = '<table>'
        headers = ['Image', 'Name', 'Company', 'Price', 'Link']  # Add new column header
        header_row = '<tr>' + ''.join([f'<th>{header}</th>' for header in headers]) + '</tr>'
        table_html += header_row
        # Loop over the rows in the CSV file
        for row in reader:
            # Get the name and image URL from the row
            company = row[0]
            name = row[1]
            price = row[2]
            link = row[3]
            image_url = row[4]
            # Create the HTML for the row
            row_html = f'<tr><td><img src="{image_url}" class="thumbnail"></td><td>{name}</td><td>{company}</td><td>{price}</td><td><a href="{link}">Link</a></td></tr>'
            # Add the row HTML to the table HTML
            table_html += row_html
        # Close the table tag
        table_html += '</table>'


    # Create the full HTML page
    html = f'''
    <html>
    <head>
    <style>
    table {{
      border-collapse: collapse;
      width: 100%;
    }}

    td, th {{
      border: 1px solid #dddddd;
      text-align: left;
      padding: 8px;
    }}

    tr:nth-child(even) {{
      background-color: #dddddd;
    }}

    .thumbnail {{
      max-width: 100px;
      max-height: 100px;
    }}
    </style>
    </head>
    <body>
    {table_html}
    </body>
    </html>
    '''

    # Write the HTML page to a file
    with open('/ *Path of your current working directory*/output.html', 'w') as f:
        f.write(html)
    print("html file updated successfully!!")



similarity_computation()
while(True):
    o = input("Do you want to try again with one more Query: y/n: ")
    if o == 'y':
        similarity_computation()
    else:
        print('Hope you like our project')
        break
