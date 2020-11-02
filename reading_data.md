Reading data from a web
================

## scrape a table

I want the first table from this page:
<http://samhda.s3-us-gov-west-1.amazonaws.com/s3fs-public/field-uploads/2k15StateFiles/NSDUHsaeShortTermCHG2015.htm>

read in the html

``` r
url = "http://samhda.s3-us-gov-west-1.amazonaws.com/s3fs-public/field-uploads/2k15StateFiles/NSDUHsaeShortTermCHG2015.htm"

drug_use_html = read_html(url)
```

extract the table(s)

``` r
#extract the first table in the web
#也可以利用nth()， last()选取别的table
tabl_marj =
  drug_use_html %>% 
  html_nodes(css = "table") %>% 
  first() %>%                  #选取第一个table
  html_table() %>%             #Parse an html table into a data frame.
  slice(-1) %>%                # remove the first row
  as_tibble()
```

## star wars movie info

I want the data from \[here\]:<https://www.imdb.com/list/ls070150896/>

``` r
url = "https://www.imdb.com/list/ls070150896/"

swm_html = read_html(url)
```

Grab elements that I want

``` r
# use select gadget to determine what kind of movie title it use
title_vec = 
  swm_html %>% 
  html_nodes(css = ".lister-item-header a") %>% ###".lister-item-header a"这个东西是用gadget 查出来的，每次都不不同
  html_text()

gross_rev_vec = 
  swm_html %>% 
  html_nodes(css = ".text-small:nth-child(7) span:nth-child(5)") %>% 
  html_text()

runtime_vec = 
  swm_html %>% 
  html_nodes(css = ".runtime") %>% 
  html_text()

swm_df = 
  tibble(
    title = title_vec,
    gross_rev = gross_rev_vec,
    runtime = runtime_vec
  )
```

## Get some water data

This is coming from an API

``` r
nyc_water = 
  GET("https://data.cityofnewyork.us/resource/ia2d-e54m.csv") %>% # GET(API)
  content("parsed") #just parse the content, 会直接recognize as csv
```

    ## Parsed with column specification:
    ## cols(
    ##   year = col_double(),
    ##   new_york_city_population = col_double(),
    ##   nyc_consumption_million_gallons_per_day = col_double(),
    ##   per_capita_gallons_per_person_per_day = col_double()
    ## )

``` r
  # 一般都是API csv,这段code是为了解释如果是json file如何处理
nyc_water =
  GET("https://data.cityofnewyork.us/resource/ia2d-e54m.json") %>%  
  content("text") %>% 
  jsonlite::fromJSON() %>% 
  as_tibble
```

## BRFSS

Same process, different data

``` r
# page through data:https://dev.socrata.com/docs/paging.html
brdss_2010 = 
  GET("https://chronicdata.cdc.gov/resource/acme-vg9e.csv") %>% 
  content("parsed")   # 发现只出现1000rows，但实际13k rows。 因为API paging through data
```

    ## Parsed with column specification:
    ## cols(
    ##   .default = col_character(),
    ##   year = col_double(),
    ##   sample_size = col_double(),
    ##   data_value = col_double(),
    ##   confidence_limit_low = col_double(),
    ##   confidence_limit_high = col_double(),
    ##   display_order = col_double(),
    ##   locationid = col_logical()
    ## )

    ## See spec(...) for full column specifications.

``` r
brdss_2010 = 
  GET("https://chronicdata.cdc.gov/resource/acme-vg9e.csv",
      query = list("$limit" = 5000)) %>%   ###instead of request 1000 rows, but request 5000 rows
  content("parsed") 
```

    ## Parsed with column specification:
    ## cols(
    ##   .default = col_character(),
    ##   year = col_double(),
    ##   sample_size = col_double(),
    ##   data_value = col_double(),
    ##   confidence_limit_low = col_double(),
    ##   confidence_limit_high = col_double(),
    ##   display_order = col_double(),
    ##   locationid = col_logical()
    ## )
    ## See spec(...) for full column specifications.
