# Python Web Crawler and ETL: IMDB Data Analysis
# Summary: 

The following repository contains 9 Jupyter notebooks of Python code implementing ETL, data cleaning, and data analysis. More specifically, the Python scripts implement the following: web crawlers, data visualizations, data analyses (e.g., exploratory data analysis, inferential analysis such as OLS regression analysis), and importing the data collected by the web crawler into a postgreSQL database via the SQLAlchemy and Pandas libraries. 

Part I is shown directly in the main branch, while the other 8 files are uploaded to one of 3 folders, for ease of reference. 

## There are 2 or more components to each notebook- web crawlers, data cleaning, data exploration, etc.: 

1.) Web crawler for extracting data: Several of the notebooks start with a Python Web crawler using the beautifulsoup library. The web crawler extracts one or more pages of film data from the IMDB database, including a for loop in several of the notebooks iterating over several variables such as metascores, IMDB ratings, and/or the number of IMDB votes for a given film. 

2.) Data cleaning, wrangling, and merging data: Data cleaning procedures include using the regex library to clean data into a format suitable for data analysis; converting strings to binary data using the Pandas library; and merging data from different datasets that were extracted froom sites such as via the IMDB, and cultivated via the code I implemented.

3.) Data exploration: Each of the notebooks also engage in some combination of exploratory data analysis, summary statistics, and/or other data analysis such as linear regression plots using the Pandas, matplotlib, seaborn, and Statsmodels libraries.

For example, one of the Jupyter notebooks saved on this  repository--navigate to the "Parts_II-III_1936-2017_analysis" folder, and open the notebook entitled "Python_Web_crawler_IMDB_Part_II_1936-2017_analysis.ipny"--implements a web crawler that extracts the first 5 pages of IMDB films data for each year during a long range (e.g., 1936-2017) from the IMDB site, collecting data for variables such as metascores, IMDB ratings, etc. 

To specifically demonstrate some of the web crawlers, let's start with the simplest: the following code implements the web crawler for a single page of IMDB films; it extracts data for the top-250 films in terms of IMDB ratings (N.B.: the IMDB web site can contain a maximum of 250 films on each page):

## Code for webcrawler and ETL of top-250 IMDB films webpage:

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


'''Create a for loop to iterate over each of the 250 films' data contained within the film_html object.
Start by creating empty lists, which will contain the data for each of the variables:'''

#Empty lists to contain the data for each variable
year_released = []
names = []
genres = []
lengths = []
imdb_ratings = []
n_votes = []
metascores=[]

#Extract data for each of the films contained within the HTMl object/container
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

After importing the requests and Beautifulsoup libraries, a beautifulsoup object was initialized, and the first 1000 characters of the webpage's source HTML code was displayed to convey a sense of the HTML code. The beautifulsoup object enables Python to parse the data contained within HTML objects from the site that Python has gained access to via the requests library.

Empty lists were then defined for each variable (eg., the year of the film's release, film names, film lengths), so that the data for each variable, which was extracted via the web crawler, would be apended into each list for each film (i.e., each row represents a particular film). 

After specifying the empty lists, a for loop was spcified. The for loop iterates on the HTML class object containing each film's data--which is contained within a span class called "lister-item-year"--implements the web crawler. The data for several specific variables is then extracted from each film on the web page, and this parsed data is then appended to each list. 

In several cases, the data are cleaned via list comprehension and other methods, such as deleting any empty space from certain variables, splicing on the data, regex, or converting the data to integer. For example, on the IMDB web site, genres are sometimes listed with more than a single description: as one example from the site: "Drama | Romance". While it might be interesting to examine these detailed genre listings, it's far more practical to parse the first genre listed for each film. Presumably, the first genre is the primary genre for each film. Conseuqently, parsing on this allows the data to be cleaned and imported as a categorical column rather than a messy mish-mash of different genres. 

for example, the metascore data clearly should be changed to integer, so the data for this variable was converted from the initial string data that's extracted to integer using the int() method.

## Code for 1936-2017 web crawler and ETL:

```
#Set up the number of pages in which data will be extracted from each year: i.e., first 10 pages
page_range = [str(i) for i in range(1,11)]

#Set up the film release date parameter range from the IMDB site: namely, the 87 year period of 1930 to 2017
year_range = [str(i) for i in range(1930,2018)]

#web crawler script:

#Create empty lists to contain the data that will be extracted from the IMDB site
genres = []
lengths = []
names = []
year_released = []
imdb_ratings = []
metascores = []
n_votes = []



#Initialize monitoring of the web crawler loop
start_t = time()
requests = 0

#Implement loop for each year in the range of 1938 to 2018
for year in year_range:

    # For each page from 1-10 for each given year
    for page in page_range:

        #implement a URL get request 
        response = get('http://www.imdb.com/search/title?release_date=' + year + 
        '&sort=num_votes,desc&page=' + page, headers = headers)

        #Pause the loop at pseudo-random intervals
        sleep(randint(3,12))

        # Monitor the get requests
        requests += 1
        elapsed_t = time() - start_t
        print('Request:{}; Frequency: {} requests/s'.format(requests, requests/elapsed_t))
        clear_output(wait = True)

        #Show a warning if there is a non-200 status error
        if response.status_code != 200:
            warn('Request: {}; Status code: {}'.format(requests, response.status_code))

        #Stop the loop if the # of requests is larger than was requested/specified
        if requests > 5000:
            warn('Number of requests was greater than expected.')  
            break 

        #Use the bs4 libary to Pparse the HTML content of the URL request
        page_html = BeautifulSoup(response.text, 'html.parser')

        # On each webpage, select the data for each of the 50 films
        films = page_html.find_all('div', class_ = 'lister-item mode-advanced')

        # For each movie of these 50
        for film in films:
            # If the movie has a Metascore, then:

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
                l = l[:3]
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
## Description of the 1936-2017 web crawler code:

Unlike the more straightforward top 250 IMDB web crawler, this web crawler iterates over multiple pages from the IMDB web site, and collects data ONLY for films that have metascore data on record. While the dataset ending up being for 1936-2017, the web crawler was initially set up to refer to 1930-2017. However, so few films released from 1930-1935 had metascores on record that these were essentially outliers, so these years of film data were deleted from the dataset. 

To implement a web crawler over web pages throughout various pages of the IMDB site (i.e., not merely a single webpage), there are 2 additional steps: 1.) 2 variables specifying sranges were specified as separate variables: a.) One of the ranges refers to the specific film release years that the web crawler and ETL loop will iterate on: in this case, for films released from 1930-2017: this range was defined as year_range. b.) The other range refers to the number of pages for each given film release year that the web crawler will be implemented on: this range was specified as page_range. 

2.) The web crawler code is nested within 2 additional for loops: the first for loop refers to the range of years in which the web crawler would search the data for, and the 2nd for loop refers to the range of pages (in this case, the first 10 pages) the web crawler would search for each year in year_range. From there, the web crawler mostly proceeds like that of the Top 250 IMDB web crawler.
