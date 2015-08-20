
## Static mirror of analytics.usa.gov

This is a frozen, i.e. _static_ copy of [analytics.usa.gov](https://analytics.usa.gov), the federal government's dashboard of web visitor traffic. [Analytics.usa.gov](https://analytics.usa.gov) is a nice place to practice web-scraping and use of the web developer tools to examine how a modern webpage works.

If your scripts target:

https://analytics.usa.gov

You can point them at:

http://dannguyen.github.io/frozen.analytics.usa.gov

&ndash; and see what federal website traffic was like on [August 19, 2015](http://dannguyen.github.io/frozen.analytics.usa.gov).

Examples in these tutorials/assignments for [Stanford Computational Journalism](http://www.compjour.org):

- [The number of people who visited a U.S. government website using Internet Explorer 6.0 in the last 90 days ](https://github.com/compjour/search-script-scrape#task-3)
- [Watching Traffic with the Network Panel](http://www.compjour.org/tutorials/watching-traffic-network-panel/)


## How to waste time re-making this with bash

Oh sure, you could do what _The Man_ recommends by reading _The Man_'s replication documentation [at _The Man's_ Github repo](https://github.com/GSA/analytics.usa.gov):

https://github.com/GSA/analytics.usa.gov#setup

Or you could pretend to be from the 1970s and do it from your (2015-era Mac OS X) Unix terminal:

```sh
# Download the page
wget --adjust-extension --span-hosts --convert-links --backup-converted \
--no-directories --timestamping --page-requisites \
--directory-prefix=frozen.analytics.usa.gov \
--user-agent="Mac OS X" \
https://analytics.usa.gov

# Copy the Open-Sans font
curl http://www.fontsquirrel.com/fonts/download/open-sans -L -o /tmp/open-sans.zip
unzip /tmp/open-sans.zip -d fonts/

# Copy the way Google Fonts sets up the font:
curl -s "https://fonts.googleapis.com/css?family=Open+Sans:400,600,700,300.css" -o open-sans.css

# Make relative path substitutions
sed -E \
  's/url.+/format("truetype");/' \
  open-sans.css

sed -i "" '1s/.*/@import url(.\/open-sans.css);/' public_analytics.css | head -n 1
# eh i'll figure it out later... just do this regex on open-sans.css:
# Find:
# (?<=, )local\('(.+?)'\), url\(.+?\)
# Replace:
# local('\1'), url('fonts/\1.ttf')

# Find:
# OpenSans.ttf
# Replace
# OpenSans-Regular.ttf
```



### Copying the data

```
# get a list of the data jsons and make a list of them
grep -oE '/live/[A-z0-9\-]+.json' index.html.orig | cut -c 7- | 
      sort | uniq > json-list.txt

# replace the references to analytics.usa.gov as a data source
sed -i "" \
  's^data-source="https://analytics.usa.gov/data/live/^data-source="./data/live/^' \
  index.html
```

The file `To copy a fresh batch of data from their __live__ servers:

e.g. https://analytics.usa.gov/data/live/ie.json

```sh
base_url='https://analytics.usa.gov'
path="/data/live/"
mypath=".$path"
mkdir -p $mypath
while read -r fn; do
  url="$base_url$path$fn"
  fname="$mypath$fn"
  echo "Downloading $url into $fname"
  curl -s $url -o $fname --compressed
done < json-list.txt
```
