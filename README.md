# Python_Web_Crawler_IMDB_data_analysis
Summary: the following repository contains 9 Jupyter notebooks of Python code; Part I is shown directly in the main branch, while the other 8 files are uploaded to one of 3 folders, for ease of reference. 

There are 2 or more components to each notebook: 

1.) Web crawler for extracting data: Several of the notebooks start with a Python Web crawler using the beautifulsoup library. The web crawler extracts one or more pages of film data from the IMDB database, including a for loop in several of the notebooks iterating over several variables such as metascores, IMDB ratings, and/or the number of IMDB votes for a given film. 

2.) Data cleaning, wrangling, and merging data: Data cleaning procedures include using the regex library to clean data into a format suitable for data analysis; converting strings to binary data using the Pandas library; and merging data from different datasets that were extracted froom sites such as via the IMDB, and cultivated via the code I implemented.

3.) Data exploration: Each of the notebooks also engage in some combination of exploratory data analysis, summary statistics, and/or other data analysis such as linear regression plots using the Pandas, matplotlib, seaborn, and Statsmodels libraries.

For example, one of the Jupyter notebooks--navigate to the "Parts_II-III" folder, and open the notebook entitled "Python_Web_crawler_IMDB_Part_II_1936-2017_analysis.ipny"--saved on this  repository implements a web crawler that extracts the first 5 pages of IMDB films data for each year during a long range (e.g., 1936-2017) from the IMDB site, collecting data for variables such as metascore, IMDB rating, etc. 

