# Nutch

## Installation

1) Download the Apache Nutch source (apache-nutch-1.X-src.zip).

2) Extract it and change directory:

```
> tar xf apache-nutch-1.X-src.zip
> cd apache-nutch-1.X/
```

3) Run ant:

```
> ant
```

4) [OPTIONAL] Setup permissions and JAVA_HOME:

```
> chmod +x bin/nutch
> export JAVA_HOME=$(readlink -f /usr/bin/java | sed "s:bin/java::")
```

5) Our Nutch build is now ready at:

```
> cd runtime/local
```

I will refer to this henceforth directory as ```${NUTCH_DIR}```

## Configuration

We need to customize three properties at minimum to crawl our first website.

### Crawl Properties

The default nutch crwaler properties are found in ```${NUTCH_DIR}/conf/nutch-default.xml``` and are overriden by the properties in ```${NUTCH_DIR}/conf/nutch-site.xml```. 

We will need to add one property to ```conf/nutch-site.xml``` to initiate the search.

```
<property>
 <name>http.agent.name</name>
 <value>Quick Search Crawler</value>
</property>
```

### Initial seed list

A URL seed list includes a list of websites, one-per-line, which nutch will look to crawl. We can create this list by using the following commands and adding the seed urls:

```
mkdir -p ${NUTCH_DIR}/urls
cd ${NUTCH_DIR}/urls
vi seed.txt
```

Our initial seed file will only contain the UMD Libraries Homepage:

```
https://www.lib.umd.edu/
```

### Regular Expression Filters

Nutch will go through the content in ```${NUTCH_DIR}/conf/regex-urlfilter.txt``` line by line and stop at the first matching rule. If the regular expression in a line matches the URL, it will include (```+```) or exclude (```-```) the URL depending on the sign, and skip the remaining lines. If there is no matching regular expression by the end of the file, it will discard the URL.

Our filter will only contain the https versions of the UMD Libraries URLs and discard ```dbfinder``` and other URLs:

```
# reject DBfinder URLs
-^https?://(www\.)*lib\.umd\.edu/dbfinder

# accept https UMD Libraries URLs
+^https://(www\.)?lib\.umd\.edu

# reject anything else
-.
```

Your searcher configurations are ready now.


## Crawling

In order to crawl the website, you will need to run the following command from 
```${NUTCH_DIR}```:

```
bin/crawl -i -D solr.server.url=http://localhost:8983/solr/nutch -s urls/ LibCrawl/  10
```

This will generate the seed list from files in the ```urls``` directory, index the results on the Solr URL ```http://localhost:8983/solr/nutch```, generate the crawl and link databases in the ```LibCrawl``` directory, and run this search upto depth ```10```.

You can also run the same crawl without indexing it to the Solr URL:

```
bin/crawl -s urls/ LibCrawl/  10
```