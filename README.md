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

getNHL("goalierecs", 38)
```

    ## # A tibble: 7 x 30
    ##   data.id data.activePlay~ data.firstName data.franchiseId data.franchiseN~
    ##     <int> <lgl>            <chr>                     <int> <chr>           
    ## 1     279 TRUE             Marc-Andre                   38 Vegas Golden Kn~
    ## 2     285 TRUE             Maxime                       38 Vegas Golden Kn~
    ## 3     286 TRUE             Oscar                        38 Vegas Golden Kn~
    ## 4     287 TRUE             Malcolm                      38 Vegas Golden Kn~
    ## 5     288 TRUE             Dylan                        38 Vegas Golden Kn~
    ## 6    1292 TRUE             Garret                       38 Vegas Golden Kn~
    ## 7    1298 TRUE             Robin                        38 Vegas Golden Kn~
    ## # ... with 25 more variables: data.gameTypeId <int>,
    ## #   data.gamesPlayed <int>, data.lastName <chr>, data.losses <int>,
    ## #   data.mostGoalsAgainstDates <chr>, data.mostGoalsAgainstOneGame <int>,
    ## #   data.mostSavesDates <chr>, data.mostSavesOneGame <int>,
    ## #   data.mostShotsAgainstDates <chr>, data.mostShotsAgainstOneGame <int>,
    ## #   data.mostShutoutsOneSeason <int>, data.mostShutoutsSeasonIds <chr>,
    ## #   data.mostWinsOneSeason <int>, data.mostWinsSeasonIds <chr>,
    ## #   data.overtimeLosses <int>, data.playerId <int>,
    ## #   data.positionCode <chr>, data.rookieGamesPlayed <int>,
    ## #   data.rookieShutouts <int>, data.rookieWins <int>, data.seasons <int>,
    ## #   data.shutouts <int>, data.ties <int>, data.wins <int>, total <int>

Using the Function
------------------
