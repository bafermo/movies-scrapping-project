# Basic web scrap project
## IMDB movies you must watch
<img src = "https://jeremymattheiss.files.wordpress.com/2017/07/jaws-logo.png?w=640">
<br>

__Importing libraries__


```python
from requests import get
import pandas as pd
from bs4 import BeautifulSoup
```

__Defining URL to be scrapped__


```python
url = "https://www.imdb.com/list/ls002448041/"
```

__Getting page from URL and transforming into indexable html string with BeautifulSoup library__


```python
response = get(url)
text1 = response.text
```


```python
html_soup = BeautifulSoup(text1,'html.parser')
```

__Defines specific section and class where movies information are__


```python
movie_detail = html_soup.findAll('div',class_='lister-item mode-detail')
```

__Inserts through a for loop each value into a correspondent list__
<br>
Note that each variable (title, year, etc) has a different path to be found on the HTML structure. It's a matter of trying and find where each interest string is kept on and then translate it into Find/FindAll code.


```python
titles = []
years = []
age_restriction = []
directors = []
revenues = []
genres = []
    
for i in movie_detail:

    try:    
        #title
        title = i.findAll('a')[1].text.strip()
        titles.append(title)
        
        #year
        year = i.findAll('span')[1].text.strip()
        year = year.replace('(','').replace(')','').replace('I','').replace(' ','')
        years.append(year)
        
        #director
        director = i.findAll('p')[2].find('a').text.strip()
        directors.append(director)
        
        #age
        age = i.find('p').find('span').text.strip()
        age = age.replace("min",'').replace('Livre','None')
        age_restriction.append(age)
        
        #revenue
        revenue = i.findAll('p')[3].findAll('span')[4].text
        revenue = revenue.replace('$','').replace('M','')
        revenues.append(revenue)
        
        #genre
        genre = i.findAll('span')[6].text.replace('/n','').strip()
        genres.append(genre)
        
    except IndexError:
        
        #Include None when gross revenue is missing
        
        revenues.append(None)      
        
```

__Treating diferent indexes on HTML__
<br>
Because of the specific HTML structure of this page, 2 movies had not the genre and revenue information place into the same index. <br>
So, I've made pecific and pontual corrections here. However, it should be noted that that for bigger amounts of data (and indexes inconsistency), there are better alternatives to solve it


```python

genres.insert(14, movie_detail[14].findAll('span')[6].text.replace('/n','').strip())
genres.insert(39, movie_detail[40].findAll('span')[4].text.replace('/n','').strip())

```

__Creating pandas DataFrame to include lists__


