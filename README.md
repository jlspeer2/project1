ST 558 Project 1
================
Jessica Speer
June 11, 2020

-   [JSON overview](#json-overview)
-   [Reading JSON Data into R](#reading-json-data-into-r)
-   [Function to Return Parsed Data from NHL API](#function-to-return-parsed-data-from-nhl-api)
    -   [Using the Function](#using-the-function)
-   [Explore the NHL Data](#explore-the-nhl-data)

JSON overview
=============

Reading JSON Data into R
========================

Function to Return Parsed Data from NHL API
===========================================

The function that is generated below enables the user to pull several data sets from the NHL API.

**Usage:**

`getNHL(dataset, franchiseID)`

**Arguments:**

`dataset`: Enter `"franchise"`, `"teamtotals"`, `"seasonrecs"`, `"goalierecs"`, or `"skaterrecs"`

`franchiseID`: Valid Franchise ID required for `"seasonrecs"`, `"goalierecs"`, and `"skaterrecs"`

**Datasets:**

`"franchise"`: Returns id, firstSeasonId and lastSeasonId and name of every team in the history of the NHL

`"teamtotals"`: Returns total stats for every franchise (ex roadTies, roadWins, etc)

`"seasonrecs"`: Drill-down into season records for a specific franchise

`"goalierecs"`: Goalie records for the specified franchise

`"skaterrecs"`: (Skater records, same interaction as goalie endpoint

**Function generation:**

``` r
getNHL <- function(str1=NULL, id=NULL) {
  if (is.null(str1)){
    return("Please enter desired data and Franchise ID (if applicable)")
  } 
  else
  s1<-stri_compare(str1, "franchise")
  if (s1==0){
    f<-GET("https://records.nhl.com/site/api/franchise")
    f<-content(f, "text")
    f<-fromJSON(f,flatten=TRUE)
    f<-data.frame(f)
    f<-tbl_df(f)
    f <- f %>% select(data.id, data.firstSeasonId, data.lastSeasonId, data.teamCommonName)
    return(f)
  }
  else
  s2<-stri_compare(str1, "teamtotals")
  if (s2==0){
    f<-GET("https://records.nhl.com/site/api/franchise-team-totals")
    f<-content(f, "text")
    f<-fromJSON(f,flatten=TRUE)
    f<-data.frame(f)
    f<-tbl_df(f)
    return(f)
  }
  else
  s3<-stri_compare(str1, "seasonrecs")
  s4<-stri_compare(str1, "goalierecs")
  s5<-stri_compare(str1, "skaterrecs")
  if ((s3==0 | s4==0 | s5==0) & is.null(id)){
    return("Please enter a Franchise ID in the second argument.")
  }
  else
  `%notin%` <- Negate(`%in%`)
  if ((s3==0 | s4==0 | s5==0) & id %notin% 1:38){
    return("Franchise ID not recognized.")
  }  
  else
  if (s3==0 & id %in% 1:38){
    f<-GET(paste0("https://records.nhl.com/site/api/franchise-season-records?cayenneExp=franchiseId=", id))
    f<-content(f, "text")
    f<-fromJSON(f,flatten=TRUE)
    f<-data.frame(f)
    f<-tbl_df(f)
    return(f)
  }
  else
  if (s4==0 & id %in% 1:38){
    f<-GET(paste0("https://records.nhl.com/site/api/franchise-goalie-records?cayenneExp=franchiseId=", id))
    f<-content(f, "text")
    f<-fromJSON(f,flatten=TRUE)
    f<-data.frame(f)
    f<-tbl_df(f)
    return(f)
  }
  else
  if (s5==0 & id %in% 1:38){
    f<-GET(paste0("https://records.nhl.com/site/api/franchise-skater-records?cayenneExp=franchiseId=", id))
    f<-content(f, "text")
    f<-fromJSON(f,flatten=TRUE)
    f<-data.frame(f)
    f<-tbl_df(f)
    return(f)
  }  
}
```

Using the Function
------------------

The function can be used to call several NHL data sets from the API. Examples are illustrated below.

``` r
#Call Franchise data, select variables
getNHL("franchise") %>% select(data.firstSeasonId, data.teamCommonName) %>% head(n=10)
```

    ## # A tibble: 10 x 2
    ##    data.firstSeasonId data.teamCommonName
    ##                 <int> <chr>              
    ##  1           19171918 Canadiens          
    ##  2           19171918 Wanderers          
    ##  3           19171918 Eagles             
    ##  4           19191920 Tigers             
    ##  5           19171918 Maple Leafs        
    ##  6           19241925 Bruins             
    ##  7           19241925 Maroons            
    ##  8           19251926 Americans          
    ##  9           19251926 Quakers            
    ## 10           19261927 Rangers

``` r
#Call Team Totals data, select variables
getNHL("teamtotals") %>% select(data.teamName, data.homeWins) %>% head(n=10)
```

    ## # A tibble: 10 x 2
    ##    data.teamName       data.homeWins
    ##    <chr>                       <int>
    ##  1 New Jersey Devils             783
    ##  2 New Jersey Devils              74
    ##  3 New York Islanders            942
    ##  4 New York Islanders             84
    ##  5 New York Rangers             1600
    ##  6 New York Rangers              137
    ##  7 Philadelphia Flyers           131
    ##  8 Philadelphia Flyers          1204
    ##  9 Pittsburgh Penguins          1116
    ## 10 Pittsburgh Penguins           111

``` r
#Call Season Records data, select variables
getNHL("seasonrecs", 38) %>% select(data.franchiseName, data.homeWinStreak)
```

    ## # A tibble: 1 x 2
    ##   data.franchiseName   data.homeWinStreak
    ##   <chr>                             <int>
    ## 1 Vegas Golden Knights                  8

``` r
#Call Goalie Records data, select variables
getNHL("goalierecs", 38) %>% select(data.firstName, data.lastName, data.wins)
```

    ## # A tibble: 7 x 3
    ##   data.firstName data.lastName data.wins
    ##   <chr>          <chr>             <int>
    ## 1 Marc-Andre     Fleury               91
    ## 2 Maxime         Lagace                6
    ## 3 Oscar          Dansk                 3
    ## 4 Malcolm        Subban               30
    ## 5 Dylan          Ferguson              0
    ## 6 Garret         Sparks                0
    ## 7 Robin          Lehner                3

``` r
#Call Skater Records data, select variables
getNHL("skaterrecs", 38) %>% select(data.firstName, data.lastName, data.points) %>% head(n=10)
```

    ## # A tibble: 10 x 3
    ##    data.firstName data.lastName data.points
    ##    <chr>          <chr>               <int>
    ##  1 Deryk          Engelland              41
    ##  2 James          Neal                   44
    ##  3 Ryan           Reaves                 37
    ##  4 David          Perron                 66
    ##  5 Jason          Garrison                1
    ##  6 Luca           Sbisa                  14
    ##  7 Brayden        McNabb                 40
    ##  8 Reilly         Smith                 167
    ##  9 Tomas          Tatar                   6
    ## 10 Brandon        Pirri                  23

Explore the NHL Data
====================

``` r
t<-getNHL("teamtotals")
t<-t[t$data.gameTypeId==2,]
t$WinRatio<-t$data.wins/t$data.gamesPlayed
avgWins<-mean(t$WinRatio)
t$PMinRatio<-t$data.penaltyMinutes/t$data.gamesPlayed
avgPMins<-mean(t$PMinRatio)
t$WinCat<-NA
t$WinCat[t$WinRatio < avgWins] <- "Below Avg Wins"
t$WinCat[t$WinRatio >= avgWins] <- "At/Above Avg Wins"
t$PMinCat<-NA
t$PMinCat[t$PMinRatio < avgPMins] <- "Below Avg Penalty Mins"
t$PMinCat[t$PMinRatio >= avgPMins] <- "At/Above Avg Penalty Mins"
t1<-table(t$WinCat, t$PMinCat)
knitr::kable(t1, row.names=TRUE, caption = "Avg Wins vs Avg Penality Min")
```

|                   |  At/Above Avg Penalty Mins|  Below Avg Penalty Mins|
|-------------------|--------------------------:|-----------------------:|
| At/Above Avg Wins |                         24|                      12|
| Below Avg Wins    |                          5|                      16|

``` r
g <- ggplot(t, aes(x=t$WinCat, y=t$data.homeWins)) + geom_boxplot()
g
```

![](st558proj1_files/figure-markdown_github/unnamed-chunk-8-1.png)

``` r
g <- ggplot(t, aes(x=t$WinCat, y=t$data.homeLosses)) + geom_boxplot()
g
```

![](st558proj1_files/figure-markdown_github/unnamed-chunk-8-2.png)
