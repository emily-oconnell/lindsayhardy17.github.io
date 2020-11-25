[Link to our site](https://lindsayhardy17.github.io/)

# <center>Evaluating the changes in art museums </center>
### <center>Lindsay Hardy and Emily O'Connell </center>

## Goals

For our project, we will be analyzing a [dataset](https://github.com/MuseumofModernArt/collection/blob/master/Artworks.csv) concerning the collection of the [Museum of Modern Art (MoMA)](https://www.moma.org/). The MoMA over the past twenty years has gone through a couple expansions, the first finishing in 2004 and the second finishing in 2019. For our analysis we mainly plan to focus on its changes that started in 2014.

In 2014 MoMA revealed its plans to expand and add 47,000 square feet to its current gallery space. This addition the MoMA said will allow for new and innovative displays and reorganization of the museum. With this expansion they had distinct goals of "diversifying the canon, embracing the present, and showing 1,000 more of its artworks." Through this the curators plan to spotlight the work of women, African American, Asian, and Latino artists who have been previously overlooked in the collection. “We feel that many, many regions that once seemed peripheral don’t seem that way any more, they seem central. Figures who once seemed secondary now seem primary,” said MoMA chief curator Ann Temkin. In order to do this the MoMA has had to actively work over the past years to diversify their collection so that they will be able to accurately meet the goals that they had laid out for themselves. 

When looking at the MoMA dataset, with their goals in mind, our overarching question is exploring whether or not MoMA fulfilled its goal of expanding the diversity of its art collection. In order to see if this shift was reflected in their collection and acquisitions, we will attempt to measure the overall diversity a work adds to the collection by analyzing the relevant aspects of the artwork such as the artist's nationality, the artist’s gender, the year the work was created, the year it was acquired, and various other factors. Although it is impossible to truly quantify the abstract concept of diversity, we believe that analyzing overall patterns in acquisitions will be helpful in determining whether or not MoMA has been successful in its goal of introducing new viewpoints to its collection. 

### The questions that we plan to answer: 
1. Is there an overwhelming number of male artists represented?
2. Has MoMA recently been acquiring artwork from non-American at a higher frequency than in the past?
3. Is the artwork acquired from non-American artists from Western European countries or elsewhere?
4. How does the average age of a work of art change in different time periods? 

By answering all of these smaller questions, we will be able to make a judgement on our overall  research question of assessing the diversity of the collection in recent years. We will also look at the trends over time to see if there are changes in what kind of artwork MoMA typically collects and note any differences between time periods. We have found a rich and detailed dataset with 130,262 works of art owned by MoMA acquired from 1929 to 2017. Although our dataset for the MoMA stops at 2017, but we believe that we will be able to conduct a thourough analysis of their goals, as they laid out their goals for changes in early 2014, so we will have around three years of data to work with and compare with their previous activities. So in all we will have a lot of information pertaining to the type of artwork MoMA has acquired in different time periods.  

### Comparison with other museums
In addition to the thorough analysis we will conduct on MoMA dataset, we also plan to compare the MoMA’s collection to other large modern/contemporary art museums like the [Tate](https://github.com/tategallery/collection), a modern art museum in London. Through a comparison of the Tate and the MoMA we will be able to see if there are any differences in collections and trends between museums in two different countries. The Tate's collection is slightly smaller than MoMA's, only having 70,000 works, and the dataset that we have only representing 69,274 of those works. Because of this when we conduct comparisons between the two datasets we will need to be mindful of the difference in size of the two museums.

We will also investigate the collection of the [Metropolitan Museum of Art](https://github.com/metmuseum/openaccess), another famous art museum in New York City. Even though the Met is not specifically focused on modern art, we will be able to key into its modern/contemporary art collection and only focus specifically on those works. The number of modern/contemporary works that the Met has is much smaller than the MoMAs, only 13,452, but we believe this will still allow us some insight into the differences and similarities between the two museums. By comparing the MoMA to these other museums we will be able to see if they not only reached their goals but if their goals place them as a forward thinker on modern art and not just a part of the crowd. 

[MoMA](https://github.com/MuseumofModernArt), [the Tate](https://github.com/tategallery/collection), and [the Met](https://github.com/metmuseum/openaccess) all have public GitHub repositories and open source data that we took advantage of. 

### Outline
We have laid out our work flow below by starting with cleaning the MoMA data, then conducting individual analysis on it to answer our questions stated above. Then we clean the Tate data and compare it to the data from the MoMA. Afterwards we clean the Met data, and in a similar fashion to how we compared the Tate dataset, we will do the same analysis between the Met and the MoMA. Finally we have a conclusion section at the bottom stating our overall findings and what we believe the analysis has shown us.

### Collaboration Plan
In terms of collaboration, we have set up a [GitHub repository](https://github.com/lindsayhardy17/lindsayhardy17.github.io) and plan for storing the datasets, sharing code, and version control. We intend to meet on Zoom once a week on Tuesdays at 3:10, right after class, and will also be flexible with adding a second time to meet on Thursday after class as well.  During these meetings we plan to check our progress, solve problems, and ask questions, in addition to communicating outside of scheduled meetings as needed.







## MoMA Data Extraction, Cleaning, and Loading

The first thing that we needed to do was unzip our files. We originally tried to pull the data from the github repo, but the MoMA has a no pull policy. So instead we downloaded the data from kaggle.


```python
import pandas as pd
import zipfile
import numpy as np
import re
import datetime
import matplotlib.pyplot as plt
import geopandas as gpd
from scipy import stats
from urllib.request import urlopen
pd.options.mode.chained_assignment = None
```

We unzipped the files and placed them in a working form in our folder


```python
with zipfile.ZipFile('archive.zip', 'r') as zip_ref:
    zip_ref.extractall()
```

Next we read in the Artworks file and the Artists file and created a new dataframe called gender. We will be analyzing the gender and nationality of the artists so we wanted it in our main dataframe. Originally we tried to merge the two dataframes on 'Artist ID', but upon a closer look, "Artist ID" had multiple ids in the artworks dataframe but not in the artists dataframe. Some had up to 5 ids while others only had 1. Because of this we decided to just merge on "Name" because of its consistence across both dataframes. Once we merged we renamed the dataframe "moma". The resulting dataframe is made up of the works title, the artist and their background, the name of the work, information on the medium and different measurement types, when the moma acquired the work, and a couple other minor things. We are looking to analyze the dataframe to find insights on the dates that they were acquired with regard to when the works were made and if there were significant changes in the works acquired after a certain year. We think with this dataset we will be able to do all of these things because of the columns below, but will need to make sure that the columns are in the correct format.


```python
moma_artworks = pd.read_csv("Artworks.csv")
moma_artists = pd.read_csv("Artists.csv")
moma_artists.columns
```




    Index(['Artist ID', 'Name', 'Nationality', 'Gender', 'Birth Year',
           'Death Year'],
          dtype='object')




```python
gender = moma_artists[["Gender", 'Name', 'Nationality', 'Birth Year','Death Year']]
moma = moma_artworks.merge(gender, on = ['Name'], how = 'left')
moma['Gender'] = moma['Gender'].replace({'male':'Male'})
moma.head()
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
      <th>Artwork ID</th>
      <th>Title</th>
      <th>Artist ID</th>
      <th>Name</th>
      <th>Date</th>
      <th>Medium</th>
      <th>Dimensions</th>
      <th>Acquisition Date</th>
      <th>Credit</th>
      <th>Catalogue</th>
      <th>...</th>
      <th>Height (cm)</th>
      <th>Length (cm)</th>
      <th>Width (cm)</th>
      <th>Depth (cm)</th>
      <th>Weight (kg)</th>
      <th>Duration (s)</th>
      <th>Gender</th>
      <th>Nationality</th>
      <th>Birth Year</th>
      <th>Death Year</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>2</td>
      <td>Ferdinandsbrücke Project, Vienna, Austria, Ele...</td>
      <td>6210</td>
      <td>Otto Wagner</td>
      <td>1896</td>
      <td>Ink and cut-and-pasted painted pages on paper</td>
      <td>19 1/8 x 66 1/2" (48.6 x 168.9 cm)</td>
      <td>1996-04-09</td>
      <td>Fractional and promised gift of Jo Carole and ...</td>
      <td>Y</td>
      <td>...</td>
      <td>48.6000</td>
      <td>NaN</td>
      <td>168.9000</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>Male</td>
      <td>Austrian</td>
      <td>1841.0</td>
      <td>1918.0</td>
    </tr>
    <tr>
      <th>1</th>
      <td>3</td>
      <td>City of Music, National Superior Conservatory ...</td>
      <td>7470</td>
      <td>Christian de Portzamparc</td>
      <td>1987</td>
      <td>Paint and colored pencil on print</td>
      <td>16 x 11 3/4" (40.6 x 29.8 cm)</td>
      <td>1995-01-17</td>
      <td>Gift of the architect in honor of Lily Auchinc...</td>
      <td>Y</td>
      <td>...</td>
      <td>40.6401</td>
      <td>NaN</td>
      <td>29.8451</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>Male</td>
      <td>French</td>
      <td>1944.0</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>2</th>
      <td>4</td>
      <td>Villa near Vienna Project, Outside Vienna, Aus...</td>
      <td>7605</td>
      <td>Emil Hoppe</td>
      <td>1903</td>
      <td>Graphite, pen, color pencil, ink, and gouache ...</td>
      <td>13 1/2 x 12 1/2" (34.3 x 31.8 cm)</td>
      <td>1997-01-15</td>
      <td>Gift of Jo Carole and Ronald S. Lauder</td>
      <td>Y</td>
      <td>...</td>
      <td>34.3000</td>
      <td>NaN</td>
      <td>31.8000</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>Male</td>
      <td>Austrian</td>
      <td>1876.0</td>
      <td>1957.0</td>
    </tr>
    <tr>
      <th>3</th>
      <td>5</td>
      <td>The Manhattan Transcripts Project, New York, N...</td>
      <td>7056</td>
      <td>Bernard Tschumi</td>
      <td>1980</td>
      <td>Photographic reproduction with colored synthet...</td>
      <td>20 x 20" (50.8 x 50.8 cm)</td>
      <td>1995-01-17</td>
      <td>Purchase and partial gift of the architect in ...</td>
      <td>Y</td>
      <td>...</td>
      <td>50.8000</td>
      <td>NaN</td>
      <td>50.8000</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>Male</td>
      <td>NaN</td>
      <td>1944.0</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>4</th>
      <td>6</td>
      <td>Villa, project, outside Vienna, Austria, Exter...</td>
      <td>7605</td>
      <td>Emil Hoppe</td>
      <td>1903</td>
      <td>Graphite, color pencil, ink, and gouache on tr...</td>
      <td>15 1/8 x 7 1/2" (38.4 x 19.1 cm)</td>
      <td>1997-01-15</td>
      <td>Gift of Jo Carole and Ronald S. Lauder</td>
      <td>Y</td>
      <td>...</td>
      <td>38.4000</td>
      <td>NaN</td>
      <td>19.1000</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>Male</td>
      <td>Austrian</td>
      <td>1876.0</td>
      <td>1957.0</td>
    </tr>
  </tbody>
</table>
<p>5 rows × 25 columns</p>
</div>



Then we checked on the datatypes, everything looked like it was the right type of object except for the dates, so we first changed Acquisition Date


```python
display(moma.dtypes)
#checked to see if the values are correct, the date and the acquisition date aren't in date time so converting them
moma['Acquisition Date'] = pd.to_datetime(moma['Acquisition Date'], errors = 'coerce')
#changed to datetime
moma.dtypes
```


    Artwork ID              int64
    Title                  object
    Artist ID              object
    Name                   object
    Date                   object
    Medium                 object
    Dimensions             object
    Acquisition Date       object
    Credit                 object
    Catalogue              object
    Department             object
    Classification         object
    Object Number          object
    Diameter (cm)         float64
    Circumference (cm)    float64
    Height (cm)           float64
    Length (cm)           float64
    Width (cm)            float64
    Depth (cm)            float64
    Weight (kg)           float64
    Duration (s)          float64
    Gender                 object
    Nationality            object
    Birth Year            float64
    Death Year            float64
    dtype: object





    Artwork ID                     int64
    Title                         object
    Artist ID                     object
    Name                          object
    Date                          object
    Medium                        object
    Dimensions                    object
    Acquisition Date      datetime64[ns]
    Credit                        object
    Catalogue                     object
    Department                    object
    Classification                object
    Object Number                 object
    Diameter (cm)                float64
    Circumference (cm)           float64
    Height (cm)                  float64
    Length (cm)                  float64
    Width (cm)                   float64
    Depth (cm)                   float64
    Weight (kg)                  float64
    Duration (s)                 float64
    Gender                        object
    Nationality                   object
    Birth Year                   float64
    Death Year                   float64
    dtype: object



While we were checking the different values we realized that the Date, the year the artwork was created, had a lot of different problems and could not be analyzed because of its different date representations, like having 1967-1977, or c. 1989, or early 1992. Because of this we had to fix the Date column, which is shown below. Having this column be correct is very important to us because the date of creation is crucial to our analysis.


```python
moma['Date'].unique()[:100]
#a small sample of the unique types and their differences 
```




    array(['1896', '1987', '1903', '1980', '1976-77', '1968', '1900', '1978',
           '1905', '1906', '1979', '1980-81', '1918', '1970', '1975', '1984',
           '1986', '1974', 'n.d.', 'c. 1917', '1917', '1923', 'Unknown',
           '1930', '1936', '1935', '1937', '1938', '1977', '1958', '1985',
           '1989', '1949', '1958–1964', 'c. 1935', '1991', '1941', '1965',
           '1981', '1983', '1985–1988', 'c. 1989-91', '1992', '1915-17',
           'c. 1915-17', '1953', '1910', 'c.1985', '1982–1986', '1982-86',
           '1945', '1923–1924', '.1-3 1987; .4 1990', '1990', '1976', '1995',
           '1927–1931', 'c. 1929-30', '1964', '1959', 'c. 1918-20',
           'c.1918-1920', '1939', 'c.1976', '1975-79', '1993', '1996', '1988',
           '1982-83', '1982–1983', '1952-53', '1921', '1957', '1972',
           '1956-57', '1924', '1962', '1925', '1960', '1969', '1963', '1994',
           '1961', '1960-61', '1952', 'c. 1978-84', '1927', '1979–1985',
           'before 1933', '1929', 'c. 1960-62', '1967', '1956', 'c. 1961',
           '1934-36', '1981–1982', '1979–1981', '1940', 'after 1938', '1946'],
          dtype=object)




```python
n_d = moma[(moma["Date"] == "nan") | (moma["Date"] == "n.d") | (moma["Date"] == "Unkn") | (moma["Date"] == "Unknown") | (moma["Date"] == "Various") | (moma["Date"] == "unknown")].index
moma.drop(n_d, inplace = True)
```

First we removed the obvious components that were incorrect, but then realized the large extent to which the dataset had issues, so we used regular expressions to extract a date, shown below, and then checked the remaining dates that were not in the correct format that we missed


```python
moma['date_edit'] = moma['Date'].str.extract('(\d{4})', expand = False)
#creating a new column to store the dates
moma[(moma['date_edit'].isnull() == True) & (moma['Date'].isnull() == False)]['Date'].unique()
#checking where the date_edit is null but the Date is true - these will be the remaining dates that could
#not be converted to a correct date format with four numbers
```




    array(['n.d.', '8th-9th century C.E.', '7th-8th century C.E.', 'Unkown',
           '(London?, published in aid of the Comforts Fund  for Women and Children of Sovie',
           '(n.d.)', 'New York', 'November 10', '(19)71', '(19)69',
           'date of publicati', 'nd', 'no date',
           '(newspaper published March 30)', 'n. d.', 'c. 196?', 'TBC', 'TBD'],
          dtype=object)



Because none of these dates had any meaning to us, we could officially switch over to have the date_edit column be our official date column, which we do below


```python
moma['Date'] = moma['date_edit']
moma.drop(columns=['date_edit'], inplace = True)
moma['Date'].unique()
#one last check to make sure that all of our corresponding values are actually dates
```




    array(['1896', '1987', '1903', '1980', '1976', '1968', '1900', '1978',
           '1905', '1906', '1979', '1918', '1970', '1975', '1984', '1986',
           '1974', nan, '1917', '1923', '1930', '1936', '1935', '1937',
           '1938', '1977', '1958', '1985', '1989', '1949', '1991', '1941',
           '1965', '1981', '1983', '1992', '1915', '1953', '1910', '1982',
           '1945', '1990', '1995', '1927', '1929', '1964', '1959', '1939',
           '1993', '1996', '1988', '1952', '1921', '1957', '1972', '1956',
           '1924', '1962', '1925', '1960', '1969', '1963', '1994', '1961',
           '1933', '1967', '1934', '1940', '1946', '1955', '1997', '1922',
           '1942', '1954', '1916', '1973', '1926', '1932', '1947', '1943',
           '1944', '1966', '1971', '1999', '1951', '1913', '1928', '1886',
           '1920', '1950', '1931', '1901', '1948', '1912', '1908', '1902',
           '1904', '1998', '1898', '1875', '1880', '1909', '1501', '1897',
           '1907', '1895', '1914', '1885', '1768', '1878', '1808', '1865',
           '1899', '1876', '1873', '1860', '1866', '1919', '1830', '1840',
           '1884', '1883', '2000', '1894', '1893', '1879', '1890', '1892',
           '1877', '1911', '1891', '1889', '1818', '1852', '1837', '1825',
           '1828', '1854', '1797', '1799', '1810', '1863', '1816', '1874',
           '1887', '1881', '1882', '1888', '1868', '1871', '1858', '1853',
           '1850', '1855', '1846', '1862', '1856', '1843', '1872', '1861',
           '1864', '1869', '1870', '1857', '1826', '1829', '1859', '1844',
           '1851', '1842', '1845', '1867', '1839', '1838', '1805', '1786',
           '1809', '1849', '1841', '1832', '1811', '1847', '1800', '2001',
           '2002', '2003', '2004', '1821', '2005', '2006', '2011', '2009',
           '2007', '2008', '1848', '2010', '2012', '2013', '2016', '2014',
           '2015', '2017'], dtype=object)



After we officially had all the values as dates we converted them to a date time


```python
# convert the strings back to integers
moma['Date'] = pd.to_datetime(moma['Date'], errors="coerce")
moma['Date'] = moma['Date'].dt.year
```

One of the main things that we will be looking at is when the MoMA acquired the work of art. Without this knowledge we won't be able to see if they really changed following their addition. Because of this we removed works that did not have a date from the original dataframe moma. We decided not to remove the date of the creation of art for right now because we believe those works could potentially be useful.


```python
# dropped dates that didn't have an acquisition date
no_date = moma[(moma['Acquisition Date'].isnull() == True)].index
moma.drop(no_date, inplace = True)
```

After cleaning the data we began to add in columns that we would utilize in our analysis. The first column that we planned on adding was a distinguisher of if the artist was alive when the work was acquired or not.


```python
moma['age_made'] = moma['Date'] - moma['Birth Year']
moma['alive?'] = moma['Date']
```


```python
def alive(acquisition,death):
    if acquisition > death:
        return 'False'
    else:
        return 'True'
moma['Acquisition Year'] = moma['Acquisition Date'].dt.year
moma['alive?'] = moma.apply(lambda row: alive(row['Acquisition Year'],row['Death Year']),axis=1)
```

Next we created a column to determine if the artists was of European or Western descent.


```python
moma['Nationality'].unique()
```




    array(['Austrian', 'French', nan, 'American', 'German', 'Swedish',
           'British', 'Japanese', 'Italian', 'Argentine', 'Swiss',
           'Brazilian', 'Luxembourgish', 'Spanish', 'Dutch', 'Russian',
           'Iranian', 'Finnish', 'Danish', 'Belgian', 'Nationality unknown',
           'Mexican', 'Czech', 'Romanian', 'Polish', 'Cuban', 'Chilean',
           'Puerto Rican', 'Uruguayan', 'Venezuelan', 'Moroccan', 'Colombian',
           'Australian', 'Chinese', 'Yugoslav', 'Hungarian', 'Canadian',
           'Slovenian', 'Latvian', 'Nationality Unknown', 'Various', 'Greek',
           'Haitian', 'Israeli', 'Icelandic', 'Czechoslovakian', 'Croatian',
           'Norwegian', 'Thai', 'Algerian', 'Guatemalan', 'Indian',
           'Ukrainian', 'Irish', 'Costa Rican', 'Korean', 'Ethiopian',
           'Kuwaiti', 'Scottish', 'South African', 'Zimbabwean', 'Portuguese',
           'Panamanian', 'Ecuadorian', 'Peruvian', 'Congolese', 'Malian',
           'Turkish', 'Cambodian', 'Bosnian', 'Canadian Inuit', 'Slovak',
           'Estonian', 'Pakistani', 'Bolivian', 'Taiwanese', 'Paraguayan',
           'Nicaraguan', 'Tunisian', 'Sudanese', 'Tanzanian', 'Guyanese',
           'Senegalese', 'Bahamian', 'Bulgarian', 'Lebanese', 'Kenyan',
           'Nigerian', 'Georgian', 'Egyptian', 'Albanian', 'Azerbaijani',
           'Ivorian', 'Malaysian', 'Singaporean', 'Serbian', 'Lithuanian',
           'Tajik', 'New Zealander', 'Namibian', 'Native American',
           'Ghanaian', 'Afghan', 'nationality unknown', 'Ugandan',
           'Cameroonian', 'Welsh', 'Mauritanian', 'Palestinian', 'Syrian',
           'Saudi Arabian', 'Kazakhstani', 'Rwandan', 'Iraqi', 'Indonesian',
           'Vietnamese', 'Burkinabe', 'Macedonian', 'Kyrgyzstani', 'Filipino',
           'Mozambican', 'Angolan'], dtype=object)



From looking at these unique values we can see that there are a couple of ways to designate if a person had an unknown nationality, which we deal with below. But all the nationalities, if they exhist, are in English and in a correct format.


```python
moma.loc[moma['Nationality'] == 'nan', 'Nationality'] = 'no'
moma.loc[moma['Nationality'] == 'Nationality unknown', 'Nationality'] = 'no'
moma.loc[moma['Nationality'] == 'Nationality Unknown', 'Nationality'] = 'no'
moma.loc[moma['Nationality'] == 'Various', 'Nationality'] = 'no'
moma.loc[moma['Nationality'] == 'nationality unknown', 'Nationality'] = 'no'
moma.loc[moma['Nationality'] == np.nan, 'Nationality'] = 'no'
moma['Nationality'] = moma['Nationality'].fillna('no')
```

In order to tell if the person is European/Western or not, we mad a list of all the nationalities that the MoMA has that are non-European/Western nationalities.


```python
nationalities_moma = ['Japanese',  'Argentine', 
 'Brazilian', 'Russian',
       'Iranian',
       'Mexican', 'Cuban', 'Chilean',
       'Puerto Rican', 'Uruguayan', 'Venezuelan', 'Moroccan', 'Colombian',
       'Australian', 'Chinese', 'Yugoslav', 'Latvian',
       'Haitian', 'Israeli', 'Icelandic', 'Thai', 'Algerian', 'Guatemalan', 'Indian',
       'Ukrainian', 'Costa Rican', 'Korean', 'Ethiopian',
       'Kuwaiti', 'South African', 'Zimbabwean',
       'Panamanian', 'Ecuadorian', 'Peruvian', 'Congolese', 'Malian',
       'Turkish', 'Cambodian', 'Bosnian', 'Canadian Inuit',
       'Estonian', 'Pakistani', 'Bolivian', 'Taiwanese', 'Paraguayan',
       'Nicaraguan', 'Tunisian', 'Sudanese', 'Tanzanian', 'Guyanese',
       'Senegalese', 'Bahamian', 'Lebanese', 'Kenyan',
       'Nigerian', 'Georgian', 'Egyptian', 'Albanian', 'Azerbaijani',
       'Ivorian', 'Malaysian', 'Singaporean', 'Serbian', 'Lithuanian',
       'Tajik', 'New Zealander', 'Namibian', 'Native American',
       'Ghanaian', 'Afghan', 'Ugandan',
       'Cameroonian', 'Welsh', 'Mauritanian', 'Palestinian', 'Syrian',
       'Saudi Arabian', 'Kazakhstani', 'Rwandan', 'Iraqi', 'Indonesian',
       'Vietnamese', 'Burkinabe', 'Macedonian', 'Kyrgyzstani', 'Filipino',
       'Mozambican', 'Angolan']
```

Then we made a function for the MoMA that designated if a person was European/Western or not, created a new column, and placed it in the MoMA dataframe. When deciding whether a country was designated as European/Western or not we refered to a list of European countries from this website: https://datahub.io/opendatafortaxjustice/listofeucountries#resource-listofeucountries


```python
moma['European?'] = ['False'] * len(moma['Nationality'])
```


```python
def nation(nationality):
    if nationality in nationalities_moma:
        return 'False'
    elif nationality == 'no':
        return np.nan
    else:
        return 'True'
moma['European?'] = moma.apply(lambda row: nation(row['Nationality']),axis=1)
moma.loc[moma['Nationality'] == 'no', 'Nationality'] = np.nan
```

After cleaning and adding in extra columns for analysis, we can now create smaller datasets that only contain related columns, with names_dates_moma being the one we will be using most often.


```python
ids = moma[['Artwork ID', 'Artist ID']]
names_dates_moma = moma[['Title', 'Name', 'Date', 'Acquisition Date', 'Artwork ID', 'Gender', 'Credit', 'Nationality','age_made'
                   , 'alive?', 'European?']]
dimensions = moma[['Artwork ID','Dimensions', 'Diameter (cm)',
       'Circumference (cm)', 'Height (cm)', 'Length (cm)', 'Width (cm)',
       'Depth (cm)', 'Weight (kg)', 'Duration (s)']]
credit = moma[['Artwork ID','Credit']]
extra = moma[['Medium', 'Department', 'Classification', 'Artwork ID']]
```


```python
names_dates_moma.head()
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
      <th>Title</th>
      <th>Name</th>
      <th>Date</th>
      <th>Acquisition Date</th>
      <th>Artwork ID</th>
      <th>Gender</th>
      <th>Credit</th>
      <th>Nationality</th>
      <th>age_made</th>
      <th>alive?</th>
      <th>European?</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>Ferdinandsbrücke Project, Vienna, Austria, Ele...</td>
      <td>Otto Wagner</td>
      <td>1896.0</td>
      <td>1996-04-09</td>
      <td>2</td>
      <td>Male</td>
      <td>Fractional and promised gift of Jo Carole and ...</td>
      <td>Austrian</td>
      <td>55.0</td>
      <td>False</td>
      <td>True</td>
    </tr>
    <tr>
      <th>1</th>
      <td>City of Music, National Superior Conservatory ...</td>
      <td>Christian de Portzamparc</td>
      <td>1987.0</td>
      <td>1995-01-17</td>
      <td>3</td>
      <td>Male</td>
      <td>Gift of the architect in honor of Lily Auchinc...</td>
      <td>French</td>
      <td>43.0</td>
      <td>True</td>
      <td>True</td>
    </tr>
    <tr>
      <th>2</th>
      <td>Villa near Vienna Project, Outside Vienna, Aus...</td>
      <td>Emil Hoppe</td>
      <td>1903.0</td>
      <td>1997-01-15</td>
      <td>4</td>
      <td>Male</td>
      <td>Gift of Jo Carole and Ronald S. Lauder</td>
      <td>Austrian</td>
      <td>27.0</td>
      <td>False</td>
      <td>True</td>
    </tr>
    <tr>
      <th>3</th>
      <td>The Manhattan Transcripts Project, New York, N...</td>
      <td>Bernard Tschumi</td>
      <td>1980.0</td>
      <td>1995-01-17</td>
      <td>5</td>
      <td>Male</td>
      <td>Purchase and partial gift of the architect in ...</td>
      <td>NaN</td>
      <td>36.0</td>
      <td>True</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>4</th>
      <td>Villa, project, outside Vienna, Austria, Exter...</td>
      <td>Emil Hoppe</td>
      <td>1903.0</td>
      <td>1997-01-15</td>
      <td>6</td>
      <td>Male</td>
      <td>Gift of Jo Carole and Ronald S. Lauder</td>
      <td>Austrian</td>
      <td>27.0</td>
      <td>False</td>
      <td>True</td>
    </tr>
  </tbody>
</table>
</div>



## Analysis: Trends in the MoMA Dataset

Before we look at other datasets we will conduct analysis on the MoMA by itself, starting with a timeline showing the acquisition of art by the MoMA of female and non-Western/European artist in recent years.

### Has MoMA acquired more diverse (based on gender and nationality) works of art in recent years?


```python
# number of works acquired each year 
acquisitions_per_year = moma.groupby(['Acquisition Year'])["Artwork ID"].count().to_frame().reset_index().rename({"Artwork ID": "num_works_acquired"},axis=1)
```


```python
# proportion of art by female artists for each year
female_per_year = moma[moma.Gender == 'Female'].groupby(['Acquisition Year'])["Artwork ID"].count().to_frame().reset_index()
female_per_year = female_per_year.reset_index()

# perform left merge because some years in female by year are NaN
result = pd.merge(acquisitions_per_year, female_per_year, how='left', on=['Acquisition Year'])

# calculate percent female and assign to df
percent_female = result["Artwork ID"] / acquisitions_per_year["num_works_acquired"]
female_per_year["percent_female"] = percent_female
```


```python
# The proportion of works by female acquired each year
female_per_year = female_per_year.set_index("Acquisition Year")
female_graph = female_per_year["percent_female"].plot(kind='bar', figsize=(16, 5), title = "Proportion of Works by Female Artists Acquired")
female_graph.figure.tight_layout()
female_graph
```




    <AxesSubplot:title={'center':'Proportion of Works by Female Artists Acquired'}, xlabel='Acquisition Year'>




    
![png](output_45_1.png)
    



```python
#number of works by non American artists acquired each year 
nonAmerican_by_year = moma[moma.Nationality != 'American'].groupby(['Acquisition Year'])["Artwork ID"].count().to_frame().reset_index()
# proportion of art by female artists for each year
nonAmerican_by_year["percent_nonAmerican"] = (nonAmerican_by_year["Artwork ID"] / acquisitions_per_year["num_works_acquired"])
```


```python
# The proportion of works by nonAmerican acquired each year
nonAmerican_by_year = nonAmerican_by_year.set_index("Acquisition Year")
na_graph = nonAmerican_by_year["percent_nonAmerican"].plot(kind='bar', figsize=(16, 5), title = "Proportion of Works by nonAmerican Artists Acquired")
na_graph.figure.tight_layout()
na_graph
```




    <AxesSubplot:title={'center':'Proportion of Works by nonAmerican Artists Acquired'}, xlabel='Acquisition Year'>




    
![png](output_47_1.png)
    


<strong>Conclusion: </strong> There is no discernable pattern in the number of diverse works that MoMA acquires each year. However, in the past 20 years, there has been an increase in years that have a high number of works of art by female or non-American artists. It looks like they were more successful in collecting works by non Americans than they were works from female artists.

Next we examine if the MoMA has done what it said it had a goal of doing: bringing the art up to the present.

### Has the MoMA strived to acquire art by more recent artists?


```python
names_dates_moma['acquisitionYear'] = names_dates_moma['Acquisition Date'].dt.year
dead = pd.DataFrame(names_dates_moma[names_dates_moma['alive?'] == 'False'].groupby('acquisitionYear')['alive?'].value_counts())
dead['count'] = dead['alive?']
dead.drop(columns = ['alive?'], inplace = True)
dead = dead.reset_index()
dead.drop(columns = ['alive?'], inplace = True)
dead = dead[dead['acquisitionYear']>1975]
dead['table'] = "dead"
dead_table = dead.pivot(index='acquisitionYear', columns = 'table', values = 'count')

alive = pd.DataFrame(names_dates_moma[names_dates_moma['alive?'] == 'True'].groupby('acquisitionYear')['alive?'].value_counts())
alive['count'] = alive['alive?']
alive.drop(columns = ['alive?'], inplace = True)
alive = alive.reset_index()
alive.drop(columns = ['alive?'], inplace = True)
alive = alive[alive['acquisitionYear']>1975]
alive['table'] = "alive"
alive_table = alive.pivot(index='acquisitionYear', columns = 'table', values = 'count')

#merging the two tables together on acquisitionYear
new = dead_table.merge(alive_table, on = 'acquisitionYear')

#plotting the graph
new.loc[:,['alive','dead']].plot.bar(stacked=True, figsize=(10,7), title = "Acquistion of Living vs Dead Artists Over a 38 year period")
```




    <AxesSubplot:title={'center':'Acquistion of Living vs Dead Artists Over a 38 year period'}, xlabel='acquisitionYear'>




    
![png](output_51_1.png)
    


<strong>Conclusion</strong>: Through this visualization it is evident that over time the MoMA has consistently bought more art by living artists than by deceased artists. But as time went on, starting in 2004, there were more consistent peaks in their purchasing of art, but these purchases still maintained the place of importance of purchasing art from living artists. It shows that they have continued to meet their goal and expand their collection to represent the present.

### Examining National and Regional Diversity


```python
# establish which works are American, Western, or non-Western
# A = American, NA-W = Non-American Western, NWNA - Non-Western Non-American
moma_nation = names_dates_moma[names_dates_moma['Nationality'].notna()]
def nationality(nation):
    if nation == 'American':
        return 'A'
    if nation not in nationalities_moma:
        return 'NA-W'
    else:
        return 'NWNA'
moma_nation['Western/American'] = moma_nation.apply(lambda row: nationality(row['Nationality']),axis=1)
```


```python
# extract year from acquisition date
moma_nation['acquisitionYear'] = moma_nation['Acquisition Date'].dt.year

# make a pivot table of American art
A = pd.DataFrame(moma_nation[moma_nation['Western/American'] == 'A'].groupby('acquisitionYear')['Western/American'].value_counts())
A['count'] = A['Western/American']
A.drop(columns = ['Western/American'], inplace = True)
A = A.reset_index()
A.drop(columns = ['Western/American'], inplace = True)
A = A[A['acquisitionYear']>1975]
A['table'] = "A"
A_table = A.pivot(index='acquisitionYear', columns = 'table', values = 'count')

# make a pivot table of non-American Western art
NAW = pd.DataFrame(moma_nation[moma_nation['Western/American'] == 'NA-W'].groupby('acquisitionYear')['Western/American'].value_counts())
NAW['count'] = NAW['Western/American']
NAW.drop(columns = ['Western/American'], inplace = True)
NAW = NAW.reset_index()
NAW.drop(columns = ['Western/American'], inplace = True)
NAW = NAW[NAW['acquisitionYear']>1975]
NAW['table'] = "NAW"
NAW_table = NAW.pivot(index='acquisitionYear', columns = 'table', values = 'count')

# make a pivot table of non-American, non-Western art
NWNA = pd.DataFrame(moma_nation[moma_nation['Western/American'] == 'NWNA'].groupby('acquisitionYear')['Western/American'].value_counts())
NWNA['count'] = NWNA['Western/American']
NWNA.drop(columns = ['Western/American'], inplace = True)
NWNA = NWNA.reset_index()
NWNA.drop(columns = ['Western/American'], inplace = True)
NWNA = NWNA[NWNA['acquisitionYear']>1975]
NWNA['table'] = "NAW"
NWNA_table = NWNA.pivot(index='acquisitionYear', columns = 'table', values = 'count')

#merging the two tables together on acquisitionYear
new1 = A_table.merge(NAW_table, on = 'acquisitionYear')
new2 = new1.merge(NWNA_table, on = 'acquisitionYear')
new2 = new2.reset_index()

# plot a stacked bar graph of number of works acquired from the 3 demographics
plt.bar(new2['acquisitionYear'], new2['A'], color='r', edgecolor='white', label = 'American')
plt.bar(new2['acquisitionYear'], new2['NAW_x'], color='b', edgecolor='white', label = 'Non-American Western')
plt.bar(new2['acquisitionYear'], new2['NAW_y'], color='g', edgecolor='white', label = 'Non-American Non-Western')

plt.ylabel("Number of Works")
plt.xlabel("Year")
plt.title("Number of works from each demographic by year")
plt.legend()
plt.show()
```


    
![png](output_55_0.png)
    


<strong>Conclusion</strong>: This graph is not quite as straight forward as the ones before. But before the MoMA entered into the early 2000s the amount of art that they acquired from Non-American Non-Western artists was consistently dominated by the amount of art that they acquired from American or Non-American Western artists. As the MoMA entered into the early 2000s, and into 2015, it conssitently aquired more works of art, but at different stages the art that was acquired from Non-American Non-Western artists increased, and in some years topped the number of American and Non-American Western artists, which didn't happen in previous years. From this analysis we see that after the MoMA determind their goals to diversify their cannon in the early 2000s they followed through with that goal and acquired more art from Non-American Non-Western artists than they had done before.

After looking at the distribution over the years of works acquired based on gender, age, and location we now conduct analysis that shows the distribution of countries that the MoMA has art from displayed on a map, utilize a Lorenz and Gini coefficient, and then a linear regression to see how the MoMA's progress compares to a prediction of what is should have been.

## Geographical Analysis

### Comparing the Map of works per country pre-2012 and at the present

Using GeoPandas, we created a maps to show the number of works per country using two different time period: all works before 2012 and all works until present.


```python
# read the natural earth shape file as a GeoDataFrame
# this file is built into geopandas, so there is no need to download it
world = gpd.read_file(gpd.datasets.get_path('naturalearth_lowres'))

# drop Antartica because it takes up too much space
world = world.drop(world.index[159])

#here's the world map we will use
world.plot()
```




    <AxesSubplot:>




    
![png](output_61_1.png)
    


Since the MoMA DataFrame lists nationality and not country of origin, we need to convert the nationalities into the country names, so it can be merged with the existing geopandas world data. We used a demonyms dataset that included nationalities paired with their respective countries. 


```python
demonyms = pd.read_csv("demonyms.csv")
```


```python
# merge the MoMA data with the demonyms data to pair the appropriate country with each nationality
moma_nationalities = pd.merge(demonyms, names_dates_moma, on="Nationality", how='inner')
```


```python
# df of all works before 2012
before_2012 = moma_nationalities[moma_nationalities["acquisitionYear"] <2012]

#get counts of the number of works from each country
count = before_2012["name"].value_counts().astype(int).to_frame().reset_index()
count = count.rename({"index": "name", "name": "count"}, axis=1)

# merge this with the geographic data to graph it and to fill in NaNs for countries not in MoMA
map_pre_2012 = pd.merge(world, count, how="left", on="name")
```


```python
# Plot the pre-2012 Map!

ax1= map_pre_2012.plot(column='count', cmap =    
                                'GnBu', figsize=(15,9),   
                                  k=5, legend =  
                                  True, missing_kwds={'color': 'lightgray'}, scheme="User_Defined", classification_kwds=dict(bins=[1,10,1000,10000,50000],))
ax1.set_title("Number of works per country 1929-2011")
```




    Text(0.5, 1.0, 'Number of works per country 1929-2011')




    
![png](output_66_1.png)
    


Here is the map of the number of works per country before the beginning of the rennovation in 2012. Let's make another map to show the works per country up until the present.


```python
# same process as above, but with all of the artworks
count = moma_nationalities["name"].value_counts().astype(int).to_frame().reset_index()
count = count.rename({"index": "name", "name": "count"}, axis=1)
map_data = pd.merge(world, count, how="left", on="name")
```


```python
# Plot the current Map!

ax = map_data.plot(column='count', cmap =    
                                'OrRd', figsize=(15,9),   
                                  k=5, legend =  
                                  True, missing_kwds={'color': 'lightgray'}, scheme="User_Defined", classification_kwds=dict(bins=[1,10,1000,10000,50000],))
ax.set_title("Number of works per country 1929-2017")

```




    Text(0.5, 1.0, 'Number of works per country 1929-2017')




    
![png](output_69_1.png)
    


These visualizations allow us to see the number of works from each country. As we've seen, North America and Europe are very highly represented, particularly the United States, France, and Germany. On the opposite end of the spectrum, 70 countries are not represented and 17 countries are only represented by one work. The bins vary in size and scale to show how polarized the data are. Scaling by 10,000s or even 1,000s is not very revealing because there are so many countries with very few works, so we chose to define the bins in a way that would most clearly show the inequality between countries.

#### Comparing pre-2012 statistics with the present


```python
map_pre_2012[map_data["count"]<=1].name.count()
```




    17




```python
map_data[map_data["count"]<=1].name.count()

```




    17




```python
# countries in the present dataset that are not in the before 2012 dataset 
set(moma_nationalities.name) - set(before_2012.name)
```




    {'Burkina Faso',
     'Indonesia',
     'Iraq',
     'Kazakhstan',
     'Macedonia',
     'Mozambique',
     'Rwanda',
     'Saudi Arabia',
     'Syria'}



Since 2012, MoMA has added art from 8 new countires, and all of them are non-Western. They did not add any works from conutires that were only represented by one work.

## Lorenz Curve and Gini Coefficient

The [Lorenz Curve](https://en.wikipedia.org/wiki/Lorenz_curve) is typically used to depict distribution of wealth, but it also has helpful applications for showing distribution over time of other variables. In this case, we used it to show the distribution of female artists over time. The x axis represents the cumulative percent of the total population and the y axis represents the cumulative proportion of female artists. In perfect equality, this would be a line with slope of 1 (y=x or x and y increase at the same rate). 

In the example of distribution of wealth, perfect equality would mean that the bottom 10% of households hold 10% of the wealth, the bottom 20% has 20%, etc. In our example, perfect equality would be the first (earliest chronologically) 10% of the all of the artworks has the first 10% of all of the artwork by female artists.

In contrast to perfect equality, the Lorenz curve shows the true distribution of female artists over time, with the same x and y axis as perfect equality.

The Lorenz curve is helpful because it shows a graphical representation of inequality and allows us to calculate the [Gini coefficient,](https://en.wikipedia.org/wiki/Gini_coefficient) which calculates a metric to shows the inequality of the proportion of art by female artists among all artworks. The Gini coefficient is the proportion of the area between perfect equality and the Lorenz curve compared to the total area under the perfect equality line.

This graph is a good representation of perfect equality, the Lorenz curve, and the Gini coefficient.
![image.png](attachment:image.png)

For our axes, since there is not necessarily an analogoue to sorting the population least to most wealthy, we chose to sort the data from earliest acquired to most recetly acquired to show the trend over time. 

#### Lorenz Curve


```python
# sort the data by acquisition date
moma_by_year = moma.sort_values(by=['Acquisition Date'])

# split the DataFrame into 10 (roughly) equal parts to be the 10 deciles (each representing 10% of the population)
# the frist 6 have 13,940 works and the last 4 have 13,939 works
df_split = np.array_split(moma_by_year, 10)
for i in range(0,10):
    
    # add decile number to DataFrame
    df_split[i]["decile"] = i+1
    
    # add decile start and end year to DataFrame
    df_split[i]["dec start year"] = df_split[i]["Acquisition Date"].min().year
    df_split[i]["dec end year"] = df_split[i]["Acquisition Date"].max().year

# combine all of the split DataFrames into one big DataFrame with their corresponding decile number
decile_df = pd.concat([df_split[0],df_split[1],df_split[2],df_split[3],df_split[4],df_split[5],df_split[6],df_split[7],df_split[8],df_split[9]])
decile_df.head()
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
      <th>Artwork ID</th>
      <th>Title</th>
      <th>Artist ID</th>
      <th>Name</th>
      <th>Date</th>
      <th>Medium</th>
      <th>Dimensions</th>
      <th>Acquisition Date</th>
      <th>Credit</th>
      <th>Catalogue</th>
      <th>...</th>
      <th>Nationality</th>
      <th>Birth Year</th>
      <th>Death Year</th>
      <th>age_made</th>
      <th>alive?</th>
      <th>Acquisition Year</th>
      <th>European?</th>
      <th>decile</th>
      <th>dec start year</th>
      <th>dec end year</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>61694</th>
      <td>59708</td>
      <td>Max Reinhardt (Head) (Kopf)</td>
      <td>3197</td>
      <td>Oskar Kokoschka</td>
      <td>1919.0</td>
      <td>Lithograph</td>
      <td>composition (irreg. ): 15 3/16 x 11 15/16" (38...</td>
      <td>1929-11-19</td>
      <td>Gift of Paul J. Sachs</td>
      <td>Y</td>
      <td>...</td>
      <td>Austrian</td>
      <td>1886.0</td>
      <td>1980.0</td>
      <td>33.0</td>
      <td>True</td>
      <td>1929</td>
      <td>True</td>
      <td>1</td>
      <td>1929</td>
      <td>1953</td>
    </tr>
    <tr>
      <th>61744</th>
      <td>59762</td>
      <td>The Lords of the World (Die Herren der Welt )</td>
      <td>5260</td>
      <td>Georg Scholz</td>
      <td>1922.0</td>
      <td>Lithograph</td>
      <td>composition: 11 3/4 x 15 3/4" (29.8 x 40 cm); ...</td>
      <td>1929-11-19</td>
      <td>Gift of Paul J. Sachs</td>
      <td>Y</td>
      <td>...</td>
      <td>German</td>
      <td>1890.0</td>
      <td>1945.0</td>
      <td>32.0</td>
      <td>True</td>
      <td>1929</td>
      <td>True</td>
      <td>1</td>
      <td>1929</td>
      <td>1953</td>
    </tr>
    <tr>
      <th>61711</th>
      <td>59728</td>
      <td>Dialogue (Zwiesprache)</td>
      <td>4533</td>
      <td>Max Pechstein</td>
      <td>1920.0</td>
      <td>Woodcut</td>
      <td>composition: 15 13/16 x 12 9/16" (40.2 x 31.9 ...</td>
      <td>1929-11-19</td>
      <td>Gift of Paul J. Sachs</td>
      <td>Y</td>
      <td>...</td>
      <td>German</td>
      <td>1881.0</td>
      <td>1955.0</td>
      <td>39.0</td>
      <td>True</td>
      <td>1929</td>
      <td>True</td>
      <td>1</td>
      <td>1929</td>
      <td>1953</td>
    </tr>
    <tr>
      <th>61660</th>
      <td>59673</td>
      <td>Woman, Standing in the Garden (Frau, im Garten...</td>
      <td>3197</td>
      <td>Oskar Kokoschka</td>
      <td>1916.0</td>
      <td>Lithograph</td>
      <td>composition (irreg.): 9 13/16 x 5 11/16" (24.9...</td>
      <td>1929-11-19</td>
      <td>Gift of Paul J. Sachs</td>
      <td>Y</td>
      <td>...</td>
      <td>Austrian</td>
      <td>1886.0</td>
      <td>1980.0</td>
      <td>30.0</td>
      <td>True</td>
      <td>1929</td>
      <td>True</td>
      <td>1</td>
      <td>1929</td>
      <td>1953</td>
    </tr>
    <tr>
      <th>61635</th>
      <td>59646</td>
      <td>Marine</td>
      <td>1832</td>
      <td>Lyonel Feininger</td>
      <td>1918.0</td>
      <td>Woodcut</td>
      <td>composition (irreg.): 6 5/8 x 8 7/8" (16.8 x 2...</td>
      <td>1929-11-19</td>
      <td>Gift of Paul J. Sachs</td>
      <td>Y</td>
      <td>...</td>
      <td>American</td>
      <td>1871.0</td>
      <td>1956.0</td>
      <td>47.0</td>
      <td>True</td>
      <td>1929</td>
      <td>True</td>
      <td>1</td>
      <td>1929</td>
      <td>1953</td>
    </tr>
  </tbody>
</table>
<p>5 rows × 32 columns</p>
</div>




```python
# find the total number of female artists in each decile
female_list = []
females = decile_df[decile_df["Gender"] == "Female"]
for decile_num in range(1,11):
    female_list.append(females[females["decile"] == decile_num]["Acquisition Date"].count())
```


```python
# make it cumulative!
total = 0
female_count = [0]
for i in range(10):
    total += female_list[i]
    female_count.append(total)
```


```python
# since we need a cumulative population, keep a list of the current population being examined at each decile

total = 0
# include 0% of the population in the list of each decile's cumulative population count
pop_count = [0]
for i in range(10):
    total += df_split[i]["Acquisition Year"].count()
    pop_count.append(total)
```

Since the Gini index depends on cumulative proportions and not counts, we need to find the total proportion of female artists for each cumulative decile. This requires us to divide the number of female artists in each cumulative decile by the total female population.

It will suffice to simply create a list of each decile value because the difference in their size is negligible.


```python
# divide female count by total population of female artists to get the proportion of female artists in each decile
total_female = females["Acquisition Year"].count()
female_prop = list(map(lambda x: (x/total_female) *100, female_count))

# create a list of all of the cumulative proportions of population (make sure to include 0 and 100)
population_prop = [0,10,20,30,40,50,60,70,80,90,100]
```


```python
# create a DataFrame with these values representing the x and y axis of the Lorenz curve
lorenz_curve = pd.DataFrame(list(zip(population_prop, female_prop)),columns =['cumulative_prop_of_artists', 'cumulative_proportion_of_female_artists'])
```


```python
# add the start and end years of each decile to the DataFrame
for i in range(0,10):
    decile_df[decile_df["decile"] == i]["dec start year"]
    decile_df[decile_df["decile"] == i]["dec end year"]
    

# create an array of the each unique start and end year to the respective list, including 0 for 0% of the population
start_years = list(decile_df["dec start year"].unique())
start_years.insert(0,0)

end_years = list(decile_df["dec end year"].unique())
end_years.insert(0,0)
start_years
```




    [0, 1929, 1953, 1964, 1968, 1974, 1986, 1998, 2005, 2009, 2013]




```python
# add this to a new DataFrame
lorenz_year = lorenz_curve.copy()
lorenz_year["decile start"] = start_years
lorenz_year["decile end"] = end_years
lorenz_year["date range"] = ["0-0", "1929-1953", "1953-1964", "1964,1968", "1968-1974", "1974-1986","1986-1998", "1998-2005", "2005-2009", "2009-2013", "2013-2016"]
lorenz_year
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
      <th>cumulative_prop_of_artists</th>
      <th>cumulative_proportion_of_female_artists</th>
      <th>decile start</th>
      <th>decile end</th>
      <th>date range</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>0</td>
      <td>0.000000</td>
      <td>0</td>
      <td>0</td>
      <td>0-0</td>
    </tr>
    <tr>
      <th>1</th>
      <td>10</td>
      <td>4.831752</td>
      <td>1929</td>
      <td>1953</td>
      <td>1929-1953</td>
    </tr>
    <tr>
      <th>2</th>
      <td>20</td>
      <td>8.083892</td>
      <td>1953</td>
      <td>1964</td>
      <td>1953-1964</td>
    </tr>
    <tr>
      <th>3</th>
      <td>30</td>
      <td>11.833809</td>
      <td>1964</td>
      <td>1968</td>
      <td>1964,1968</td>
    </tr>
    <tr>
      <th>4</th>
      <td>40</td>
      <td>17.773943</td>
      <td>1968</td>
      <td>1974</td>
      <td>1968-1974</td>
    </tr>
    <tr>
      <th>5</th>
      <td>50</td>
      <td>26.866662</td>
      <td>1974</td>
      <td>1986</td>
      <td>1974-1986</td>
    </tr>
    <tr>
      <th>6</th>
      <td>60</td>
      <td>45.503418</td>
      <td>1986</td>
      <td>1998</td>
      <td>1986-1998</td>
    </tr>
    <tr>
      <th>7</th>
      <td>70</td>
      <td>58.711090</td>
      <td>1998</td>
      <td>2005</td>
      <td>1998-2005</td>
    </tr>
    <tr>
      <th>8</th>
      <td>80</td>
      <td>76.431937</td>
      <td>2005</td>
      <td>2009</td>
      <td>2005-2009</td>
    </tr>
    <tr>
      <th>9</th>
      <td>90</td>
      <td>90.960377</td>
      <td>2009</td>
      <td>2013</td>
      <td>2009-2013</td>
    </tr>
    <tr>
      <th>10</th>
      <td>100</td>
      <td>100.000000</td>
      <td>2013</td>
      <td>2016</td>
      <td>2013-2016</td>
    </tr>
  </tbody>
</table>
</div>



Here is a dataframe with the cumulative proportion of artists, the cumulative proprtion of female artists, the decile start date, decile end date, and the date range. This provides some context for each decile. 


```python
# plot the Lorenz curve
fig = plt.figure()
ax = fig.add_subplot(1,1,1)
ax.plot(lorenz_curve)
ax.set_xlabel("cumulative proportion of population")
ax.set_ylabel("cumulative proportion of female artists")
ax.set_title("Lorenz Curve and Perfect Equality line")
```




    Text(0.5, 1.0, 'Lorenz Curve and Perfect Equality line')




    
![png](output_88_1.png)
    


Now we have a graph showing perfect equality (blue line) the Lorenz curve (orange line). The area in between the two lines represents the inequality between the two. The gap decreases in more recent years, showing that there is a more equal distribution of works by female artists in later years, particularly starting in the 8th decile (all works until 2009). Interestingly, in the 9th decile (all works until around 2015) the Lorenz curve actually slightly surpasses perfect equality. This means that the earliest 90% of all artworks contains approximately 91% of the all the works by female artists.

#### Gini Coefficient

To find the Gini Coefficient, we need to integrate the Lorenz curve and the perfect equality line to get the area under the Lorenz curve and the area under the perfect equality line respectively. We then use those values to find the Gini Coefficient.
Mathematically, this is:

Gini Coefficient =  $\frac{\int Perfect Equality Line - \int Lorenz Curve}{\int Perfect Equality Line}$


```python
# calculate the area under the Lorenz curve
lorenz_area = np.trapz(female_prop, x=population_prop)

# calculate the area under the perfect equality line (y=x)
pe_area = np.trapz(population_prop, x=population_prop)

# use those values to calculate the gini coefficient
gini_coeff = (pe_area - lorenz_area) / pe_area
gini_coeff
```




    0.2180062388000266



We found a Gini coefficient of 21.8. The closer a Gini coefficient is to 0, the closer the data are to perfect equality.

This means there is 21.8 percent concentration of works by female artists in the dataset over time, so works by female artists are generally acquired at lower rate than works are acquired in the general population. However, the graph of the Lorenz curve surpasses perfect equality around the 90th decile, so we can concluded that MoMA has acquired a higher proportion of works by female artists in more recent years.

It is important to remember that the Gini coefficient does not directly compare the percentage of female artists among all artists, but rather shows what percent of the art by female artists were acquired in each decile to show distribution over time

## Linear regression predicting proportion of art by female artists

We used scikit learn to run a linear regression on the data in two different sets:  all of the data and only data before 2012 to see if the model could accurately predict the proportion of female artists.

First, we looked at the regression for all of the data.


```python
# get counts for acquisitions each year
acquisitons_per_year = moma.groupby(['Acquisition Year']).count()

# proportion of art by female artists for each year
female_proportion_by_year = female_per_year["percent_female"]

# create df of year and number of acquisitions
acquisitons_per_year = moma.groupby(['Acquisition Year'])["Artwork ID"].count().to_frame().reset_index().rename({"Artwork ID": "num_works_acquired"},axis=1)

# get a df of year and percent female
all_data = pd.merge(acquisitons_per_year, female_proportion_by_year, on="Acquisition Year").fillna(0)
```


```python
x = all_data["Acquisition Year"]
y = all_data["percent_female"]

slope, intercept, r_value, p_value, std_err = stats.linregress(x,y)
plt.figure(figsize=(20,10))
plt.plot(x, y, 'o')
plt.plot(x, slope*x + intercept, '#EB6E1F')
plt.title("1929-2016")

x_list = x.tolist()
y_list = y.tolist()

label = all_data["Acquisition Year"]
for i, txt in enumerate(label):
    plt.annotate(txt, (x_list[i], y_list[i]), 
                xytext=(10,5), 
                textcoords='offset points')
    
plt.xlabel("year")
plt.ylabel("percent_female")

plt.show()

# put summary stats into a df column
summary_frame = pd.DataFrame(index=["slope", "intercept", "r_value", "p_value", "std_err"])
summary_stats = ["{:.7f}".format(slope),"{:.7f}".format(intercept),"{:.7f}".format(r_value),"{:.7f}".format(p_value), "{:.7f}".format(std_err)]
summary_frame["summary stats"] = summary_stats
summary_frame
```


    
![png](output_97_0.png)
    





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
      <th>summary stats</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>slope</th>
      <td>0.0024934</td>
    </tr>
    <tr>
      <th>intercept</th>
      <td>-4.8223284</td>
    </tr>
    <tr>
      <th>r_value</th>
      <td>0.6689979</td>
    </tr>
    <tr>
      <th>p_value</th>
      <td>0.0000000</td>
    </tr>
    <tr>
      <th>std_err</th>
      <td>0.0003059</td>
    </tr>
  </tbody>
</table>
</div>



For the next linear regression, we only provided the model with the data up to but not including 2012 to see what it's predictions would be for 2012 onward.


```python
# get only the pre 2012 percent of female artists
pre_2012 = acquisitons_per_year[acquisitons_per_year["Acquisition Year"] < 2012]
pre_2012 = pd.merge(pre_2012, female_proportion_by_year, on="Acquisition Year")
pre_2012.rename(columns={"Acquisition Date" : "percent_female"},inplace=True)

# some of the earlier years did not have any works by female artists, so fill the NaNs with 0s
pre_2012 = pre_2012.fillna(0).drop("Artwork ID",1,errors="ignore")

```


```python
# the feature we are looking at is the year a work was acquired
X = pre_2012["Acquisition Year"]

# we are using this to predict the percent of artworks by female artists
y = pre_2012["percent_female"]

# run a linear regression
slope, intercept, r_value, p_value, std_err = stats.linregress(X, y)

# use the slope to graph the best fit line
line = [slope*p + intercept for p in X]

fig, ax = plt.subplots(figsize=(20, 10))

# graph the line and the points
ax.scatter(all_data["Acquisition Year"], all_data["percent_female"], s = 20, color = 'blue')
ax.plot(X, line, color = 'orange', linewidth=2)

# add more years 
test_years = np.arange(2011, 2017, 1) # representing 2012-2017
predict_years = np.arange(2016, 2025, 1)# predicting 2017-2020


# calculate the predicted line
t_line = [slope*x + intercept for x in test_years] 
p_line = [slope*x + intercept for x in predict_years] 

# plot test line
ax.plot(test_years, t_line, color = 'red', linewidth=2, ls='--')
ax.scatter(X, y, s = 5)

# plot prediction line
ax.plot(predict_years, p_line, color = 'green', linewidth=2, ls='--')
ax.scatter(X, y, s = 5)

# format plot
plt.xticks(range(1930, 2021, 10))
plt.xlabel("year")
plt.ylabel("percent_female")
plt.title("Linear regression showing values and predictions for 2012 onward")
plt.axvline(x=2011, ls=':', color='#add8e6')
plt.show()


# calculate root of mean standard error
mse = std_err.mean()
rmse = np.sqrt(mse).mean()
print("MSE is " + str(mse))
print("RMSE is " + str(rmse))

# put summary stats into a df column
summary_frame = pd.DataFrame(index=["slope", "intercept", "r_value", "p_value", "std_err"])
summary_stats = ["{:.7f}".format(slope),"{:.7f}".format(intercept),"{:.7f}".format(r_value),"{:.7f}".format(p_value), "{:.7f}".format(std_err)]
summary_frame["summary stats"] = summary_stats
summary_frame
```


    
![png](output_100_0.png)
    


    MSE is 0.0003427077824517334
    RMSE is 0.01851236836419731





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
      <th>summary stats</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>slope</th>
      <td>0.0024978</td>
    </tr>
    <tr>
      <th>intercept</th>
      <td>-4.8310949</td>
    </tr>
    <tr>
      <th>r_value</th>
      <td>0.6389450</td>
    </tr>
    <tr>
      <th>p_value</th>
      <td>0.0000000</td>
    </tr>
    <tr>
      <th>std_err</th>
      <td>0.0003427</td>
    </tr>
  </tbody>
</table>
</div>



The points represent the proportion of female artists for a given year. The organge line is the best fit line found with the liner regression that represents the expected value  for each year. The red line is the section that we used to test the line with data that we knew but did not include in the dataframe that the linear regression is based on. The green segment represents the projected future data until 2025.

In 2012, the regression line underestimates the proportion of female artists, but for all of the other known years after 2012, it overestimates it, with the exception of 2017 which is more or less predected accurately. 

While the line predicted a general fairly solid increase in the proportion, from 2012 onward the actual data trended downward. While there is still a positive cooreletion overall, this does support our findings that the MoMA could have acquired more works be female artists to make an even greater difference.

Both datasets showed a fairly strong positive correlation between year and gender, meaning that the proportion of female artists increases as year increases. This helps support the claim that MoMA did improve gender diversity, but it does not necessarily mean causation. However, the more recent years actually caused the correlation to go down slightly, showing that MoMA not consistently increasing the proportion. In the last year for which we have data, the percentage did increase from the previous year.

## Tate Data Extraction, Cleaning, and Loading

In order to do through analysis we should compare the MoMA's data to data from other museums. The first museum that we will examine is the [Tate](https://www.tate.org.uk), another museum for modern and contemporary art located in London, England.


```python
tate_artworks = pd.read_csv("artwork_data-tate.csv", low_memory = False)
tate_artists = pd.read_csv("artist_data-tate.csv", low_memory = False)
```


```python
display(tate_artworks.head())
tate_artists.head() 
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
      <th>id</th>
      <th>accession_number</th>
      <th>artist</th>
      <th>artistRole</th>
      <th>artistId</th>
      <th>title</th>
      <th>dateText</th>
      <th>medium</th>
      <th>creditLine</th>
      <th>year</th>
      <th>acquisitionYear</th>
      <th>dimensions</th>
      <th>width</th>
      <th>height</th>
      <th>depth</th>
      <th>units</th>
      <th>inscription</th>
      <th>thumbnailCopyright</th>
      <th>thumbnailUrl</th>
      <th>url</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>1035</td>
      <td>A00001</td>
      <td>Blake, Robert</td>
      <td>artist</td>
      <td>38</td>
      <td>A Figure Bowing before a Seated Old Man with h...</td>
      <td>date not known</td>
      <td>Watercolour, ink, chalk and graphite on paper....</td>
      <td>Presented by Mrs John Richmond 1922</td>
      <td>NaN</td>
      <td>1922.0</td>
      <td>support: 394 x 419 mm</td>
      <td>394</td>
      <td>419</td>
      <td>NaN</td>
      <td>mm</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>http://www.tate.org.uk/art/images/work/A/A00/A...</td>
      <td>http://www.tate.org.uk/art/artworks/blake-a-fi...</td>
    </tr>
    <tr>
      <th>1</th>
      <td>1036</td>
      <td>A00002</td>
      <td>Blake, Robert</td>
      <td>artist</td>
      <td>38</td>
      <td>Two Drawings of Frightened Figures, Probably f...</td>
      <td>date not known</td>
      <td>Graphite on paper</td>
      <td>Presented by Mrs John Richmond 1922</td>
      <td>NaN</td>
      <td>1922.0</td>
      <td>support: 311 x 213 mm</td>
      <td>311</td>
      <td>213</td>
      <td>NaN</td>
      <td>mm</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>http://www.tate.org.uk/art/images/work/A/A00/A...</td>
      <td>http://www.tate.org.uk/art/artworks/blake-two-...</td>
    </tr>
    <tr>
      <th>2</th>
      <td>1037</td>
      <td>A00003</td>
      <td>Blake, Robert</td>
      <td>artist</td>
      <td>38</td>
      <td>The Preaching of Warning. Verso: An Old Man En...</td>
      <td>?c.1785</td>
      <td>Graphite on paper. Verso: graphite on paper</td>
      <td>Presented by Mrs John Richmond 1922</td>
      <td>1785</td>
      <td>1922.0</td>
      <td>support: 343 x 467 mm</td>
      <td>343</td>
      <td>467</td>
      <td>NaN</td>
      <td>mm</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>http://www.tate.org.uk/art/images/work/A/A00/A...</td>
      <td>http://www.tate.org.uk/art/artworks/blake-the-...</td>
    </tr>
    <tr>
      <th>3</th>
      <td>1038</td>
      <td>A00004</td>
      <td>Blake, Robert</td>
      <td>artist</td>
      <td>38</td>
      <td>Six Drawings of Figures with Outstretched Arms</td>
      <td>date not known</td>
      <td>Graphite on paper</td>
      <td>Presented by Mrs John Richmond 1922</td>
      <td>NaN</td>
      <td>1922.0</td>
      <td>support: 318 x 394 mm</td>
      <td>318</td>
      <td>394</td>
      <td>NaN</td>
      <td>mm</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>http://www.tate.org.uk/art/images/work/A/A00/A...</td>
      <td>http://www.tate.org.uk/art/artworks/blake-six-...</td>
    </tr>
    <tr>
      <th>4</th>
      <td>1039</td>
      <td>A00005</td>
      <td>Blake, William</td>
      <td>artist</td>
      <td>39</td>
      <td>The Circle of the Lustful: Francesca da Rimini...</td>
      <td>1826–7, reprinted 1892</td>
      <td>Line engraving on paper</td>
      <td>Purchased with the assistance of a special gra...</td>
      <td>1826</td>
      <td>1919.0</td>
      <td>image: 243 x 335 mm</td>
      <td>243</td>
      <td>335</td>
      <td>NaN</td>
      <td>mm</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>http://www.tate.org.uk/art/images/work/A/A00/A...</td>
      <td>http://www.tate.org.uk/art/artworks/blake-the-...</td>
    </tr>
  </tbody>
</table>
</div>





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
      <th>id</th>
      <th>name</th>
      <th>gender</th>
      <th>dates</th>
      <th>yearOfBirth</th>
      <th>yearOfDeath</th>
      <th>placeOfBirth</th>
      <th>placeOfDeath</th>
      <th>url</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>10093</td>
      <td>Abakanowicz, Magdalena</td>
      <td>Female</td>
      <td>born 1930</td>
      <td>1930.0</td>
      <td>NaN</td>
      <td>Polska</td>
      <td>NaN</td>
      <td>http://www.tate.org.uk/art/artists/magdalena-a...</td>
    </tr>
    <tr>
      <th>1</th>
      <td>0</td>
      <td>Abbey, Edwin Austin</td>
      <td>Male</td>
      <td>1852–1911</td>
      <td>1852.0</td>
      <td>1911.0</td>
      <td>Philadelphia, United States</td>
      <td>London, United Kingdom</td>
      <td>http://www.tate.org.uk/art/artists/edwin-austi...</td>
    </tr>
    <tr>
      <th>2</th>
      <td>2756</td>
      <td>Abbott, Berenice</td>
      <td>Female</td>
      <td>1898–1991</td>
      <td>1898.0</td>
      <td>1991.0</td>
      <td>Springfield, United States</td>
      <td>Monson, United States</td>
      <td>http://www.tate.org.uk/art/artists/berenice-ab...</td>
    </tr>
    <tr>
      <th>3</th>
      <td>1</td>
      <td>Abbott, Lemuel Francis</td>
      <td>Male</td>
      <td>1760–1803</td>
      <td>1760.0</td>
      <td>1803.0</td>
      <td>Leicestershire, United Kingdom</td>
      <td>London, United Kingdom</td>
      <td>http://www.tate.org.uk/art/artists/lemuel-fran...</td>
    </tr>
    <tr>
      <th>4</th>
      <td>622</td>
      <td>Abrahams, Ivor</td>
      <td>Male</td>
      <td>born 1935</td>
      <td>1935.0</td>
      <td>NaN</td>
      <td>Wigan, United Kingdom</td>
      <td>NaN</td>
      <td>http://www.tate.org.uk/art/artists/ivor-abraha...</td>
    </tr>
  </tbody>
</table>
</div>



After examining the data set you can see they have a lot of different columns that are not needed because of their repetition, or because we won't be using them in the future. So we created new data sets without them below.


```python
tate_newartist = tate_artists[['name','gender','placeOfBirth','yearOfBirth','yearOfDeath']]
```


```python
tate_newartworks = tate_artworks[['id','accession_number','artist','artistRole','artistId','title','medium','year'
                                 ,'acquisitionYear','dimensions']]
```

Next we merged the artist and the artworks data together, in the artist dataset the artist column is called artists, and in the artworks it is called name. We merged this on left so that if there is an artist who doesn't have an artwork they don't show up.


```python
tate = tate_newartworks.merge(tate_newartist, left_on = ['artist'],right_on = ['name'], how = 'left')
```

Then we dropped the name column because we would no longer need it


```python
tate = tate.drop(columns = ['name'])
```

Then, like we did for the MoMA, we removed the rows where the acquisition year was not present. 


```python
no_date = tate[(tate['acquisitionYear'].isnull() == True)].index
tate.drop(no_date, inplace = True)
```

When examining the dates we noticed that they were not as wonky as the other one so we only had to change two dates.


```python
tate['year'].unique()
```




    array([nan, '1785', '1826', '1828', '1825', '1803', '1794', '1789',
           '1786', '1800', '1790', '1805', '1779', '1870', '1871', '1866',
           '1877', '1873', '1872', '1879', '1875', '1862', '1863', '1861',
           '1880', '1821', '1840', '1846', '1874', '1860', '1852', '1850',
           '1845', '1844', '1827', '1829', '1831', '1830', '1812', '1814',
           '1869', '1881', '1851', '1843', '1864', '1865', '1788', '1876',
           '1858', '1859', '1849', '1885', '1818', '1893', '1806', '1837',
           '1810', '1807', '1808', '1809', '1811', '1816', '1822', '1833',
           '1817', '1931', '1959', '1916', '1917', '1951', '1900', '1899',
           '1902', '1953', '1933', '1974', '1819', '1820', '1967', '1969',
           '1979', '1983', '1989', '1992', '1993', '1994', '1997', '1998',
           '1999', '2000', '1981', '1985', '1996', '1991', '1971', '2004',
           '2002', '2005', '2006', '1975', '2001', '1973', '2003', '1963',
           '1976', '1977', '1960', '1990', '1988', '2007', '1964', '1970',
           '1949', '1952', '1954', '1955', '1956', '1958', '1961', '1965',
           '1966', '1986', '1982', '1978', '1980', '1962', '1984', '1972',
           '1957', '1948', '1987', '1968', '1946', '1945', '2009', '1995',
           '2010', '2008', '2011', '1787', '1791', '1792', '1793', '1795',
           '1796', '1797', '1802', '1798', '1799', '1780', '1804', '1801',
           '1740', '1815', '1835', '1824', '1813', '1856', '1823', '1839',
           '1832', '1834', '1836', '1842', '1841', '1847', '1777', '1680',
           '1770', '1773', '1771', '1756', '1745', '1768', '1766', '1784',
           '1760', '1776', '1783', '1781', '1838', '1696', '1765', '1775',
           '1782', '1758', '1848', '1854', '1855', '1764', '1778', '1772',
           '1753', '1670', '1759', '1752', '1738', '1868', '1750', '1731',
           '1642', '1747', '1644', '1919', '1923', '1853', '1905', '1922',
           '1857', '1762', '1737', '1748', '1755', '1746', '1545', '1888',
           '1896', '1882', '1886', '1892', '1887', '1889', '1883', '1884',
           '1891', '1878', '1897', '1895', '1890', '1894', '1769', '1741',
           '1898', '1867', '1749', '1901', '1763', '1903', '1739', '1761',
           '1904', '1906', '1907', '1908', '1646', '1774', '1909', '1910',
           '1911', '1655', '1912', '1913', '1914', '1720', '1915', '1695',
           '1623', '1920', '1743', '1918', '1715', '1937', '1921', '1751',
           '1924', '1925', '1926', '1927', '1928', '1929', '1930', '1932',
           '1645', '1744', '1934', '1576', '1935', '1936', '1938', '1939',
           '1940', '1941', '1942', '1943', '1944', '1732', '1947', '1699',
           '1735', '1659', '1950', '1733', '1573', '1710', '1672', '1625',
           '2012', '1660', '1690', '1684', '1615', '1600', '1679', '1619',
           '1569', '1586', '1650', '1742', '1557', '1629', '1725', '1648',
           '1674', '1730', '1754', '1563', '1757', '1592', '1636', '1633',
           '1700', '1594', '1635', '1620', '1640', '1767', '1724', '1610',
           '1605', '1565', '1707', '1639', '1676', '1661', '1596', '1630',
           '1701', '1681', '1728', '1675', '1654', '1689', '1662', '1685',
           '1723', '1683', '1734', '1721', '1595', '1638', '1713', '1716',
           '1719', '1692', '1726', '1718', '1637', '1647', '1658', '1736',
           'no date', '1628', 'c.1997-9'], dtype=object)




```python
tate['year'].replace({'no date':np.nan, 'c.1997-9':1997}, inplace = True)
```

Next we made three new columns similar to the ones we made for the MoMA, one that is the age of the person when they made the work, one that will tell if a person was alive or not when the work was acquired by the Tate, and one representing the person's nationality


```python
tate['age_made'] = pd.to_numeric(tate['year']) - tate['yearOfBirth']
tate['alive?'] = tate['year']
```

Finally we utilized the same function and apply method as we did above to make a column determining if the artist was alive or not.


```python
def alive(acquisition,death):
    if acquisition > death:
        return 'False'
    else:
        return 'True'
tate['alive?'] = tate.apply(lambda row: alive(row['acquisitionYear'],row['yearOfDeath']),axis=1)
```

Next, before we could start making a new column for the Tate to represent nationality we noticed that the placeOfBirth column contained both the city and the country for the majority of the artists. We realized that in order to analyze the country that the artists were from we needed to separate the city from the country, so we made a new column called countryOfOrigin that contained just the country.


```python
def country(place):
    new = str(place).split(',')
    if len(new) > 1:
        first = re.sub(r"^\s+", "", new[1])
        return first
    else:
        return new[0]
tate['countryOfOrigin'] = tate.apply(lambda row: country(row['placeOfBirth']), axis=1)
```

The names of the countries for the Tate dataset were especially difficult to work with beacuse of the number of differences in the name titles. The majority of the names were in the countries native language or there was a city name rather than a country, which you can see below. Because of this we had to manually go through and check for what we wanted by looking at the unique values and then adding them to the list. 


```python
tate['countryOfOrigin'].unique()
```




    array(['United Kingdom', 'nan', 'Bharat', 'France', 'Italia', 'Viet Nam',
           'Deutschland', 'Rossiya', 'United States', 'Bahamas', 'Australia',
           'Ellás', 'België', 'Latvija', 'Canada', 'Éire', 'Schweiz',
           'Plymouth', 'Edinburgh', 'España', 'Nederland', 'Beckington',
           'Perth', 'Wimbledon', 'Canterbury', 'Danmark', 'Türkiye',
           'Österreich', 'Blackheath', 'Charlieu', 'Isle of Man',
           'Magyarország', 'Hrvatska', 'Chile', 'Epsom', 'Auteuil',
           'Kensington', 'South Africa', 'Mauritius', 'Rochdale', 'Melmerby',
           'Ukrayina', 'Sverige', 'Département de la', 'New Zealand',
           'România', 'Otok', 'Charlotte Amalie', 'Schlesien', 'Saint Hélier',
           'Polska', 'Misr', 'Bermondsey', 'Egremont', 'Norge', 'Braintree',
           'Liverpool', 'Belarus', 'Stoke on Trent', 'Zhonghua', 'Portugal',
           'Cuba', 'Moldova', 'Singapore', 'Nihon', 'Brasil', 'Staten Island',
           'México', 'Bosna i Hercegovina', 'Slovenija',
           'Slovenská Republika', 'Ceská Republika', 'Myanmar', "Yisra'el",
           'Chung-hua Min-kuo', 'D.C.', 'Colombia', 'Sri Lanka', 'Indonesia',
           'Jugoslavija', 'Bulgaria', 'Samoa', 'Argentina', 'Barbados',
           "Taehan Min'guk", 'Îran', 'Niederschlesien', 'Al-Lubnan',
           'Pakistan', 'Panamá', 'Zimbabwe', 'Zambia', 'Suriyah', 'Cameroun',
           'Nigeria', 'Bangladesh', "Al-Jaza'ir", 'Lietuva', 'Solothurn',
           'Makedonija', 'Venezuela', 'Malaysia', 'Douglas', 'London',
           'Tunis', 'Bristol', 'Armenia', 'Eesti', 'Jamaica', 'Guyana',
           'Montserrat', 'Tanzania', 'Ísland', 'Mehoz', 'Suomi',
           'Hertfordshire', 'Stockholm', 'Luxembourg', 'Shqipëria', 'Uganda',
           'Perú', 'Pilipinas', 'Malta', 'Kenya', 'Lao', 'Al-‘Iraq',
           'Nicaragua', 'Prathet Thai', "Choson Minjujuui In'min Konghwaguk",
           'As-Sudan'], dtype=object)



First we replaced "nan" with np.nan and then we created a new dataframe that didn't contain any np.nans for country of origin


```python
tate['countryOfOrigin'] = tate['countryOfOrigin'].replace({'nan':np.nan})
tate_country = tate[tate['countryOfOrigin'].isnull() == False]
#designation wasn't properly set so had to remove the works that didn't have a country of origin
```

Next we created a list of the countries, a function to make a new column and insert true or false if it was a european country or not.


```python
index = tate_country['countryOfOrigin'].index
tate_country['European?'] = ['False'] * len(index)
```


```python
countries_tate = ['Austria','Belgium','Bulgaria','Croatia','Cyprus','Czech Republic','Denmark','Estonia','Finland','France','Germany',
'Greece','Hungary','Ireland','Italy','Latvia','Luxembourg','Lithuania','Malta','Netherlands','Poland','Portugal',
'Romania','Slovak Republic','Slovenia','Spain','Sweden','United Kingdom', 'Deutschland', 'Italia','London','Ceská Republika',
 'D.C.',  'Staten Island','Liverpool', 'Polska', 'România', 'España', 'Edinburgh', 'Canada','United States', 'Stoke on Trent',
                 'Ellás','België', 'Latvija','Nederland','Wimbledon','Canterbury', 'Danmark','Österreich','Blackheath',
                  'Bermondsey', 'Egremont', 'Norge', 'Braintree','Liverpool','Bristol','Eesti','Hertfordshire', 'Stockholm', 'Luxembourg'
                 ]
non_european = []
european = []

def nation(nation):
    if nation in countries_tate:
        return 'True'
    elif nation == np.nan:
        return np.nan 
    else:
        if nation in non_european:
            return 'False'
        else:
            non_european.append(nation)
            return 'False'
            
tate_country['European?'] = tate_country.apply(lambda row: nation(row['countryOfOrigin']),axis=1)
```

After each of those steps our dataframe is now clean and in the format that we want it in, which is displayed below.


```python
tate_country
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
      <th>id</th>
      <th>accession_number</th>
      <th>artist</th>
      <th>artistRole</th>
      <th>artistId</th>
      <th>title</th>
      <th>medium</th>
      <th>year</th>
      <th>acquisitionYear</th>
      <th>dimensions</th>
      <th>gender</th>
      <th>placeOfBirth</th>
      <th>yearOfBirth</th>
      <th>yearOfDeath</th>
      <th>age_made</th>
      <th>alive?</th>
      <th>countryOfOrigin</th>
      <th>European?</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>1035</td>
      <td>A00001</td>
      <td>Blake, Robert</td>
      <td>artist</td>
      <td>38</td>
      <td>A Figure Bowing before a Seated Old Man with h...</td>
      <td>Watercolour, ink, chalk and graphite on paper....</td>
      <td>NaN</td>
      <td>1922.0</td>
      <td>support: 394 x 419 mm</td>
      <td>Male</td>
      <td>London, United Kingdom</td>
      <td>1762.0</td>
      <td>1787.0</td>
      <td>NaN</td>
      <td>False</td>
      <td>United Kingdom</td>
      <td>True</td>
    </tr>
    <tr>
      <th>1</th>
      <td>1036</td>
      <td>A00002</td>
      <td>Blake, Robert</td>
      <td>artist</td>
      <td>38</td>
      <td>Two Drawings of Frightened Figures, Probably f...</td>
      <td>Graphite on paper</td>
      <td>NaN</td>
      <td>1922.0</td>
      <td>support: 311 x 213 mm</td>
      <td>Male</td>
      <td>London, United Kingdom</td>
      <td>1762.0</td>
      <td>1787.0</td>
      <td>NaN</td>
      <td>False</td>
      <td>United Kingdom</td>
      <td>True</td>
    </tr>
    <tr>
      <th>2</th>
      <td>1037</td>
      <td>A00003</td>
      <td>Blake, Robert</td>
      <td>artist</td>
      <td>38</td>
      <td>The Preaching of Warning. Verso: An Old Man En...</td>
      <td>Graphite on paper. Verso: graphite on paper</td>
      <td>1785</td>
      <td>1922.0</td>
      <td>support: 343 x 467 mm</td>
      <td>Male</td>
      <td>London, United Kingdom</td>
      <td>1762.0</td>
      <td>1787.0</td>
      <td>23.0</td>
      <td>False</td>
      <td>United Kingdom</td>
      <td>True</td>
    </tr>
    <tr>
      <th>3</th>
      <td>1038</td>
      <td>A00004</td>
      <td>Blake, Robert</td>
      <td>artist</td>
      <td>38</td>
      <td>Six Drawings of Figures with Outstretched Arms</td>
      <td>Graphite on paper</td>
      <td>NaN</td>
      <td>1922.0</td>
      <td>support: 318 x 394 mm</td>
      <td>Male</td>
      <td>London, United Kingdom</td>
      <td>1762.0</td>
      <td>1787.0</td>
      <td>NaN</td>
      <td>False</td>
      <td>United Kingdom</td>
      <td>True</td>
    </tr>
    <tr>
      <th>4</th>
      <td>1039</td>
      <td>A00005</td>
      <td>Blake, William</td>
      <td>artist</td>
      <td>39</td>
      <td>The Circle of the Lustful: Francesca da Rimini...</td>
      <td>Line engraving on paper</td>
      <td>1826</td>
      <td>1919.0</td>
      <td>image: 243 x 335 mm</td>
      <td>Male</td>
      <td>London, United Kingdom</td>
      <td>1757.0</td>
      <td>1827.0</td>
      <td>69.0</td>
      <td>False</td>
      <td>United Kingdom</td>
      <td>True</td>
    </tr>
    <tr>
      <th>...</th>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
    </tr>
    <tr>
      <th>69313</th>
      <td>122959</td>
      <td>T13864</td>
      <td>P-Orridge, Genesis</td>
      <td>artist</td>
      <td>16646</td>
      <td>It’s That Time Of The Month (from Tampax Romana)</td>
      <td>Wood, Perspex, clock case, tampons and human b...</td>
      <td>1975</td>
      <td>2013.0</td>
      <td>object: 305 x 305 x 135 mm</td>
      <td>Male</td>
      <td>Manchester, United Kingdom</td>
      <td>1950.0</td>
      <td>NaN</td>
      <td>25.0</td>
      <td>True</td>
      <td>United Kingdom</td>
      <td>True</td>
    </tr>
    <tr>
      <th>69314</th>
      <td>122960</td>
      <td>T13865</td>
      <td>P-Orridge, Genesis</td>
      <td>artist</td>
      <td>16646</td>
      <td>Larvae (from Tampax Romana)</td>
      <td>Perspex, Wood, hairpiece, tampon and human blood</td>
      <td>1975</td>
      <td>2013.0</td>
      <td>object: 305 x 305 x 135 mm</td>
      <td>Male</td>
      <td>Manchester, United Kingdom</td>
      <td>1950.0</td>
      <td>NaN</td>
      <td>25.0</td>
      <td>True</td>
      <td>United Kingdom</td>
      <td>True</td>
    </tr>
    <tr>
      <th>69315</th>
      <td>122961</td>
      <td>T13866</td>
      <td>P-Orridge, Genesis</td>
      <td>artist</td>
      <td>16646</td>
      <td>Living Womb (from Tampax Romana)</td>
      <td>Wood, Perspex, plastic, photograph on paper, t...</td>
      <td>1976</td>
      <td>2013.0</td>
      <td>object: 305 x 305 x 135 mm</td>
      <td>Male</td>
      <td>Manchester, United Kingdom</td>
      <td>1950.0</td>
      <td>NaN</td>
      <td>26.0</td>
      <td>True</td>
      <td>United Kingdom</td>
      <td>True</td>
    </tr>
    <tr>
      <th>69316</th>
      <td>121181</td>
      <td>T13867</td>
      <td>Hatoum, Mona</td>
      <td>artist</td>
      <td>2365</td>
      <td>Present Tense</td>
      <td>Soap and glass beads</td>
      <td>1996</td>
      <td>2013.0</td>
      <td>displayed: 45 x 2410 x 2990 mm</td>
      <td>Female</td>
      <td>Bayrut, Al-Lubnan</td>
      <td>1952.0</td>
      <td>NaN</td>
      <td>44.0</td>
      <td>True</td>
      <td>Al-Lubnan</td>
      <td>False</td>
    </tr>
    <tr>
      <th>69317</th>
      <td>112306</td>
      <td>T13868</td>
      <td>Creed, Martin</td>
      <td>artist</td>
      <td>2760</td>
      <td>Work No. 227: The lights going on and off</td>
      <td>Gallery lighting</td>
      <td>2000</td>
      <td>2013.0</td>
      <td>Overall display dimensions variable</td>
      <td>Male</td>
      <td>Wakefield, United Kingdom</td>
      <td>1968.0</td>
      <td>NaN</td>
      <td>32.0</td>
      <td>True</td>
      <td>United Kingdom</td>
      <td>True</td>
    </tr>
  </tbody>
</table>
<p>66147 rows × 18 columns</p>
</div>



## Analysis: Tate vs MoMA

Now that the datasets are in similar formats we can start comparisons and anaylsis. But, the Tate data stopped collecting in 2013, so need to make MoMA align with them.


```python
tate_country['acquisitionYear'].max()
```




    2013.0




```python
# create a new DataFrame of all of the works before 2013
d = datetime.datetime(2014, 1, 1)
moma_before_2013 = names_dates_moma[names_dates_moma['Acquisition Date'] < d]
moma_after_2013 = names_dates_moma[names_dates_moma['Acquisition Date'] > d]
```

### How does the diversity of the two museums compare?

#### Comparing Western vs non-Western Art in each museum

The first visualization that we made, compares the diversity of the artists at both the Tate and the MoMA by looking at how many artists are Western or non-Western.


```python
# get the counts of works by Western and non-Western artists in MoMA before 2013
moma_European = moma_before_2013['European?'].value_counts()
```


```python
# get the counts of works by Western and non-Western artists in the Tate
tate_European = tate_country['European?'].value_counts()

# plot the Lorenz curve
fig = plt.figure()
ax = fig.add_subplot(1,1,1)
ax.plot(lorenz_curve)
ax.set_xlabel("cumulative proportion of population")
ax.set_ylabel("cumulative proportion of female artists")
ax.set_title("Lorenz Curve and Perfect Equality line")
```




    Text(0.5, 1.0, 'Lorenz Curve and Perfect Equality line')




    
![png](output_142_1.png)
    



```python
# create and format pie charts
fig = plt.figure()
ax1 = fig.add_subplot(1,2,1)
ax2 = fig.add_subplot(1,2,2)

moma_European.plot.pie(ax=ax1,  autopct='%.2f')
ax1.set_title("MoMA Western vs Non Western")

tate_European.plot.pie(ax=ax2, autopct='%.2f')

ax2.set_title("Tate Western vs Non Western")

fig.subplots_adjust(wspace=.5)
fig.set_figheight(12)
fig.set_figwidth(12)



```


    
![png](output_143_0.png)
    


<strong> Conclusion: </strong> This comparison shows that the MoMA, even before its renovation, was ahead of the Tate with regard to artists who were not of European or Western descent.

### Comparing Art by Male vs Female artists in each museum

Next we chose to look at the differences in sizes of the male and female population at both the Tate and the MoMA, as this was another area that the MoMA was striving the improve.


```python
# get the counts of works by male and female artists in MoMA before 2013
moma_gender = moma_before_2013['Gender'].value_counts()
```


```python
# get the counts of works by male and female artists in the Tate
tate_gender = tate_country['gender'].value_counts()
```


```python
# create and format pie charts
fig = plt.figure()
ax3 = fig.add_subplot(1,2,1)
ax4 = fig.add_subplot(1,2,2)
moma_gender.plot.pie(ax=ax3, autopct='%.2f')
ax3.set_title("MoMA Male vs Female Artists")

tate_gender.plot.pie(ax=ax4, autopct='%.2f')
ax4.set_title("Tate Male vs Female Artists")

fig.subplots_adjust(wspace=.5)
fig.set_figheight(12)
fig.set_figwidth(12)
```


    
![png](output_149_0.png)
    


<strong> Conclusion: </strong>This is an interesting comparison, as it once again shows that the MoMA has a greater diversity than the Tate did before its renovation, as it has a much larger proportion of female artists to male artists.

### Comparing Art by Male vs Female artists of non-Western art in each museum

What about the male/female ratio when looking at non-European/Western artists? 



```python
# get the counts of Western and non-Western works by gender in MoMA before 2013
moma_western_gender = moma_before_2013[moma_before_2013['European?'] == 'False']['Gender'].value_counts()
```


```python
# get the counts of Western and non-Western works by gender in the Tate
tate_western_gender = tate_country[tate_country['European?'] == 'False']['gender'].value_counts()
```


```python
# create and format pie charts
fig = plt.figure()
ax5 = fig.add_subplot(1,2,1)
ax6 = fig.add_subplot(1,2,2)
moma_western_gender.plot.pie(ax=ax5, autopct='%.2f')
ax5.set_title("MoMA Non Western Male vs Female Artists")

tate_western_gender.plot.pie(ax=ax6, autopct='%.2f')
ax6.set_title("Tate Non Western Male vs Female Artists")

fig.subplots_adjust(wspace=.5)
fig.set_figheight(12)
fig.set_figwidth(12)
```


    
![png](output_155_0.png)
    


<strong>Conclusion: </strong> The data shows that  MoMA has a larger of female non-Western/european artists than the Tate, but this difference is smaller than the one above.


### Do countries favor their own art over foreign art? Could this be a bias towards American or British art across many museums?

We next examined the proportion of British works in the Tate, the proportion of American works in the MoMA, the proportion of American works in the Tate, and the proportion of British works in MoMA to see if the museums tended to collect more work created by artists from their respective countries or if there was a significant bias towards American art in the Tate or British art in MoMA.


```python
# find the proportion of British works in the Tate
tate_country = tate_country.assign(British=np.where(tate_country['countryOfOrigin']=="United Kingdom", True, False))
tate_British_prop = (tate_country["British"] == True).mean()
```


```python
# Find the proportion of American art in MoMA
names_dates_moma = names_dates_moma.assign(American=np.where(names_dates_moma['Nationality']=="American", True, False))
moma_American_prop = (names_dates_moma["American"] == True).mean()
```


```python
# Find the proportion of British art in MoMA
names_dates_moma = names_dates_moma.assign(British=np.where(names_dates_moma['Nationality']=="British", True, False))
moma_British_prop = (names_dates_moma["British"] == True).mean()
```


```python
# find the proportion of American works in the Tate
tate_country = tate_country.assign(American=np.where(tate_country['countryOfOrigin']=="United States", True, False))
tate_American_prop = (tate_country["American"] == True).mean()
```


```python
# Graph it!
plotdata = pd.DataFrame(
    {"% of artworks": [tate_American_prop, moma_American_prop, tate_British_prop, moma_British_prop]}, 
    index=["Proportion of American Art in the Tate", "Proportion of American Art in MoMA", "Proportion of British Art in the Tate", "Proportion of British Art in MoMA"])
plotdata.plot(kind="barh", title = "Examining National Biases in MoMA and the Tate")
```




    <AxesSubplot:title={'center':'Examining National Biases in MoMA and the Tate'}>




    
![png](output_163_1.png)
    


<strong>Conclusion:</strong> It looks like both MoMA and the Tate favor art from their home country, with the Tate having a higher percentage of British art than MoMA has American art. There is roughly the same proportion of British art in MoMA as there is American art in the Tate.

### Examining gender and home country bias in the two museums
How does the gender ratio look when examining art from the museum's home country?


```python
# Proportion of Male American Artists
american_males_moma = (names_dates_moma['American'] == True) & (names_dates_moma['Gender']  == "Male")
american_males_moma_prop = american_males_moma.mean()
```


```python
# Proportion of Male non-American Artists
nonamerican_males_moma = (names_dates_moma['American'] == False) & (names_dates_moma['Gender']  == "Male")
nonamerican_males_moma_prop = nonamerican_males_moma.mean()
```


```python
# Proportion of Female American Artists
american_females_moma = (names_dates_moma['American'] == True) & (names_dates_moma['Gender']  == "Female")
american_females_moma_prop = american_females_moma.mean()
```


```python
# Proportion of Female non-American Artists
nonamerican_females_moma = (names_dates_moma['American'] == False) & (names_dates_moma['Gender']  == "Female")
nonamerican_females_moma_prop = nonamerican_females_moma.mean()
```


```python
# Proportion of Male British Artists
british_males_tate = (tate_country['British'] == True) & (tate_country['gender']  == "Male")
british_males_tate_prop = british_males_tate.mean()
```


```python
# Proportion of Male non-British Artists
nonbritish_males_tate = (tate_country['British'] == False) & (tate_country['gender']  == "Male")
nonbritish_males_tate_prop = nonbritish_males_tate.mean()
```


```python
# Proportion of Female British Artists
british_females_tate = (tate_country['British'] == True) & (tate_country['gender']  == "Female")
british_females_tate_prop = british_females_tate.mean()
```


```python
# Proportion of Feale non-British Artists
nonbritish_females_tate = (tate_country['British'] == False) & (tate_country['gender']  == "Female")
nonbritish_females_tate_prop = nonbritish_females_tate.mean()
```


```python
# Graph MoMA data!
plotdata_moma = pd.DataFrame(
    {"% of artworks": [american_males_moma_prop, nonamerican_males_moma_prop, american_females_moma_prop, nonamerican_females_moma_prop]}, 
    index=["Proportion of American Male Artists", "Proportion of non-American Male Artists", "Proportion of American Female Artists", "Proportion of Non-American Female Artists"])
plotdata_moma.plot(kind="barh", title = "Examining National Biases MoMA")

# Graph Tate data!
plotdata_tate = pd.DataFrame(
    {"% of artworks": [british_males_tate_prop, nonbritish_males_tate_prop, british_females_tate_prop, nonbritish_females_tate_prop]}, 
    index=["Proportion of British Male Artists", "Proportion of non-British Male Artists", "Proportion of British Female Artists", "Proportion of Non-British Female Artists"])
plotdata_tate.plot(kind="barh", title = "Examining National Biases in the Tate")
```




    <AxesSubplot:title={'center':'Examining National Biases in the Tate'}>




    
![png](output_174_1.png)
    



    
![png](output_174_2.png)
    


Conclusion: Both museums favored male artists, but MoMA has a higher proportion of non-American art than it has American art by male artists, while the Tate heavily favors British male artists. In terms of female artists, MoMA favors American over non-American artists while the Tate doesn't have a distinct difference between British and non-British female artists.

## Met Data Extraction, Cleaning, and Loading

The second museum that we are comparing to the MoMA is the [Metropolitan Museum of Art (the Met)](https://www.metmuseum.org/), which offers a lot of [open source resources](https://www.metmuseum.org/about-the-met/policies-and-documents/image-resources) pertaining to its collection. We extracted the data from the [.csv](https://github.com/metmuseum/openaccess/blob/master/MetObjects.csv) that is located on their GitHub page. To download it, you must use [GitHub Large File Storage](https://git-lfs.github.com/) because the dataset is rather large. [Here](https://github.com/metmuseum/openaccess/issues/4)'s more information.

It was a little more difficult to work with than the previous two datasets because of its sized. The file is too large to be posted on our GitHub or viewed on the Met's GitHub.


```python
met = pd.read_csv("MetObjects.csv",  low_memory=False)
```


```python
met.head()
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
      <th>Object Number</th>
      <th>Is Highlight</th>
      <th>Is Timeline Work</th>
      <th>Is Public Domain</th>
      <th>Object ID</th>
      <th>Gallery Number</th>
      <th>Department</th>
      <th>AccessionYear</th>
      <th>Object Name</th>
      <th>Title</th>
      <th>...</th>
      <th>River</th>
      <th>Classification</th>
      <th>Rights and Reproduction</th>
      <th>Link Resource</th>
      <th>Object Wikidata URL</th>
      <th>Metadata Date</th>
      <th>Repository</th>
      <th>Tags</th>
      <th>Tags AAT URL</th>
      <th>Tags Wikidata URL</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>1979.486.1</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>1</td>
      <td>NaN</td>
      <td>The American Wing</td>
      <td>1979</td>
      <td>Coin</td>
      <td>One-dollar Liberty Head Coin</td>
      <td>...</td>
      <td>NaN</td>
      <td>Metal</td>
      <td>NaN</td>
      <td>http://www.metmuseum.org/art/collection/search/1</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>Metropolitan Museum of Art, New York, NY</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>1</th>
      <td>1980.264.5</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>2</td>
      <td>NaN</td>
      <td>The American Wing</td>
      <td>1980</td>
      <td>Coin</td>
      <td>Ten-dollar Liberty Head Coin</td>
      <td>...</td>
      <td>NaN</td>
      <td>Metal</td>
      <td>NaN</td>
      <td>http://www.metmuseum.org/art/collection/search/2</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>Metropolitan Museum of Art, New York, NY</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>2</th>
      <td>67.265.9</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>3</td>
      <td>NaN</td>
      <td>The American Wing</td>
      <td>1967</td>
      <td>Coin</td>
      <td>Two-and-a-Half Dollar Coin</td>
      <td>...</td>
      <td>NaN</td>
      <td>Metal</td>
      <td>NaN</td>
      <td>http://www.metmuseum.org/art/collection/search/3</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>Metropolitan Museum of Art, New York, NY</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>3</th>
      <td>67.265.10</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>4</td>
      <td>NaN</td>
      <td>The American Wing</td>
      <td>1967</td>
      <td>Coin</td>
      <td>Two-and-a-Half Dollar Coin</td>
      <td>...</td>
      <td>NaN</td>
      <td>Metal</td>
      <td>NaN</td>
      <td>http://www.metmuseum.org/art/collection/search/4</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>Metropolitan Museum of Art, New York, NY</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>4</th>
      <td>67.265.11</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>5</td>
      <td>NaN</td>
      <td>The American Wing</td>
      <td>1967</td>
      <td>Coin</td>
      <td>Two-and-a-Half Dollar Coin</td>
      <td>...</td>
      <td>NaN</td>
      <td>Metal</td>
      <td>NaN</td>
      <td>http://www.metmuseum.org/art/collection/search/5</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>Metropolitan Museum of Art, New York, NY</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
  </tbody>
</table>
<p>5 rows × 54 columns</p>
</div>



The Met dataset has a lot of columns that we don't need to compare with the MoMA, so we drop them below


```python
met.drop(columns = ['Object Number','Is Highlight','Is Timeline Work','Is Public Domain','Object ID',
                          'Gallery Number','Dynasty', 'Reign',
       'Portfolio', 'Constiuent ID', 'Artist Role', 'Artist Prefix','Artist Display Bio', 'Artist Suffix',
       'Artist Alpha Sort','Artist ULAN URL',
       'Artist Wikidata URL','Object Begin Date',
       'Object End Date', 'Medium', 'Dimensions', 'Credit Line',
       'Geography Type', 'City', 'State', 'County', 'Country', 'Region',
       'Subregion', 'Locale', 'Locus', 'Excavation', 'River', 'Classification',
       'Rights and Reproduction', 'Link Resource', 'Object Wikidata URL',
       'Metadata Date', 'Repository', 'Tags', 'Tags AAT URL',
       'Tags Wikidata URL'], inplace = True)
```

The Met as a museum itself contains multiple differnt departments and isn't only modern and contemporary art, so we filter based on department to only look at the modern and contemporary art that the Met has.


```python
met_modern = met[met['Department'] == 'Modern and Contemporary Art']
```

Next we drop the rows that don't have an Accession year, and filter for the years that are before 2017, as the MoMA dataset only goes up to 2017 but the Met is frequently updated and has works acquired in 2020 in its dataset.


```python
met_modern = met_modern.dropna(subset=['AccessionYear'])
met_modern['AccessionYear'] = met_modern['AccessionYear'].astype(int)
met_before_2017 = met_modern[met_modern['AccessionYear'] < 2017]
```


```python
met_before_2017['Artist Begin Date'] = met_before_2017['Artist Begin Date'].replace(np.nan,'no')
met_before_2017['Artist End Date'] = met_before_2017['Artist End Date'].replace(np.nan,'no')
```

After filtering we then looked at different columns that we had noticed original problems with, for instance, Artist Nationality, which is displayed below, sometimes has multiple nationalities in the column that are divided with a '|' and then on the other side of the '|' there are sometimes even more nationalities sepearted by a ','. When examining the columns we noticed that the majority of the time the first nationality was the one that they were born with while the other ones were the country that they died in or lived their later life in. Because of this we chose to pick the first nationality every time.


```python
display(met_before_2017['Artist Nationality'].unique())
met_before_2017['Artist Nationality'] = met_before_2017['Artist Nationality'].replace(np.nan,'no')
```


    array(['American', 'Spanish', 'German|German', 'French', 'French|French',
           'British|British', 'British', nan, 'Austrian', 'Hungarian',
           'American ', 'British, Scottish', 'American, born Luxembourg',
           'American, born Poland', 'Mexican', 'American, born Germany',
           'Italian, born Greece', 'Italian', 'French, born Switzerland',
           'German', 'British, born United States', 'French, born Russia',
           'Israeli', 'American, born Hungary', 'Czech',
           'American, born Russia', 'Russian, born Romania', 'Australian',
           'Japanese', 'American, born Denmark', 'French, born Germany',
           'Russian', 'French, born Poland|American, born Lithuania',
           'Norwegian', 'American, born Canada', 'Swedish',
           'American|American, born England', 'German, born Switzerland',
           'French|French|French|French', 'American, born Spain',
           'American, born Japan', 'American|Italian', 'Swedish|Swedish',
           'American, born Lithuania', 'American, born The Netherlands',
           'American, born Ukraine', 'American, born France',
           'Austrian|Austrian', 'American|American, born Lithuania',
           'American|American', 'American|Japanese', 'Spanish|Italian',
           'Venezuelan', 'American, born Bulgaria', 'Chilean', 'Finnish',
           'Belgian', 'Indian', 'British, born Germany', 'Austrian|German',
           'American, born Philippines', 'American, born China',
           'American, born Austria', 'American, born Germany|American',
           'Brazilian', 'Canadian', 'American, born Sweden',
           'American|American|American', 'American, born Italy',
           'American|French', 'Dutch', 'American, born England',
           'Austrian|Czech|Austrian', 'Romanian', 'Danish',
           'British, Scottish|British', 'American, born England|American',
           'British, born Canada', 'Swiss', 'Lebanese',
           'French, born Hungary|French', 'Uruguayan', 'Rumanian',
           'American, born Armenia', 'American, born Panama',
           'Italian, born Argentina', 'French, born Japan', 'Czechoslovakian',
           'Australian, born India', 'Colombian',
           'American, born Puerto Rico', 'American, born Cuba',
           'Austrian|Czech', 'American, born Switzerland',
           'Israeli, born Romania', 'American|Dutch', 'Finnish|Finnish',
           'American|American|German', 'Austrian, born Germany',
           'Danish|Danish', 'French|French|French', 'Irish',
           'American, born Hungary|German', 'German|Russian',
           'American, born Ireland',
           'American, born Austria|American, born Austria', 'Polish',
           'French|Dutch', 'American, born Greece', 'French|Italian',
           'Italian|Italian', 'German|German|German', 'Dutch|Dutch',
           'Italian|Italian|Italian', 'British|American',
           'Italian|Italian|Italian|Italian', 'Spanish|French',
           'Italian, born Austria', 'American|Israeli',
           'Italian, born Austria|Italian', 'American, born Romania',
           'Japanese|Japanese', 'Japanese|Italian|Japanese',
           'American, born Italy|American|Italian',
           'American, born Italy|American', 'Argentinian', 'Spanish|Spanish',
           'Italian|Argentinian', 'American|American, born Italy',
           'Australian|American', 'Yugoslavian', 'Czech|Czechoslovakian',
           'British|British|British', 'Israeli, born Palestine',
           'American|Mexico', 'American|American|American|American',
           'American|American, born Russia|American, born Russia', 'Korean',
           'New Zealander', 'Mexican, born United States',
           'Austrian, born Germany|Austrian|Austrian', 'Saint Lucian',
           'Chinese', 'British, born Austria', 'Cuban',
           'French, born Lithuania', 'Taiwanese', 'Danish|Italian',
           'American|German', 'Kuwaiti', 'French, born Romania',
           'French|American', 'American, born Egypt', 'Iranian',
           'Spanish|French|French', 'Italian|Italian, born Austria',
           'French|American and French', 'American and French|French',
           'French, born Switzerland|French', 'Andorran|French',
           'American, born Norway', 'American|Swedish', 'French|German',
           'German|French', 'American, born Luxembourg|American',
           'American|American, born Luxembourg', 'American German',
           'Austrian|Austrian, born Hungary', 'Peruvian', 'Italian|American',
           'Swedish|Danish', 'American, born Belgium',
           'American, born Finland', 'France', 'Portuguese',
           'French|French, born Switzerland', 'Chinese|American',
           'British, born United States|American', 'British, born France',
           'British, born China', 'Denmark|Danish', 'Dominican',
           'French, born Poland', 'American, born Germany|German',
           'American|Turkish', 'American|British',
           'American, born Austria-Hungary|American', 'American|Hungarian',
           'French|American|American', 'Swiss|German/Swiss',
           'French, born Switzerland|French|French|Swiss', 'Danish|Swiss',
           'American|American, born Austria-Hungary', 'Hungarian|American',
           'American, born Russia|American',
           'Czech|Czech|Czechoslovakian|Czech', 'Dutch|Dutch|German',
           'Dutch|Italian', 'South African', 'German|American, born Austria',
           'German|Italian', 'Swedish|Italian', 'British, born Scotland',
           'German, born Poland', 'Mexican, born England',
           'British, Scottish|Italian', 'Italian|British, Scottish',
           'French|Romanian|French', 'Romanian|French', 'Japanese|French',
           'French, born Switzerland|German', 'French, born Hungary',
           'Tajikistan', 'Swiss|Swiss', 'French, born China', 'French|Czech',
           'Turkish', 'Austrian|Austrian|Austrian', 'Slovakian', 'Egyptian',
           'Ghanaian', 'Kashmir', 'British, born Argentina',
           'Swedish, born Austria', 'Danish-Icelandic', 'Ukrainian',
           'Pakistani', 'Israeli|Italian', 'Mexican|French', 'French|Swiss',
           'Guatemalan', 'Icelandic', 'American|American|American '],
          dtype=object)



```python
index = met_before_2017['Artist Nationality'].index
for x in index:
    if met_before_2017.loc[x,'Artist Nationality'] != 'no':
        if 'born' in met_before_2017.loc[x,'Artist Nationality']:
            b = met_before_2017.loc[x,'Artist Nationality'].split(' ')
            born = b[-1]
            if '|' in born:
                n = born.split('|')
                met_before_2017.loc[x,'Artist Nationality'] = n[0]
            else:
                met_before_2017.loc[x,'Artist Nationality'] = born
        elif '|' in met_before_2017.loc[x,'Artist Nationality']:
            if 'born' in met_before_2017.loc[x,'Artist Nationality']:
                n = met_before_2017.loc[x,'Artist Nationality'].split(',')
                met_before_2017.loc[x,'Artist Nationality'] = n[0]
            else:
                n = met_before_2017.loc[x,'Artist Nationality'].split('|')
                met_before_2017.loc[x,'Artist Nationality'] = n[0] 
met_before_2017['Artist Nationality'] = met_before_2017['Artist Nationality'].replace('no',np.nan)
```


```python
index = met_before_2017['Artist Begin Date'].index
met_before_2017['Artist Begin Date'].astype(str)
for x in index:
    if met_before_2017.loc[x,'Artist Begin Date'] != np.nan:
        if '|' in met_before_2017.loc[x,'Artist Begin Date']:
            n = met_before_2017.loc[x,'Artist Begin Date'].split('|')
            met_before_2017.loc[x,'Artist Begin Date'] = n[0]
```


```python
index = met_before_2017['Artist End Date'].index
met_before_2017['Artist End Date'] = met_before_2017['Artist End Date'].astype(str, copy=True, errors='raise')
for x in index:
    if met_before_2017.loc[x,'Artist End Date'] != np.nan:
        if '|' in met_before_2017.loc[x,'Artist End Date']:
            n = met_before_2017.loc[x,'Artist End Date'].split('|')
            met_before_2017.loc[x,'Artist End Date'] = n[0]
```

Next we tried to clean the values in the Artist Gender column, but upon further inspection the column doesn't contain any usable information. It is a mix of | and only Female, which although it would be interesting to learn that the Met only had modern and contemporary works of art by female artists we know that this is not true. We displayed the different unique values, and the mess that is their Artist Gender column. Because of this we will not be able to do the same analysis of the Met vs the MoMA on gender like we did above for the MoMA vs the Tate.


```python
met['Artist Gender'].unique()
```




    array([nan, '|', '|Female', '||', '||||', 'Female|', 'Female', '|||',
           'Female|Female', '||Female', '|Female|Female', 'Female||',
           'Female|Female|Female|Female|Female||Female|Female||||||Female|Female|Female|Female|||Female|||Female|Female|',
           '|Female|', 'Female||Female', 'Female|Female|Female|', '||||||||',
           '|||||||||||||||||||||||||||', 'Female|Female|', 'Female|Female||',
           'Female|||', '|Female||', '|Female||Female', '||Female|',
           '|Female|Female|Female', '|||Female', 'Female|Female|Female',
           '||||||', '|Female|||', '|||||', '||||Female|', '||||||||||',
           '||||||||||||||||', '|||||||', '|||||||||||||', '|||||||||',
           '||||||||||||||||||||',
           'Female|||||||||||||||||||||||||||||Female||Female||||',
           'Female||||Female|||Female|||||||||||||||||Female|',
           '|Female||||||||||||||Female', '|||||||Female|||||',
           'Female||||Female|||||Female|||',
           '||||Female||||||Female|||||Female||||Female', '||||Female',
           '||||||||||||||||||||||||||||||', '||Female||',
           'Female|||||||Female|Female||||Female|',
           'Female|||Female|||Female|||Female||||', '|||||||||||||||||',
           '||||||||||||||||||', '||Female|Female|Female|||||Female||||',
           '||||||||||||', '|||||||||||||||',
           '||||||||||||||||||||||||||||||||||||||', '||Female||||||',
           '||||||||||||||||||||||||||', 'Female|||Female|', '|||||||||||',
           '||Female|||||||||||Female|||||||', '|||||||||Female',
           '||||||||Female||||||||', '|||||||||||||||Female||||',
           '|||Female|', '|||||||||||||||||||',
           '|||||||||||||||||||||||||||||', '|||||||Female|',
           '||||||||||||||Female|||||||', '||||||||||||||',
           '||||||||||Female', '||Female||Female||',
           '|Female||||||||||||||||Female|||||||||||||||',
           '|||||Female||||||||||||Female|||Female|||||||||',
           '||||||||||||Female|||||Female||||||||||||||',
           '|||||||||||||||||Female||||||||||||||||',
           '|Female|||||||||||||||||||||||',
           '||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||',
           '|Female||||||||||||||||||||||||||Female||||Female|||||||||||||||||',
           '||||||||||||||||Female|||||||||||||', 'Female||||||',
           '||||||||||||||||||||||Female||||Female|Female|Female|||||||Female|||Female|||||||||||||||||||||||||||||Female',
           'Female||||||||||||', '||||||||||||||||||||||||||||||||',
           '|||||||||||||||||||||||||', '|||||Female|||||||',
           '|||||||||||||||||||||||||||||||||', '|||||Female||',
           '|||Female|||||', '|||||Female', '||||||||||||||||||||||||',
           '|||||||Female|||||||||||||||||||||||||',
           '|||||||||||||||||||||||||||||||||||||', '|||||Female||||Female|',
           '||||||||Female|Female|||||||||||||||||||',
           '|||||||||Female|||||||||||||||||||||||', '|||Female||',
           '|||||||||||||||||||||||||||||||||||||||||||||||||',
           '||||||||Female||||', '||||||||||||||||||||||',
           '|||||||||||||||||||||||||||||||||||||||||||||||', 'Female||||',
           '||||||||||||Female|||||', '|Female||||||||',
           '||||||||||||Female||||Female||||Female|||||||||||',
           '||Female||||', '|||||||||||||||||||||||||||||||||||',
           '||||Female|||', 'Female||Female|Female', 'Female|Female||Female',
           '||||||||Female|', '|Female|Female|||Female', '||||||Female',
           '||||||||||||||||||||||||||||', '|||Female|||',
           '||Female|Female|Female|||||||Female||',
           '|||||||||||||||||Female|||||', '||||Female|||||||',
           '||||||||||||||||||||||||||||||Female|||||',
           '|||||||||||||||||||||Female||||||||||||||||||||',
           '|||||||||||||||||||||', '||||||||||||||||||||||||||Female|',
           '||Female|||', '||||||Female||', '|||Female||Female|',
           '||||||||||||||Female|||||Female||',
           '||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||',
           '|||||||Female', '|||||||||||||||||||||||||||||||||||||||||',
           '||||||||Female|||||||',
           '|||Female|||Female|Female||||||Female|||||||||||||||||',
           '||||||||||||||||||||Female|||||||||||||',
           '|||||||||||||||||||||||||||||||||||||||', '|||||Female|',
           '||||||||||||||Female|||||', '|||||Female||||||', '|Female|||||',
           '||||Female|Female||||Female||||||||Female|||',
           '|||||||||||||Female|||', '||||||Female|||||',
           '||Female||||||Female||||||||',
           '||||||||||||||||||||||||||||||||||||||||||||||||||',
           '|||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||',
           '||Female||||Female|Female',
           '|||||||||||||||Female||Female||||||||Female||||',
           'Female||Female||Female|||||||||||||', 'Female|||||||',
           'Female|||Female||||||||||Female|||||',
           'Female||Female||||||Female|', '|||||Female|Female||||',
           '||||Female||||||||', '|||||Female||||||||||||',
           '||||||||||||||||||||||||||||||||||Female||||||||||Female',
           '||||||||||||Female||||', '||||||||||Female||', '||||Female||',
           '|Female||||||||Female|||||||Female|||||||||Female||||',
           '||||Female|||||||||||',
           '||||||||||||||||||||||||||||||Female|Female||||||||||||||||||||||||||||||||||||||||||Female||||Female|||||||||Female|Female|||||||||||||||||Female||Female||||||||||||||||||||||||||||',
           '|Female||||',
           '|||||Female||||||||||||||Female|||||||||||||||||||||||||',
           '||||||||Female||||||||||||||||||||', 'Female|||||',
           '||||||||Female|||||Female||||||||||||Female||Female|Female|||||||||||||||Female|||||||',
           '|||Female||||||||||||||||', '|||Female|||||Female',
           '||Female|||Female|||', '|Female|||||||||Female|',
           '|||Female||||||Female|||||||Female||||',
           'Female|||||Female||||||||||||||||Female|',
           '||Female|Female||||Female|Female|||||||||Female|',
           '||Female|Female||||||Female||Female||||',
           '|||||||||||||||Female|||||||||||||||||||||||||||||||||||||||||||||||||||||||||||Female||||||||',
           '|||Female||||||||', '|||||||||Female|||||',
           '|||||||Female|||||||', '||Female|Female||',
           '||||Female||Female||||Female|||||', '|||||||||||||||||||||||',
           '|||||||||||Female||||||||||Female||||||',
           '||||||||||||||||||||Female||', '||||||||||||||||||Female||',
           '|Female|||||||||||Female||||||',
           '||Female||Female|Female||Female|Female||||Female||Female|Female||Female||Female|||',
           'Female||||Female||||||Female||||Female||Female|||||Female||||||Female|Female|||Female|Female',
           '||||Female|||||', '|||Female|||||||||||Female||||||||',
           '|||||||||||||||Female|||', '|||Female|||Female|||',
           '||||||||||||||||||||||||||||||||||||||||||||',
           '||||||||||||||Female|||||||||||||||||||||||', '|Female|Female||',
           'Female||||||Female', '||||||Female|||', '||Female||||||||',
           '||Female|||||', '||||||||||Female|||||||Female|||||||||||||',
           '||||||||||||Female|||||||||',
           '||||||||||||||||||Female|||||||||||||||||||||||||||||',
           '||||||||||||||||||||||||||||||Female||||||||||||||Female|||',
           '||||||||||||Female||||||||Female|',
           '|Female||||Female|Female||||||',
           '|||||||||||||Female|||||||Female||',
           '||||||||||||Female|||Female||',
           '|Female||Female||||Female|Female|Female|Female|Female|Female|Female|Female|',
           '||||||||||||Female|||||||||||Female||Female|Female||||||||||||Female||',
           '|||||||||||||||||||||||||||||||||||||||||||',
           'Female|Female||Female|',
           'Female||||||||||||||Female|||||||||||||||||', '||Female|||||||',
           'Female|||Female|||Female|', '|Female||||||||||||||||||||||||||||',
           '|Female|||||||', '|||||Female||||||||||||||',
           '||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||',
           'Female||||Female|||Female|Female|Female|Female||||||Female||Female||Female|Female|||Female||Female|Female||Female|Female|Female||||Female||||',
           '|||||||||Female||||||||', '|||||||||||||||||Female',
           '||Female|||||||||', 'Female|Female|||',
           '||||||Female|||||||||||||||||||Female|||||||||',
           '|||||||Female|||||||||||||||',
           '||||||||||||||||||||||||||||||||||||||||||||||||||||||||Female||||||||||||||||Female|||||||||||||||||||||||||||||||||||',
           '||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||Female|||||||||||||Female||||||||||||||||||||||||||||||||||||||||||||||||||||||||||',
           '|||||||||||||||||||||||||||||||Female||||||||',
           '|Female||Female|Female|||||',
           '|||||||||||||||||||||||||Female||||',
           '|||||||||||||||||||||Female||||||', 'Female|||||||||||',
           '||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||Female',
           '|Female|||Female', 'Female||||Female', 'Female|||Female',
           'Female||||Female|', '|Female|Female|',
           '|||||||||||||||||Female||Female|||',
           '||Female||||||||||||||||||||||||',
           '|||||||||||||Female||||||||||||', 'Female||||||||||||||',
           'Female|||||||||||||', 'Female|||||Female',
           '||||||||||||||Female||Female|||||||', '|||||Female||||',
           '||||||||||||||||||||Female||||||', '||Female|Female|',
           'Female||Female|Female|Female||Female|Female||',
           'Female|Female|Female|Female|Female|Female|Female||Female|Female|Female|Female|Female|Female|Female|Female|Female|Female||Female|Female|Female',
           '|||Female|||||Female|', '|||||||||||Female', '|||Female|Female',
           '|||Female|||Female||||Female||', '||||||Female|',
           '||||Female|||Female|Female|', 'Female|||Female||Female||||',
           '|Female|Female|Female||||||', 'Female|Female|Female|||||||',
           '|Female||Female||||Female||', '|||Female||Female|Female',
           '|Female|Female||Female|||Female||Female', '|Female||Female|',
           '||Female||Female', 'Female||Female||', '||Female|Female',
           '||||||Female||||||||||||||||||||||', '||Female||Female|||',
           '||||||||Female||||||',
           'Female|Female|Female|Female|Female|Female|Female',
           '||Female|||||||Female|', '|||Female||||',
           'Female|||||Female||||Female|', '|Female||Female|||',
           'Female||Female|', 'Female||Female|||', '||||Female|Female',
           '|||||||||||||||||||||||||||||||Female||||||||Female|||||||||||Female||||||||||||||||||||||||||||||||||||',
           '|||||Female|||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||',
           '|||||||||||||||||||||||||||Female|||||Female|||||||||||||||||Female|||||||Female||',
           '||||||||||Female|||||||||||||||||||||||||||||Female|||||||||',
           '||||||||Female|||Female||'], dtype=object)



## Analysis: Met vs MoMA

Unlike the Tate the Met doesn't have a usable gender column so we won't be able to conduct quite as much analysis as we did with the Tate, but we can still compare the diversity of the two museums based on the nationality of the artists.

#### Comparing Western vs non-Western Art in each museum

First we take out the colums where there isn't a nationality for the artist.


```python
met_country_before2017 = met_before_2017[met_before_2017['Artist Nationality'] != 'no']
```

Then we examine the different nationalities and compire the ones that are Western nationalities into a list.


```python
met_country_before2017['Artist Nationality'].unique()
```




    array(['American', 'Spanish', 'German', 'French', 'British', nan,
           'Austrian', 'Hungarian', 'American ', 'British, Scottish',
           'Luxembourg', 'Poland', 'Mexican', 'Germany', 'Greece', 'Italian',
           'Switzerland', 'States', 'Russia', 'Israeli', 'Hungary', 'Czech',
           'Romania', 'Australian', 'Japanese', 'Denmark', 'Russian',
           'Lithuania', 'Norwegian', 'Canada', 'Swedish', 'England', 'Spain',
           'Japan', 'Netherlands', 'Ukraine', 'France', 'Venezuelan',
           'Bulgaria', 'Chilean', 'Finnish', 'Belgian', 'Indian',
           'Philippines', 'China', 'Austria', 'Brazilian', 'Canadian',
           'Sweden', 'Italy', 'Dutch', 'Romanian', 'Danish', 'Swiss',
           'Lebanese', 'Uruguayan', 'Rumanian', 'Armenia', 'Panama',
           'Argentina', 'Czechoslovakian', 'India', 'Colombian', 'Rico',
           'Cuba', 'Irish', 'Ireland', 'Polish', 'Argentinian', 'Yugoslavian',
           'Palestine', 'Korean', 'New Zealander', 'Saint Lucian', 'Chinese',
           'Cuban', 'Taiwanese', 'Kuwaiti', 'Egypt', 'Iranian',
           'American and French', 'Andorran', 'Norway', 'American German',
           'Peruvian', 'Belgium', 'Finland', 'Portuguese', 'Dominican',
           'Austria-Hungary', 'South African', 'Scotland', 'Tajikistan',
           'Turkish', 'Slovakian', 'Egyptian', 'Ghanaian', 'Kashmir',
           'Danish-Icelandic', 'Ukrainian', 'Pakistani', 'Guatemalan',
           'Icelandic'], dtype=object)



With this list we can filter and create a new column like we did before for the Tate and the MoMA to determine if the artist is European or not.


```python
countries_met = ['American', 'Spanish', 'German', 'French', 'British', 'Austrian',
       'Hungarian', 'American ', 'British, Scottish','Italian','Czech','Norwegian', 'Swedish','Finnish',
       'Belgian','Canadian', 'Dutch', 'Romanian','Danish', 'Swiss','Rumanian',
       'Czechoslovakian', 'Irish', 'Polish','Yugoslavian','American and French','American German',
                 'France', 'Portuguese', 'Denmark','Slovakian','Danish-Icelandic','Icelandic']
non_european = []
met_country_before2017['European'] = ['False'] * len(met_country_before2017['Artist Nationality'])
index = met_country_before2017['Artist Nationality'].index

for x in index:
    if met_country_before2017.loc[x, 'Artist Nationality'] in countries_met:
        met_country_before2017.loc[x,'European?'] = 'True'
    elif met_country_before2017.loc[x, 'Artist Nationality'] == np.nan:
        met_country_before2017.loc[x,'European?'] = 'True' 
    else:
        if met_country_before2017.loc[x, 'Artist Nationality'] in non_european:
            met_country_before2017.loc[x,'European?'] = 'False'
        else:
            non_european.append(met_country_before2017.loc[x, 'Artist Nationality'])
            met_country_before2017.loc[x,'European?'] = 'False'
```

Next we can create a visualization comparing the MoMA to the Met like we did for the Tate


```python
# get the counts of works by Western and non-Western artists in MoMA before 2013
moma_European = names_dates_moma['European?'].value_counts()
```


```python
# get the counts of works by Western and non-Western artists in the Met
met_European = met_country_before2017['European?'].value_counts()
```


```python
# create and format pie charts

# create and format pie charts
fig = plt.figure()
ax1 = fig.add_subplot(1,2,1)
ax2 = fig.add_subplot(1,2,2)
moma_European.plot.pie(ax=ax1,  autopct='%.2f')
ax1.set_title("MoMA Western vs Non Western")


met_European.plot.pie(ax=ax2, autopct='%.2f')
ax2.set_title("Met Western vs Non Western")

fig.subplots_adjust(wspace=.5)
fig.set_figheight(12)
fig.set_figwidth(12)
```


    
![png](output_206_0.png)
    



```python
met_country_before2017
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
      <th>Department</th>
      <th>AccessionYear</th>
      <th>Object Name</th>
      <th>Title</th>
      <th>Culture</th>
      <th>Period</th>
      <th>Artist Display Name</th>
      <th>Artist Nationality</th>
      <th>Artist Begin Date</th>
      <th>Artist End Date</th>
      <th>Artist Gender</th>
      <th>Object Date</th>
      <th>European</th>
      <th>European?</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>8708</th>
      <td>Modern and Contemporary Art</td>
      <td>1976</td>
      <td>Vase</td>
      <td>Vase</td>
      <td>American</td>
      <td>NaN</td>
      <td>George E. Ohr|Biloxi Art Pottery</td>
      <td>American</td>
      <td>1857</td>
      <td>1918</td>
      <td>|</td>
      <td>1894–1906</td>
      <td>False</td>
      <td>True</td>
    </tr>
    <tr>
      <th>87592</th>
      <td>Modern and Contemporary Art</td>
      <td>1986</td>
      <td>Painting</td>
      <td>Berthe David-Weill</td>
      <td>Spanish</td>
      <td>NaN</td>
      <td>Salvador Dalí</td>
      <td>Spanish</td>
      <td>1904</td>
      <td>1989</td>
      <td>NaN</td>
      <td>1952</td>
      <td>False</td>
      <td>True</td>
    </tr>
    <tr>
      <th>117644</th>
      <td>Modern and Contemporary Art</td>
      <td>1991</td>
      <td>Chess set and board</td>
      <td>Chess Set</td>
      <td>German (Weimar)</td>
      <td>NaN</td>
      <td>Josef Hartwig|Heinz Nösselt</td>
      <td>German</td>
      <td>1880</td>
      <td>1955</td>
      <td>|</td>
      <td>ca. 1923</td>
      <td>False</td>
      <td>True</td>
    </tr>
    <tr>
      <th>138438</th>
      <td>Modern and Contemporary Art</td>
      <td>1975</td>
      <td>Figure</td>
      <td>Woodcock</td>
      <td>American</td>
      <td>NaN</td>
      <td>Edward Marshall Boehm</td>
      <td>American</td>
      <td>1912</td>
      <td>1969</td>
      <td>NaN</td>
      <td>1940s</td>
      <td>False</td>
      <td>True</td>
    </tr>
    <tr>
      <th>243271</th>
      <td>Modern and Contemporary Art</td>
      <td>2007</td>
      <td>Drawing</td>
      <td>Head of a Man</td>
      <td>French</td>
      <td>NaN</td>
      <td>Roger de la Fresnaye</td>
      <td>French</td>
      <td>1885</td>
      <td>1925</td>
      <td>NaN</td>
      <td>1925</td>
      <td>False</td>
      <td>True</td>
    </tr>
    <tr>
      <th>...</th>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
    </tr>
    <tr>
      <th>473774</th>
      <td>Modern and Contemporary Art</td>
      <td>2002</td>
      <td>Sugar bowl and lid</td>
      <td>"Stratoware" Sugar bowl with lid</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>Eva Zeisel|Francis Blod|Universal Potteries In...</td>
      <td>NaN</td>
      <td>1906</td>
      <td>2011</td>
      <td>Female||</td>
      <td>designed ca. 1940; manufactured 1942–43</td>
      <td>False</td>
      <td>False</td>
    </tr>
    <tr>
      <th>473775</th>
      <td>Modern and Contemporary Art</td>
      <td>2002</td>
      <td>Creamer</td>
      <td>"Stratoware" Creamer</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>Eva Zeisel|Francis Blod|Universal Potteries In...</td>
      <td>NaN</td>
      <td>1906</td>
      <td>2011</td>
      <td>Female||</td>
      <td>designed ca. 1940; manufactured 1942–43</td>
      <td>False</td>
      <td>False</td>
    </tr>
    <tr>
      <th>473776</th>
      <td>Modern and Contemporary Art</td>
      <td>2002</td>
      <td>Salt shaker</td>
      <td>"Stratoware" Salt shaker</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>Universal Potteries Inc. (Cambridge, Ohio)|Fra...</td>
      <td>NaN</td>
      <td>1906</td>
      <td>2011</td>
      <td>||Female</td>
      <td>designed ca. 1940; manufactured 1942–43</td>
      <td>False</td>
      <td>False</td>
    </tr>
    <tr>
      <th>473777</th>
      <td>Modern and Contemporary Art</td>
      <td>2002</td>
      <td>Pepper shaker</td>
      <td>"Stratoware" Pepper shaker</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>Universal Potteries Inc. (Cambridge, Ohio)|Eva...</td>
      <td>NaN</td>
      <td>1906</td>
      <td>2011</td>
      <td>|Female|</td>
      <td>designed ca. 1940; manufactured 1942–43</td>
      <td>False</td>
      <td>False</td>
    </tr>
    <tr>
      <th>473778</th>
      <td>Modern and Contemporary Art</td>
      <td>2002</td>
      <td>Pitcher and lid</td>
      <td>"Stratoware" Pitcher with lid</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>Eva Zeisel|Francis Blod|Universal Potteries In...</td>
      <td>NaN</td>
      <td>1906</td>
      <td>2011</td>
      <td>Female||</td>
      <td>designed ca. 1940; manufactured 1942–43</td>
      <td>False</td>
      <td>False</td>
    </tr>
  </tbody>
</table>
<p>13452 rows × 14 columns</p>
</div>



<strong> Conclusion: </strong> This comparison shows that the MoMA, even before its renovation, was ahead of the Met with regard to artists of modern and contemporary art who were not of European or Western descent.

### Proportion of American vs Non American Works for the MoMA and the Met

One final comparison that we can look at is how the proportion of American vs Non-American differs between the MoMA and the Met as two large American museums.


```python
#Create new columns that assigns a true or false value to show if the work is by an American or not
names_dates_moma = names_dates_moma.assign(American=np.where(names_dates_moma['Nationality']=="American", True, False))
met_country_before2017 = met_country_before2017.assign(American=np.where(met_country_before2017['Artist Nationality']=="American", True, False))

#Calculate the mean of the number of American and the Non-American artists for the MoMA
american_moma = (names_dates_moma['American'] == True)
american_moma_num = american_moma.mean()
nonamerican_moma = (names_dates_moma['American'] == False)
nonamerican_moma_num = nonamerican_moma.mean()

#Calculate the mean of the number of American and the Non-American artists for the Met
america_met_before_2017 = (met_country_before2017['American'] == True)
american_met_before_2017 = america_met_before_2017.mean()
nonamerica_met_before_2017 = (met_country_before2017['American'] == False)
nonamerican_met_before_2017 = nonamerica_met_before_2017.mean()
```


```python
# Graph MoMA data!
plotdata_moma = pd.DataFrame(
    {"% of artworks": [american_moma_num,nonamerican_moma_num]}, 
    index=['Proportion of American Artists in the MoMA','Proportion of Non-American Artists in the MoMA'])
plotdata_moma.plot(kind="barh", title = "Examining National Biases MoMA")

# Graph Met data!
plotdata_tate = pd.DataFrame(
    {"% of artworks": [american_met_before_2017,nonamerican_met_before_2017]}, 
    index=['Proportion of American Artists in the Met','Proportion of Non-American Artists in the Met' ])
plotdata_tate.plot(kind="barh", title = "Examining National Biases in the Met")
```




    <AxesSubplot:title={'center':'Examining National Biases in the Met'}>




    
![png](output_212_1.png)
    



    
![png](output_212_2.png)
    


<strong>Conclusion</strong>: The MoMA out preforms the Met in diversity by having a larger number of Non-American works to American works. This shows that the MoMA as a museum places more emphasis on acquiring and retaining works from outside of America and more representational of the world as a whole. Through this, when compared to the Met, the MoMA is keeping their goal of diversifying the canon.

# Final Conclusion

We feel confident in saying that MoMA has accomplised their goal of increasing diversity. While we don't have all of the acquisitions up to the end of the renovation in 2019, we are confident based on trends and predictions that MoMA did a good job of diversifying their collection. Patterns in the data suggest that MoMA is becoming more diverse in the key areas they defined: gender, nationality, and modernity of works.


However, there were some limitations of our analysis. There was not a lot of personal information about the artists, which limited the amount of features we were able to examine. For example, we do not have information about whether or not they acquired works from more LGBTQ artists or artists of different socieoeconomic backgrounds. While we did have information about the artist's nationality, nationality can be conplex and definied in different ways. Nationality could mean the artist's country of origin or the country where the artist resides, or an artist could identify with multiple nationalities.

Additionally, nationality is not necessarily an indication of the artist's race or ethnicity. Using nationality would not make a distinction between Black Americans, Asian Americans, Indiginous Americans, and Hispanic and Latino Americans, which is not reflective of the actual diversity of the American artists. 

As we previously mentioned, we do not have the most recent data from the final years of the rennovation, so we cannot see the final statistics, but we still feel confident based on the intent and numbers from the data we have. 

The comparison with both the Tate and the Met further confirms MoMA's commitment to diversity. Even before the second rennovation was completed, MoMA already surpassed the Tate and the Met in the key categories of diversity that we were able to measure. The range of its collection show MoMA's commitment to truly expanding the canon and including underrepresented demographics. That being said, MoMA, like the other two museums, was still dominated by works by western male artists. Comparing the acquisitions of female/non-Western artists compared to all works acquired for a single year gives us a good picture of the types of works MoMA has been collected in recent years, but looking at the overall collection, there is still a lot of work to be done to be truly representative and diverse.
