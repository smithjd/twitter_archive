twitter_archive
================

## Keeping up with the R community

### There are many sources

- newsletters

- mastodon?

- Meetups

## Twitter has been important

The \#rstats hashtag opens a world. Useful to follow specific
individuals and the people they follow.

Grabbing content can be a challenge. My process has been:

- Save Web pages that are referenced in Evernote using its handy web
  clipper plugin
- Save images to my photo library and take it from there
- Selective “likes”
- Extract relevant text with an OCR browser extension
  https://github.com/amebalabs/TRex

### Getting the Twitter Archive

Request a download here: https://twitter.com/settings/download_your_data

It can take a few days for it to be ready. You’ll get a direct message
on Twitter when it’s ready to download.

### Use the built-in html interface

![](images/image-885175351.png)

### Using R to access the data from your Twitter Archive

Demonstration, looking at how to access and possible uses of `like.js`
and `following.js`.

``` r
library(tidyverse)
```

    ── Attaching packages ─────────────────────────────────────── tidyverse 1.3.2 ──
    ✔ ggplot2 3.4.0      ✔ purrr   1.0.0 
    ✔ tibble  3.1.8      ✔ dplyr   1.0.10
    ✔ tidyr   1.2.1      ✔ stringr 1.5.0 
    ✔ readr   2.1.3      ✔ forcats 0.5.2 
    ── Conflicts ────────────────────────────────────────── tidyverse_conflicts() ──
    ✖ dplyr::filter() masks stats::filter()
    ✖ dplyr::lag()    masks stats::lag()

``` r
library(here)
```

    here() starts at /Users/jds/Documents/Library/R/twitter_archive

``` r
library(jsonlite)
```


    Attaching package: 'jsonlite'

    The following object is masked from 'package:purrr':

        flatten

``` r
library(gt)
library(knitr)
```

Each archive file has some junk at the beginning that you need to skip.
Different files have a different amount of junk, so you have to figure
out how much of the file to skip.

``` r
following <- read_file(here("data", "following.js")) |>
  str_sub(start = 30) |> 
  fromJSON()

str(following[[1]])
```

    'data.frame':   418 obs. of  2 variables:
     $ accountId: chr  "257757365" "2311053678" "358192461" "102121156" ...
     $ userLink : chr  "https://twitter.com/intent/user?user_id=257757365" "https://twitter.com/intent/user?user_id=2311053678" "https://twitter.com/intent/user?user_id=358192461" "https://twitter.com/intent/user?user_id=102121156" ...

``` r
like <- read_file(here("data", "like.js")) |>
  str_sub(start = 25) |> 
  fromJSON()

str(like[[1]])
```

    'data.frame':   609 obs. of  3 variables:
     $ tweetId    : chr  "1588139679483777024" "1587544654202929153" "1586777080158642177" "1582977833869402112" ...
     $ fullText   : chr  "Maple Tree and Small Birds, by Itō Jakuchū, ca. 1765 -1766 https://t.co/mFxOD2c8oZ" "Classic inference for startup ideas:\nDeductive➡️ Amazon: Bezos picked books based on industry analysis\nInducti"| __truncated__ "How did people discover new communities in the listserv days of the internet?\n\nSeeing many discussions of gro"| __truncated__ "@sharon000 @smithjd @lorenzwalthert @krlmlr Yes, it does work with `.qmd` files! \n\nYou can download the GitHu"| __truncated__ ...
     $ expandedUrl: chr  "https://twitter.com/i/web/status/1588139679483777024" "https://twitter.com/i/web/status/1587544654202929153" "https://twitter.com/i/web/status/1586777080158642177" "https://twitter.com/i/web/status/1582977833869402112" ...

The `flatten` function is handy for pulling data frames out of JSON
structures.

``` r
following_df <- as_tibble(flatten(following$following))
glimpse(following_df)
```

    Rows: 418
    Columns: 2
    $ accountId <chr> "257757365", "2311053678", "358192461", "102121156", "285589…
    $ userLink  <chr> "https://twitter.com/intent/user?user_id=257757365", "https:…