```python

data = pd.DataFrame({'movie_title': titles, 
                     'release_year': years,
                     'director': directors,
                     'gross_revenue': revenues,
                     'age_restriction': age_restriction,
                     'genre': genres})

data.tail(20)
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>movie_title</th>
      <th>release_year</th>
      <th>director</th>
      <th>gross_revenue</th>
      <th>age_restriction</th>
      <th>genre</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>80</th>
      <td>Apocalypto</td>
      <td>2006</td>
      <td>Mel Gibson</td>
      <td>50.87</td>
      <td>16</td>
      <td>Action, Adventure, Drama</td>
    </tr>
    <tr>
      <th>81</th>
      <td>Os Infiltrados</td>
      <td>2006</td>
      <td>Martin Scorsese</td>
      <td>132.38</td>
      <td>18</td>
      <td>Crime, Drama, Thriller</td>
    </tr>
    <tr>
      <th>82</th>
      <td>Pequena Miss Sunshine</td>
      <td>2006</td>
      <td>Jonathan Dayton</td>
      <td>59.89</td>
      <td>14</td>
      <td>Comedy, Drama</td>
    </tr>
    <tr>
      <th>83</th>
      <td>Onde os Fracos Não Têm Vez</td>
      <td>2007</td>
      <td>Ethan Coen</td>
      <td>74.28</td>
      <td>16</td>
      <td>Crime, Drama, Thriller</td>
    </tr>
    <tr>
      <th>84</th>
      <td>Batman: O Cavaleiro das Trevas</td>
      <td>2008</td>
      <td>Christopher Nolan</td>
      <td>534.86</td>
      <td>12</td>
      <td>Action, Crime, Drama</td>
    </tr>
    <tr>
      <th>85</th>
      <td>Quem Quer Ser um Milionário?</td>
      <td>2008</td>
      <td>Danny Boyle</td>
      <td>141.32</td>
      <td>16</td>
      <td>Drama, Romance</td>
    </tr>
    <tr>
      <th>86</th>
      <td>Se Beber, Não Case!</td>
      <td>2009</td>
      <td>Todd Phillips</td>
      <td>277.32</td>
      <td>14</td>
      <td>Comedy</td>
    </tr>
    <tr>
      <th>87</th>
      <td>Se Beber, Não Case! Parte II</td>
      <td>2011</td>
      <td>Todd Phillips</td>
      <td>254.46</td>
      <td>16</td>
      <td>Comedy</td>
    </tr>
    <tr>
      <th>88</th>
      <td>Um Parto de Viagem</td>
      <td>2010</td>
      <td>Todd Phillips</td>
      <td>100.54</td>
      <td>14</td>
      <td>Comedy, Drama</td>
    </tr>
    <tr>
      <th>89</th>
      <td>Avatar</td>
      <td>2009</td>
      <td>James Cameron</td>
      <td>760.51</td>
      <td>12</td>
      <td>Action, Adventure, Fantasy</td>
    </tr>
    <tr>
      <th>90</th>
      <td>Distrito 9</td>
      <td>2009</td>
      <td>Neill Blomkamp</td>
      <td>115.65</td>
      <td>14</td>
      <td>Action, Sci-Fi, Thriller</td>
    </tr>
    <tr>
      <th>91</th>
      <td>Bastardos Inglórios</td>
      <td>2009</td>
      <td>Quentin Tarantino</td>
      <td>120.54</td>
      <td>18</td>
      <td>Adventure, Drama, War</td>
    </tr>
    <tr>
      <th>92</th>
      <td>Cisne Negro</td>
      <td>2010</td>
      <td>Darren Aronofsky</td>
      <td>106.95</td>
      <td>16</td>
      <td>Drama, Thriller</td>
    </tr>
    <tr>
      <th>93</th>
      <td>A Rede Social</td>
      <td>2010</td>
      <td>David Fincher</td>
      <td>96.96</td>
      <td>14</td>
      <td>Biography, Drama</td>
    </tr>
    <tr>
      <th>94</th>
      <td>A Origem</td>
      <td>2010</td>
      <td>Christopher Nolan</td>
      <td>292.58</td>
      <td>14</td>
      <td>Action, Adventure, Sci-Fi</td>
    </tr>
    <tr>
      <th>95</th>
      <td>O Discurso do Rei</td>
      <td>2010</td>
      <td>Tom Hooper</td>
      <td>138.80</td>
      <td>12</td>
      <td>Biography, Drama, History</td>
    </tr>
    <tr>
      <th>96</th>
      <td>O Artista</td>
      <td>2011</td>
      <td>Michel Hazanavicius</td>
      <td>44.67</td>
      <td>12</td>
      <td>Comedy, Drama, Romance</td>
    </tr>
    <tr>
      <th>97</th>
      <td>A Invenção de Hugo Cabret</td>
      <td>2011</td>
      <td>Martin Scorsese</td>
      <td>73.86</td>
      <td>None</td>
      <td>Drama, Family, Fantasy</td>
    </tr>
    <tr>
      <th>98</th>
      <td>Os Descendentes</td>
      <td>2011</td>
      <td>Alexander Payne</td>
      <td>82.58</td>
      <td>12</td>
      <td>Comedy, Drama</td>
    </tr>
    <tr>
      <th>99</th>
      <td>Histórias Cruzadas</td>
      <td>2011</td>
      <td>Tate Taylor</td>
      <td>169.71</td>
      <td>12</td>
      <td>Drama</td>
    </tr>
  </tbody>
</table>
</div>



__Exports data frame into csv__


```python
data.to_csv('MovieList.csv')
```
