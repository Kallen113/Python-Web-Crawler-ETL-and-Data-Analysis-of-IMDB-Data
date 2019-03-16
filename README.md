# Python Web Crawler and ETL: IMDB Data Analysis
# Summary: 

The following repository contains 9 Jupyter notebooks of Python code implementing web crawlers, data visualizations,
data analyses (e.g., exploratory data analysis, OLS regression analysis), and/or importing the web crawler data into an SQL
database via SQLAlchemy. 

Part I is shown directly in the main branch, while the other 8 files are uploaded to one of 3 folders, for ease of reference. 

## There are 2 or more components to each notebook: 

1.) Web crawler for extracting data: Several of the notebooks start with a Python Web crawler using the beautifulsoup library. The web crawler extracts one or more pages of film data from the IMDB database, including a for loop in several of the notebooks iterating over several variables such as metascores, IMDB ratings, and/or the number of IMDB votes for a given film. 

2.) Data cleaning, wrangling, and merging data: Data cleaning procedures include using the regex library to clean data into a format suitable for data analysis; converting strings to binary data using the Pandas library; and merging data from different datasets that were extracted froom sites such as via the IMDB, and cultivated via the code I implemented.

3.) Data exploration: Each of the notebooks also engage in some combination of exploratory data analysis, summary statistics, and/or other data analysis such as linear regression plots using the Pandas, matplotlib, seaborn, and Statsmodels libraries.

For example, one of the Jupyter notebooks--navigate to the "Parts_II-III" folder, and open the notebook entitled "Python_Web_crawler_IMDB_Part_II_1936-2017_analysis.ipny"--saved on this  repository implements a web crawler that extracts the first 5 pages of IMDB films data for each year during a long range (e.g., 1936-2017) from the IMDB site, collecting data for variables such as metascore, IMDB rating, etc. 

For easy reference, the following code implements the web crawler for a single page of IMDB films; in this case, it extracts data for the top-250 films in terms of IMDB ratings:

## Code for webcrawler of top-250 IMDB films webpage:

```
#import requests library
import requests

#have Python make request to access the IMDB URL that will be used for webscraping
req = requests.get('https://www.imdb.com/search/title?groups=top_250&count=250')


#import bs4 library for parsing on the data from the site's html code
from bs4 import BeautifulSoup

#Define a Beautifulsoup object, which will load the HTML data into Python
soup=BeautifulSoup(req.text, 'html.parser')

#Prints first 1000 characters of the website HTML code
print(soup.text[0:1000])


'''Create a for loop to iterate over each of the 50 films' data contained within the film_html object.
Start by creating empty lists, which will contain the data for each of the variables:'''

#Empty lists to contain the data for each variable
year_released = []
names = []
genres = []
lengths = []
imdb_ratings = []
n_votes = []
metascores=[]

#Extract data for each of the 50 films contained within the HTMl object/container
for film in film_html:
    #only extract data from the HTML code if metascore data are available for the given film
    if film.find('div', class_ = 'ratings-metascore') is not None:
        
        #iterate and extract data for the year the film was released
        y = film.h3.find('span', class_ = 'lister-item-year').text
        #append these data to the year_released list
        year_released.append(y)

        #iterate on the film names
        n = film.h3.a.text
        names.append(n)

        #iterate on genres
        g = film.find('span', class_ = 'genre').text
        #assign to g as a slice to get only the name of genre (i.e., delete first 2 characters)
        g = g[1:]
        #Multiple genres are listed for some films, with commas in between 
        #Thus, split the genres by comma, and then only keep the first element before the first comma
        g = g.split(',')[0]
        #Before appending to genres list, delete any empty space trailing after the actual genre name
        g = g.strip()
        #Having done the needed data cleaning, append the data to the genres list
        genres.append(g)

        #itereate on film lengths
        l = film.find('span', class_='runtime').text
        #slice the data to keep only the characters that actually contain the numeric data
        l = l[:-3]
        #convert the film lengths data from string to integer
        l2 = int(l)
        #append the data to the lengths list
        lengths.append(l2)

        #iterate on IMDB ratings
        i = float(film.strong.text)
        imdb_ratings.append(i)

        #number of IMDB votes
        v = film.find('span', attrs = {'name':'nv'})['data-value']
        n_votes.append(int(v))

        # Scrape the Metascore
        m_score = film.find('span', class_ = 'metascore').text
        metascores.append(int(m_score))
        
```

## Description of the web crawler code:

After importing the requests and Beautifulsoup libraries, a beautifulsoup object was initialized, and the first 1000 characters of the webpage's source HTML code was displayed to convey a sense of the HTML code. The beautifulsoup object enables Python to parse the data/information contained within HTML objects from the site that Python has gained access to via the requests library.

Empty lists were then defined for each variable (eg.., the year of the film's release, film names, lengths), so that the data for each variable, which was extracted via from the web crawler, would be apended into each list for each film. 

After specifying the empty lists, a for loop was spcified. The for loop iterates on the HTML class object containing each film's data--which is contained within a span class called "lister-item-year"--implements the web crawler. The data for several specific variables is then extracted from each film on the web page, and this parsed data is then appended to each list. 

In several cases, the data are cleaned via list comprehension and other methods, such as deleting any empty space from certain variables, splicing on the data, or converting the data to integer (e.g., the metascore data clearly should be changed to integer, so the data for this variable was converted from the initial string data that's extracted to integer using the int() method).
