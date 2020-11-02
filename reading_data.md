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
