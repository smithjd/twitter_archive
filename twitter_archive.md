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
  filter(str_detect(fullText,"http")) 

kable(sample_table, "simple")
```

| fullText                                                                                                                                                                 | expandedUrl                                          |
|:-------------------------------------------------------------------------------------------------------------------------------------------------------------------------|:-----------------------------------------------------|
| Maple Tree and Small Birds, by Itō Jakuchū, ca. 1765 -1766 https://t.co/mFxOD2c8oZ                                                                                       | https://twitter.com/i/web/status/1588139679483777024 |
| Classic inference for startup ideas:                                                                                                                                     |                                                      |
| Deductive➡️ Amazon: Bezos picked books based on industry analysis                                                                                                        |                                                      |
| Inductive➡️ Quickbooks: Intuit heard complaints from folks using Quicken for business                                                                                    |                                                      |
| Abductive➡️ Slack: As Glitch failed, they had an epiphany that their chat tool was valuable https://t.co/ft1vtxOBQG https://twitter.com/i/web/status/1587544654202929153 |                                                      |
| Spring in Mount Atago, by Kawase Hasui, 1921                                                                                                                             |                                                      |

\#shinhanga https://t.co/58HrPfML0U
https://twitter.com/i/web/status/1580547904548356096 A young woman from
Kansei period playing with her cat, by Tsukioka Yoshitoshi, 1888

\#ukiyoe https://t.co/TKNwiStqh1
https://twitter.com/i/web/status/1579664835586838528 One of the most
interesting videos I’ve ever seen. https://t.co/ZQeMJyPNfO
https://twitter.com/i/web/status/1578807784887705600 Miyamoto Musashi
Beating Whale, by Utagawa Kuniyoshi, 1848-1854

\#ukiyoe

https://t.co/pmU1R2bh0H https://t.co/jH8Drh7r0w
https://twitter.com/i/web/status/1578858019374465024 Waxwing on maple
tree, by Ohara Koson, 1900s

\#shinhanga https://t.co/N0dcSSplxW
https://twitter.com/i/web/status/1576165024393043968 I really enjoyed
presenting “Level up your plots” yesterday at the @RUGatHDSI - talking
about design tips and \#rstats tricks to enhance the storytelling
capabilities of our \#dataviz, and how we can apply them within the
context of \#academic publishing.🧵👇 https://t.co/SFW4xSX4bs
https://twitter.com/i/web/status/1575852809995505664 From Pictures of
Flowers and Birds, by Okamoto Shūki, 19th century
https://t.co/HcTK2mg4Kr
https://twitter.com/i/web/status/1576061089703493635 I created a video
on how to use \#QuartoPub + blastula + GitHub Actions to send automated
emails ✉️ on a schedule 🕒. Check it out here! https://t.co/XA8WUYH1MU
\#rstats https://twitter.com/i/web/status/1574734992265121794 Imado
Bridge and Matsuchi Hill, by Utagawa Hiroshige, 1862

\#ukiyoe https://t.co/qufSAu2hCD
https://twitter.com/i/web/status/1573052515335032832 Crow on a Branch by
Kawanabe Kyōsai, ca 1887

\#nihonga https://t.co/9ei1XtW4dj
https://twitter.com/i/web/status/1572902527032561665 Mallards and full
moon, by Ohara Koson, 1900s

\#shinhanga https://t.co/6db5v1g03o
https://twitter.com/i/web/status/1571571003347574791 For something we do
all the time (there are 55 million a day!), most organizations don’t
spend enough time trying to make meetings better. Here is a synthesis of
the literature on effective meetings, compiled into 10 rules (all in the
images). Experiment! https://t.co/NfUdjBj50K https://t.co/82i7kntymH
https://twitter.com/i/web/status/1569480818258751491 Arranging flowers
and replacing a hanging scroll with a new one, from the series A Tea
Ceremony Periwinkle, by Mizuno Toshikata, 1897

\#ukiyoe https://t.co/CiUHDQpcG4
https://twitter.com/i/web/status/1568270002335145984 Teams need to focus
on learning, not just doing.

When teams focus only on getting a job done, they perform worse than
teams that also spend time learning from each other. Groups should be
encouraged to think about learning for the future, not just on what they
are doing now. https://t.co/JBkoFGy3H0
https://twitter.com/i/web/status/1567923007586181122 Boy seated on a
tree trunk playing a flute, attributed to Katsushika Hokusai, late 18th
century-early 19th century

\#ukiyoe https://t.co/s5cb5lzo6q
https://twitter.com/i/web/status/1567014479409205248 Cockatoo and
Pomegranate, by Ohara Shōson (Koson), 20th century

\#shinhanga https://t.co/TLfgY3sWnr
https://twitter.com/i/web/status/1566368975788843011 Diabutsu Temple
Gate, by Yoshida Hiroshi, 1940

\#shinhanga https://t.co/5N73vakmUl
https://twitter.com/i/web/status/1565762228229324800 Famous Heroes of
the Kabuki Stage Played by Frogs, by Utagawa Kuniyoshi, 19th century

\#ukiyoe https://t.co/Mgx2agtOn5
https://twitter.com/i/web/status/1562757428906639360 Fireworks Like
Flowers in Bloom at Ryôgoku Bridge in the Eastern Capital, by Utagawa
Toyoharu, 1770s

\#ukiyoe https://t.co/e1IOpivB1M
https://twitter.com/i/web/status/1562986940726665217 Mice transcribing a
book, by Kawanabe Kyōsai, ca. 1870s

\#ukiyoe https://t.co/zuyA6np99F
https://twitter.com/i/web/status/1562999272722890752 100 years different
in athletic ability https://t.co/sv5Kn8pqbg
https://twitter.com/i/web/status/1557066043134279686 @unisaacarroyov
@sponce1 @USGS @FGazzelloni code: https://t.co/kY7OKUu92B
https://twitter.com/i/web/status/1556596923938746373 I pulled 5,000
tweets with the hashtag \#rstudioconf2022 using {rtweet} and made a fun
lil word cloud with {wordcloud}! Should I be worried python is so
prominent? Or is that just people quoting @hadleywickham saying he
refuses to learn 🐍?

Code:https://t.co/sfDNlmwA2t \#RStats https://t.co/UWoxxAxK6V
https://twitter.com/i/web/status/1555601574034038784 Jianzhen, 1189

\#sculputure https://t.co/7oqdwhG7Hx
https://twitter.com/i/web/status/1555780203649159168 @datavisFriendly
@f2harrell Here’s a ton of resources https://t.co/YkOFHhaRgW
https://twitter.com/i/web/status/1555327593359978496 YESSSSS LETS GOOOOO
\#rstudioconf2022 https://t.co/WTNPyHiqLZ
https://twitter.com/i/web/status/1552767410607898624 the cutest guy ever
followed me on ig today https://t.co/Jt6wdBZS2p
https://twitter.com/i/web/status/1545078485907083265 What a picture…
https://t.co/TaSLrbn5sz
https://twitter.com/i/web/status/1537398315997970436 Using code from
@topepos, tidy tools from @rstudio, {anytime} from @eddelbuettel, and
{trelliscopejs} from @hafenstats, I built a cognostic-guided EDA tool
for exploring COVID-19 cases and deaths by state in less than 50 lines
of R. \#rstats \#rmedicine https://t.co/P5h64NXwMl
https://twitter.com/i/web/status/1244653973426114566 R Workflow article
much improved with automatic Quarto tabs, variable recoding examples,
more longitudinal data manipulation examples, creating a pop-up window
data dictionary to guide analysis coding \#Statistics
https://t.co/AWWpOz2nyW \#rstats @vandy_biostat @VUDataScience
https://twitter.com/i/web/status/1522607766141034497 Here is the draft
agenda for the \#rstats for \#peopleanalytics 2-day workshop at the
@rstudio conference July 25th-26th. \#datascience
https://t.co/L52Xau7kn4
https://twitter.com/i/web/status/1520025582209277955 want to rename 192
variables w/o writing 192 lines of \#RStats code? i got you! 👯

i’m still learning about the power💪of the `!!!` splice operator 🔪💉,
but for now i’ll happily accept the magic! 💫🧙‍♂️ https://t.co/UDVBjmNxwq
https://t.co/IycIDLWHIl
https://twitter.com/i/web/status/1497014703473704965 Here’s a resource I
find myself using all the time. A while ago I made a sort of cheatsheet
for the Theme Elements in \#ggplot2 in \#rstats. I have a terrible
memory and it’s hard to remember all the names! Maybe you find it useful
too. Download it here⬇️ https://t.co/gEJ7PhzsYa https://t.co/62PJYe6TDR
https://twitter.com/i/web/status/1496489734457208834 An attempt at
summarising how to pass columns as arguments when using tidyverse
functions inside a custom function.

\#rstats https://t.co/J3bAznl5xT
https://twitter.com/i/web/status/1493908215796535296 Lecture slides for
my ‘Data Science Programming Methods’ course STAT 447 from this Fall
2021 at U of Illinois are now accessible via https://t.co/hHewPJY3pl
covering shell (incl sed/awk), markdown, git(hub), sql, lots of \#Rstats
up to packaging, and Docker. Enjoy! https://t.co/Dv6XSO1ZWj
https://twitter.com/i/web/status/1484910046295306245 Lil’ \#rstats thing
I learned today: how to easily ignore all .DS_Store files with the
git_vaccinate() function from the usethis package.
https://t.co/zzHI0sYcf3
https://twitter.com/i/web/status/1478365685390757900 I’ve written a
little R package called tabbycat for tabulating and summarising
categorical variables. It’s designed to work nicely with the tidyverse.
\#rstats https://t.co/Wn0v5gtyC2
https://twitter.com/i/web/status/1442209392070479875 Michael K. Williams
talking to different versions of himself on getting typecast as a black
man. https://t.co/uRowCxU4Tf
https://twitter.com/i/web/status/1434982712104816640

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
