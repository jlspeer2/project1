ST 558 Project 1
================
Jessica Speer
June 11, 2020

-   [JSON overview](#json-overview)
-   [Reading JSON Data into R](#reading-json-data-into-r)
-   [Function to Return Parsed Data from NHL API](#function-to-return-parsed-data-from-nhl-api)
    -   [Using the Function](#using-the-function)

JSON overview
=============

Reading JSON Data into R
========================

Function to Return Parsed Data from NHL API
===========================================

``` r
getf <- function() {
  f<-GET("https://records.nhl.com/site/api/franchise")
  f<-content(f, "text")
  f<-fromJSON(f,flatten=TRUE)
  f<-data.frame(f)
  f<-tbl_df(f)
  f <- f %>% select(id = data.id, firstSeasonId = data.firstSeasonId, lastSeasonID = data.lastSeasonId,
                    teamName = data.teamCommonName)
  return(f)
}

getf()[1:10,]
```

    ## # A tibble: 10 x 4
    ##       id firstSeasonId lastSeasonID teamName   
    ##    <int>         <int>        <int> <chr>      
    ##  1     1      19171918           NA Canadiens  
    ##  2     2      19171918     19171918 Wanderers  
    ##  3     3      19171918     19341935 Eagles     
    ##  4     4      19191920     19241925 Tigers     
    ##  5     5      19171918           NA Maple Leafs
    ##  6     6      19241925           NA Bruins     
    ##  7     7      19241925     19371938 Maroons    
    ##  8     8      19251926     19411942 Americans  
    ##  9     9      19251926     19301931 Quakers    
    ## 10    10      19261927           NA Rangers

Using the Function
------------------
