# Web_Scraping_IMDB_Most_Popular_Movies
So, if you ask me about the most important skill of this century, I would definitely tell you it’s data literacy. Understanding data is crucial because when you manage to comprehend and make it meaningful, that’s where you stand out.

This project is about scraping the IMDB’s “Most Popular Movies” data by using `requests`, and `BeautifulSoup` modules so let’s start by analyzing what are these two modules:

# <ins>Requests</ins>
The Python requests module is a powerful yet simple tool for sending HTTP requests. It allows you to send all kinds of HTTP requests without the need to manually add query strings to your URLs or to form-encode your POST data.

# <ins>BeautifulSoup (HTML parser)</ins>
BeautifulSoup is a Python library that makes it easy to scrape information from web pages. It turns complicated HTML and XML files into something that’s simple to work with in Python. You can easily find and work with specific parts of a web page, like text, links, or tags, using BeautifulSoup. It’s great for both beginners and experienced programmers who need to gather data from the internet quickly and easily. In short, it’s like a helpful tool that takes the hard work out of reading and using the content of web pages.

For example, let me give you one example about how we can scrape. So with the following lines of code, we can scrape https://www.reddit.com/ ‘s main page and I only want to extract hyperlinks from this page and the result is:

![image](https://github.com/yavuzCodiin/Web_Scraping_IMDB_Most_Popular_Movies/assets/82445309/bd0c043d-6cff-4d60-9b28-4c9f0be685f9)

If we want to extract the data, first we need to know the basics of html to scrape what we want:
## <ins>Page Structure:</ins>

* `!DOCTYPE html`: Declares the document type.
* `html`: The root element.
* `head`: Contains meta-information about the document.
* `title`: Specifies the title shown in the browser's title bar or tab.
* `body`: Contains the visible page content.

```html
<!DOCTYPE html>
<html>
  <head>
    <title>Title</title>
  </head>
  <body>
    <h1>Heading</h1>
    <p>Paragraph.</p>
  </body>
</html>
```

`Links`: Links are created with <a> tag and href attribute specifies the link’s destination

```html
<a href="https://example.com">Visit Example.com</a>
```

`Parameter`: Parameters, often referred to as attributes, provide additional information about HTML elements. For instance, in the link example, href is a parameter.

`Tables`: Tables are defined with `<table>`, with rows (`<tr>`) and cells (`<td>` for data or `<th>` for headers).

```html
<table>
  <tr>
    <th>Header 1</th>
    <th>Header 2</th>
  </tr>
  <tr>
    <td>Data 1</td>
    <td>Data 2</td>
  </tr>
</table>

```

# <ins>Web Scraping — IMDB’s “Most Popular Movies”</ins>

## <ins>|Importing libraries</ins>

```python
import requests
from bs4 import BeautifulSoup
```
## <ins>|Function to fetch movie data from a given URL using BeautifulSoup</ins>

```python
def fetch_movies(url, headers):
    # Send a GET request to the URL with specified headers
    response = requests.get(url, headers=headers)

    # Parse the response content using BeautifulSoup
    soup = BeautifulSoup(response.content, "html.parser")
    return soup
```
## <ins>|Function to extract movie names and ratings from the parsed HTML content</ins>
If I need to find the name, I check the page structure and find how it is stored then I can scrape it, so here is how we can find it:

![image](https://github.com/yavuzCodiin/Web_Scraping_IMDB_Most_Popular_Movies/assets/82445309/200de62b-48dd-41e9-a3f0-b2875e5c132f)

![image](https://github.com/yavuzCodiin/Web_Scraping_IMDB_Most_Popular_Movies/assets/82445309/cb6131c2-3ad5-47ee-96eb-fb2cee513f23)

```python
def extract_movie_data(soup):
    movie_data = []
    # Find all movie names within 'h3' tags with a specific class
    movie_names = soup.find_all('h3', class_='ipc-title__text')
    # Find all movie ratings within 'span' tags with a specific class
    movie_ratings = soup.find_all('span', class_='ipc-rating-star ipc-rating-star--base ipc-rating-star--imdb sc-9ab53865-1 iXEijC ratingGroup--imdb-rating') 
    
    # Loop through each name and rating, extract and store them
    for name, rating in zip(movie_names, movie_ratings):
        movie_name = name.text.strip()
        movie_rating_str = rating.text.split()[0]
        
        # Convert rating to float and append to movie data; handle conversion errors
        try:
            movie_rating = float(movie_rating_str)
            movie_data.append((movie_name, movie_rating))
        except ValueError:
            print(f"Could not convert rating '{movie_rating_str}' for movie '{movie_name}' to a float.")
    
    return movie_data
```
## <ins>|Function to print sorted movie data based on a minimum rating threshold</ins>

```python
def print_sorted_movies(movies, min_rating):
    # Sort the movies by rating in ascending order
    sorted_movies = sorted(movies, key=lambda x: x[1], reverse=False)

    # Filter and print movies that meet or exceed the minimum rating
    for movie in sorted_movies:
        if movie[1] >= min_rating:
            print(f"Movie Name: {movie[0]}, Rating: {movie[1]}")
```
## <ins>|Main Program Execution</ins>

```python
url = "https://www.imdb.com/chart/moviemeter"
# Headers to mimic a browser request for bypassing potential access restrictions
headers = {
    'User-Agent': 'Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/58.0.3029.110 Safari/537.3'}

# Fetch and parse the HTML content from the given URL
soup = fetch_movies(url, headers)

# Extract movie data from the parsed HTML content
movies = extract_movie_data(soup)

# Get user input for minimum rating and print movies that meet this criterion
min_rating = float(input("Enter the minimum rating (e.g., 7.5): "))
print_sorted_movies(movies, min_rating)}
```

![image](https://github.com/yavuzCodiin/Web_Scraping_IMDB_Most_Popular_Movies/assets/82445309/f0403317-c3d3-46fb-a818-7a2b1c867efe)

![image](https://github.com/yavuzCodiin/Web_Scraping_IMDB_Most_Popular_Movies/assets/82445309/06c49842-cd26-44e1-ad1d-53ee070a9075)

If you want to understand this in a more simpler language you can check my Medium writing published on `Level Up Coding`

LINK => https://levelup.gitconnected.com/web-scraping-series-part-i-imdbs-most-popular-movies-with-requests-and-beautifulsoup-19dfcc0f524a