``` r
like_df <- as_tibble(flatten(like$like))
glimpse(like_df)
```

    Rows: 609
    Columns: 3
    $ tweetId     <chr> "1588139679483777024", "1587544654202929153", "15867770801…
    $ fullText    <chr> "Maple Tree and Small Birds, by Itō Jakuchū, ca. 1765 -176…
    $ expandedUrl <chr> "https://twitter.com/i/web/status/1588139679483777024", "h…

`gt` is a handy way to have a look at text that would otherwise get
truncated.

``` r
sample_table <- like_df[1:50,2:3] |> 
  filter(str_detect(fullText,"http") & str_detect(fullText,"rstat") ) 

kable(sample_table, "simple")
```

| fullText                                                                                                                                                                                                                                                                                                        | expandedUrl                                          |
|:----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|:-----------------------------------------------------|
| I really enjoyed presenting “Level up your plots” yesterday at the @RUGatHDSI - talking about design tips and \#rstats tricks to enhance the storytelling capabilities of our \#dataviz, and how we can apply them within the context of \#academic publishing.🧵👇                                             |                                                      |
| https://t.co/SFW4xSX4bs https://twitter.com/i/web/status/1575852809995505664                                                                                                                                                                                                                                    |                                                      |
| I created a video on how to use \#QuartoPub + blastula + GitHub Actions to send automated emails ✉️ on a schedule 🕒. Check it out here! https://t.co/XA8WUYH1MU                                                                                                                                                |                                                      |
| \#rstats https://twitter.com/i/web/status/1574734992265121794                                                                                                                                                                                                                                                   |                                                      |
| Using code from @topepos, tidy tools from @rstudio, {anytime} from @eddelbuettel, and {trelliscopejs} from @hafenstats, I built a cognostic-guided EDA tool for exploring COVID-19 cases and deaths by state in less than 50 lines of R. \#rstats \#rmedicine https://t.co/P5h64NXwMl                           | https://twitter.com/i/web/status/1244653973426114566 |
| R Workflow article much improved with automatic Quarto tabs, variable recoding examples, more longitudinal data manipulation examples, creating a pop-up window data dictionary to guide analysis coding \#Statistics https://t.co/AWWpOz2nyW \#rstats @vandy_biostat @VUDataScience                            | https://twitter.com/i/web/status/1522607766141034497 |
| Here is the draft agenda for the \#rstats for \#peopleanalytics 2-day workshop at the @rstudio conference July 25th-26th. \#datascience https://t.co/L52Xau7kn4                                                                                                                                                 | https://twitter.com/i/web/status/1520025582209277955 |
| Here’s a resource I find myself using all the time. A while ago I made a sort of cheatsheet for the Theme Elements in \#ggplot2 in \#rstats. I have a terrible memory and it’s hard to remember all the names! Maybe you find it useful too. Download it here⬇️ https://t.co/gEJ7PhzsYa https://t.co/62PJYe6TDR | https://twitter.com/i/web/status/1496489734457208834 |
| An attempt at summarising how to pass columns as arguments when using tidyverse functions inside a custom function.                                                                                                                                                                                             |                                                      |

\#rstats https://t.co/J3bAznl5xT
https://twitter.com/i/web/status/1493908215796535296 Lil’ \#rstats thing
I learned today: how to easily ignore all .DS_Store files with the
git_vaccinate() function from the usethis package.
https://t.co/zzHI0sYcf3
https://twitter.com/i/web/status/1478365685390757900 I’ve written a
little R package called tabbycat for tabulating and summarising
categorical variables. It’s designed to work nicely with the tidyverse.
\#rstats https://t.co/Wn0v5gtyC2
https://twitter.com/i/web/status/1442209392070479875

## Other resources

- [Python twitter archive parser and other
  resources](https://github.com/timhutton/twitter-archive-parser)

- How to [download your twitter
  archive](https://support.twitter.com/articles/20170160)

- [A visual analysis around a twitter
  hashtag](https://blog.ouseful.info/2012/02/06/visualising-activity-round-a-twitter-hashtag-or-search-term-using-r/)

- [Albert Rapp - How to collect dataviz from Twitter into your
  note-taking
  system](https://albert-rapp.de/posts/09_get_twitter_posts_into_your_notetaking_system/09_get_twitter_posts_into_your_notetaking_system.html)
