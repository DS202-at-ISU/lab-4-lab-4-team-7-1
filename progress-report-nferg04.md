
<!-- README.md is generated from README.Rmd. Please edit the README.Rmd file -->

# Lab report \#4 - instructions

``` r
# install.packages("Lahman")
library(Lahman)
```

    ## Warning: package 'Lahman' was built under R version 4.3.2

``` r
head(HallOfFame, 3)
```

    ##    playerID yearID votedBy ballots needed votes inducted category needed_note
    ## 1  cobbty01   1936   BBWAA     226    170   222        Y   Player        <NA>
    ## 2  ruthba01   1936   BBWAA     226    170   215        Y   Player        <NA>
    ## 3 wagneho01   1936   BBWAA     226    170   215        Y   Player        <NA>

``` r
str(HallOfFame)
```

    ## 'data.frame':    4323 obs. of  9 variables:
    ##  $ playerID   : chr  "cobbty01" "ruthba01" "wagneho01" "mathech01" ...
    ##  $ yearID     : int  1936 1936 1936 1936 1936 1936 1936 1936 1936 1936 ...
    ##  $ votedBy    : chr  "BBWAA" "BBWAA" "BBWAA" "BBWAA" ...
    ##  $ ballots    : num  226 226 226 226 226 226 226 226 226 226 ...
    ##  $ needed     : num  170 170 170 170 170 170 170 170 170 170 ...
    ##  $ votes      : num  222 215 215 205 189 146 133 111 105 80 ...
    ##  $ inducted   : Factor w/ 2 levels "N","Y": 2 2 2 2 2 1 1 1 1 1 ...
    ##  $ category   : Factor w/ 4 levels "Manager","Pioneer/Executive",..: 3 3 3 3 3 3 3 3 3 3 ...
    ##  $ needed_note: chr  NA NA NA NA ...

``` r
# hf <- HallOfFame
```

``` r
library(rvest)
```

    ## 
    ## Attaching package: 'rvest'

    ## The following object is masked from 'package:readr':
    ## 
    ##     guess_encoding

``` r
url <- "https://www.baseball-reference.com/awards/hof_2023.shtml"
html <- read_html(url)
tables <- html_table(html)

head(tables[[1]], 3)
```

    ## # A tibble: 3 × 39
    ##   ``    ``          ``    ``    ``    ``    ``    ``    ``    ``    ``    ``   
    ##   <chr> <chr>       <chr> <chr> <chr> <chr> <chr> <chr> <chr> <chr> <chr> <chr>
    ## 1 Rk    Name        YoB   Votes %vote HOFm  HOFs  Yrs   WAR   WAR7  JAWS  Jpos 
    ## 2 1     Scott Rolen 6th   297   76.3% 99    40    17    70.1  43.6  56.9  55.8 
    ## 3 2     Todd Helton 5th   281   72.2% 175   59    17    61.8  46.6  54.2  53.4 
    ## # ℹ 27 more variables: `Batting Stats` <chr>, `Batting Stats` <chr>,
    ## #   `Batting Stats` <chr>, `Batting Stats` <chr>, `Batting Stats` <chr>,
    ## #   `Batting Stats` <chr>, `Batting Stats` <chr>, `Batting Stats` <chr>,
    ## #   `Batting Stats` <chr>, `Batting Stats` <chr>, `Batting Stats` <chr>,
    ## #   `Batting Stats` <chr>, `Batting Stats` <chr>, `Pitching Stats` <chr>,
    ## #   `Pitching Stats` <chr>, `Pitching Stats` <chr>, `Pitching Stats` <chr>,
    ## #   `Pitching Stats` <chr>, `Pitching Stats` <chr>, `Pitching Stats` <chr>, …

Took a while to figure out how to change from TRUE and FALS statements
to a Y or N to mark if the player was inducted into the hall of fame.
Had to add all of the columns to combine the two smoothly.

``` r
write.csv(tables[[1]], "temp.csv", row.names=FALSE)
backin <- readr::read_csv("temp.csv", skip = 1, show_col_types =FALSE)
```

    ## New names:
    ## • `G` -> `G...13`
    ## • `H` -> `H...16`
    ## • `HR` -> `HR...17`
    ## • `BB` -> `BB...20`
    ## • `G` -> `G...31`
    ## • `H` -> `H...35`
    ## • `HR` -> `HR...36`
    ## • `BB` -> `BB...37`

``` r
head(backin, 3)
```

    ## # A tibble: 3 × 39
    ##      Rk Name       YoB   Votes `%vote`  HOFm  HOFs   Yrs   WAR  WAR7  JAWS  Jpos
    ##   <dbl> <chr>      <chr> <dbl> <chr>   <dbl> <dbl> <dbl> <dbl> <dbl> <dbl> <dbl>
    ## 1     1 Scott Rol… 6th     297 76.3%      99    40    17  70.1  43.6  56.9  55.8
    ## 2     2 Todd Helt… 5th     281 72.2%     175    59    17  61.8  46.6  54.2  53.4
    ## 3     3 Billy Wag… 8th     265 68.1%     107    24    16  27.7  19.8  23.7  32.5
    ## # ℹ 27 more variables: G...13 <dbl>, AB <dbl>, R <dbl>, H...16 <dbl>,
    ## #   HR...17 <dbl>, RBI <dbl>, SB <dbl>, BB...20 <dbl>, BA <dbl>, OBP <dbl>,
    ## #   SLG <dbl>, OPS <dbl>, `OPS+` <dbl>, W <dbl>, L <dbl>, ERA <dbl>,
    ## #   `ERA+` <dbl>, WHIP <dbl>, G...31 <dbl>, GS <dbl>, SV <dbl>, IP <dbl>,
    ## #   H...35 <dbl>, HR...36 <dbl>, BB...37 <dbl>, SO <dbl>, `Pos Summary` <chr>

``` r
backin$needed <- 292
backin$inducted <- (backin$Votes > backin$needed)
backin$Name <- gsub("X-", "", backin$Name)

matchedData <- data.frame(
  playerID = backin$Name,
  yearID = 2023,
  votedBy = "BBWAA",
  ballots = 389,
  needed = 292,
  votes = as.integer(backin$Votes),
  inducted = ifelse(backin$inducted == TRUE,"Y","N"),
  category = "Player",
  needed_note = NA

)

dframe <- rbind(HallOfFame, matchedData)
```

Exporting to CSV file

``` r
write_csv(dframe, file = "combinedData.csv")
```
