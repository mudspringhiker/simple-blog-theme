---
layout: post
title: Wrangling OpenStreetMap Data
tags: ["data_wrangling", "Python", "SQLite", "OSM_XML"]
category: projects
repository:
    is_project_page: true
    repository_url: https://github.com/mudspringhiker/openstreetmap_datawrangling
---

One of the projects I did for the Udacity Data Analyst Nanodegree is the data wrangling of an [OpenStreetMap](https://www.openstreetmap.org/#map=19/30.27482/-97.75237){:target="_blank"} dataset. The project demonstrates the data wrangling process from XML files which is the type of data [OpenStreetMap](https://www.openstreetmap.org/#map=19/30.27482/-97.75237){:target="_blank"} is. It is human-edited, and hence, is inherently "dirty". The process involves parsing, cleaning, and converting the data into a database. The database was then queried for exploration.

### Map Area

I chose the Austin, TX area for this project. An extract for this area already existed and was downloaded. This file was at least 50 MB and just right for a simple data wrangling exercise. Instructions on how to find and download a dataset from OpenStreetMap was illustrated somewhere (see current Udacity course of data wrangling).

### The OSM XML

To understand how to parse the OSM XML, we need to understand its structure. It contains various elements, and an element is composed on a block of nodes, a block of ways, and a block of relations, aside from the character encoding. An example of an XML code is shown in the [OSM XML wiki](https://wiki.openstreetmap.org/wiki/OSM_XML){:target="_blank"}.

![](./assets/img/2016-10-23-data-wrangling-openstreetmap/osmxml.png)

Looking at the [wiki](https://wiki.openstreetmap.org/wiki/OSM_XML){:target="_blank"} and as can be seen above, a *node* block contains information such as "latitude", "longitude", and "node id", among other attributes. A *ways* block "is an ordered list of nodes, which has at least one *tag*, or is included within a *relation*".

### Problems Encountered in the Map

Prior to cleaning the whole map, a small part of it was parsed first and explored. The code used to do this is shown [here](https://github.com/mudspringhiker/openstreetmap_datawrangling/blob/master/exploration_audit.ipynb){:target="_blank"}. The code was highly dependent on `xml` and `regex` modules of Python. After exploring the sample file, some problems that needed attention were:

1. Street names, some of which were abbreviated
2. Inconsistent abbreviation of street names (for example, 'IH35', 'I H 35', 'I-35', and 'I35' all mean the same thing)
3. Phone numbers also didn't have the same format (ex. '(512) 782-5659', '+1 512-472-1666', '51224990093', '512 466-3937', etc.)
4. Postcodes didn't have a consistent format as well
5. City names were also not uniform

### Cleaning of Street Names

Here are some examples of data cleaning done in the project:

    North IH 35 => North Interstate Highway 35
    Calhoun Ln => Calhoun Lane
    FM 685 => Farm-to-Market Road 685
    W. University Avenue,Ste 320 => West University Avenue Suite 320

The ["clean function"](https://github.com/mudspringhiker/openstreetmap_datawrangling/blob/master/clean.py){:target="_blank"} I created to fix the formats was able to distinguish between some abbreviations:

    Avenue H => Avenue H (H stayed the same)
    N I H 35 Bldg 7 => North Interstate Highway 35 Building 7 (H was converted to Highway)

This was made possible using the `regex` module (see [code](https://github.com/mudspringhiker/openstreetmap_datawrangling/blob/master/clean.py){:target="_blank"} on how I did this). However, after the updates, there were still some problems remaining after the clean up, such as certain streets have different names. For example, Ranch Road 620 is also referred to as Farm-to-Market Road 620, US Highway 290 is also Country Road 290. These were not addressed in the project although it could be easily added to the ["mapping_street" dictionary](https://github.com/mudspringhiker/openstreetmap_datawrangling/blob/master/mapping_street.py){:target="_blank"} used by the [clean function](https://github.com/mudspringhiker/openstreetmap_datawrangling/blob/master/clean.py){:target="_blank"}.

### Parsing of OSM Data to CSV Files

Data were parsed from the OSM file using ["data.py"](https://github.com/mudspringhiker/openstreetmap_datawrangling/blob/master/data.py){:target="_blank"}, a program provided by the course instructor. In short, this program "parses the OSM XML file and transforms them form document format to tabular format, making it possible to write to csv files". It uses `iterparse` to step through each top level element in the XML, shape each element into several data structures using `shape_element` function (which uses the `clean` function mentioned above), use a schema and validation library to ensure the transformed data is in the correct format, and write each data structure to the appropriate csv file (see docstring in the "data.py" program). Three csv files were created, all corresponding to three XML attributes discussed under **"OSM XML"** above.

- `nodes.csv`
- `nodes_tags.csv`
- `ways.csv`


### Creating the SQL Database (SQLite)

Creation of the SQLite database was straightforward using the `sqlite` module. The code for this task is stored [here](https://github.com/mudspringhiker/openstreetmap_datawrangling/blob/master/db_creation.ipynb).

### Querrying the SQL Database

#### Checking for effectiveness of the clean function

Querrying the database created can be a way to determine if cleaning was effective. For example, querrying for cities showed that the cities names were found to be fixed.

```
cities = cur.execute("""SELECT tags.value, COUNT(*) as count
                        FROM (SELECT * FROM nodes_tags
                          UNION ALL
                          SELECT * FROM ways_tags) tags
                        WHERE tags.key = 'city'
                        GROUP BY tags.value
                        ORDER By count DESC""").fetchall()
print cities

Out: [(u'Austin', 3068),
    (u'Round Rock', 113),
    (u'Kyle', 64),
    (u'Cedar Park', 43),
    (u'Pflugerville', 37), .....
```
However, looking at the postcodes (querried using a similar code as that used for querrying for cities), there were three "None" values. To figure out what these should be, I querried for the accompanying information with these values.

```
missing_postcodes = cur.execute("""SELECT *
                                   FROM (SELECT * FROM nodes_tags
                                     UNION ALL
                                     SELECT * FROM ways_tags) tags
                                   WHERE tags.key = 'postcode'
                                   AND tags.value = 'None'""").fetchall()

print missing_postcodes

Out:    [(2152207067, u'postcode', u'None', u'addr'),
        (247506590, u'postcode', u'None', u'addr'),
        (383791236, u'postcode', u'None', u'addr')]
```

To determine what info is accompanying id 2152207067, the following query was done:

```
cur.execute("""SELECT *
         FROM (SELECT * FROM nodes_tags
         UNION ALL
         SELECT * FROM ways_tags) tags
         WHERE tags.id = 2152207067""")
cur.fetchall()
Out: [(2152207067, u'name', u'Nyle Maxwell - Taylor', u'regular'),
    (2152207067, u'shop', u'car', u'regular'),
    (2152207067, u'website', u'www.nylemaxwellcjd.com', u'regular'),
    (2152207067, u'street', u'United States Highway 79', u'addr'),
    (2152207067, u'postcode', u'None', u'addr')]
```

From this result and accessing the provided website, it can be seen that the postcode should be 76574. The other missing postcodes were determined in the same way.

#### Locations of restaurants

Exploring the locations of all restaurants recorded in the OSM XML file used involved using the following code in Python:

```
cuisine_loc = cur.execute("""SELECT b.id, b.value, nodes.lat, nodes.lon
                             FROM (SELECT * FROM nodes_tags UNION ALL SELECT * FROM ways_tags) b
                               JOIN nodes ON b.id = nodes.id
                             WHERE b.key = 'cuisine'""").fetchall()
```


### Overview of (Meta)Data

The following are some information about the dataset:

#### File sizes
```
austin_texas.osm        1.41 GB
atx_osm.db              820.4 MB
nodes.csv               604.3 MB
nodes_tags.csv          11.7 MB
ways.csv                48.6 MB
ways_tags.csv           70.6 MB
ways_nodes.csv          175.6 MB
```

#### Number of Nodes

```
In [17]: cur.execute("SELECT COUNT(*) FROM nodes")
         nodes = cur.fetchall()
         nodes
Out[17]:
         [(6356394,)]
```

#### Number of Ways

```
In [18]: cur.execute("SELECT COUNT(*) FROM ways")
         ways = cur.fetchall()
         ways
Out[18]:
        [(666390,)]
```

#### Number of Users/Contributors

```
In [19]: cur.execute("""SELECT COUNT(DISTINCT(e.uid))
                        FROM (SELECT uid from nodes UNION ALL SELECT uid FROM ways) e""")
         users = cur.fetchall()
         users
Out[19]:
         [(1146,)]
```

#### Top 10 Contributing Users

```
cur.execute("""SELECT e.user, COUNT(*) as num
         FROM (SELECT user FROM nodes UNION ALL SELECT user FROM ways) e
         GROUP BY e.user
         ORDER BY num DESC
         LIMIT 10""").fetchall()
Output:

[(u'patisilva_atxbuildings', 2743705),
 (u'ccjjmartin_atxbuildings', 1300514),
 (u'ccjjmartin__atxbuildings', 940070),
 (u'wilsaj_atxbuildings', 359124),
 (u'jseppi_atxbuildings', 300983),
 (u'woodpeck_fixbot', 223425),
 (u'kkt_atxbuildings', 157847),
 (u'lyzidiamond_atxbuildings', 156383),
 (u'richlv', 50212),
 (u'johnclary_axtbuildings', 48232)]
```

### Suggestions for Improvement of Data

One aspect that always crop up during clean up of my data was loss of data, such as in the case where one of two phone numbers provided gets discarded. This may be remedied by using a list as value for the field. However, the validation check will flag this and create an error. A non-SQL database might be more applicable in handling this case.

Another problem with the data itself is the presence of more than one field names for one type of data. When the values of attribute 'k' was explored, there were at least two "fix me"'s as values. There were also more than one for phone numbers and postal codes. A standardization of the k values should be instituted by OpenStreetMap. Anything that does not fit the list of these k values should create an error upon data entry for contributors. Also, the format for the values might also be standardized. A disadvantage of such rules however, might discourage contributors causing a slow development of OSM. However, if an automated cleaning program is instituted, it might be ok.

### Conclusion

Information from an xml file can be extracted for data by Python through the xml.eTree.ElementTree module. This can be converted to a csv file which can be converted to an sql database (or to a pandas dataframe, which is not shown here, but was explored in another unit of the course). SQL databases can be converted to a pandas dataframe.

Cleaning of data takes a while. Knowledge of the nature of data also is very important so the best decisions on what to do with it can be done.

### References

- [iterparse method](http://effbot.org/zone/celementtree.htm)
- [Data Wrangling with MongoDB](https://www.udacity.com/course/data-wrangling-with-mongodb--ud032)
