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
```

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

``` r
gt_table <- like_df[1:100,2:3] |> 
  filter(str_detect(fullText,"http")) |> 
  gt() 
  gtsave(gt_table, "gt_table.html")
  
gt_table
```

<div id="dbgvzfontn" style="padding-left:0px;padding-right:0px;padding-top:10px;padding-bottom:10px;overflow-x:auto;overflow-y:auto;width:auto;height:auto;">
<style>html {
  font-family: -apple-system, BlinkMacSystemFont, 'Segoe UI', Roboto, Oxygen, Ubuntu, Cantarell, 'Helvetica Neue', 'Fira Sans', 'Droid Sans', Arial, sans-serif;
}

#dbgvzfontn .gt_table {
  display: table;
  border-collapse: collapse;
  margin-left: auto;
  margin-right: auto;
  color: #333333;
  font-size: 16px;
  font-weight: normal;
  font-style: normal;
  background-color: #FFFFFF;
  width: auto;
  border-top-style: solid;
  border-top-width: 2px;
  border-top-color: #A8A8A8;
  border-right-style: none;
  border-right-width: 2px;
  border-right-color: #D3D3D3;
  border-bottom-style: solid;
  border-bottom-width: 2px;
  border-bottom-color: #A8A8A8;
  border-left-style: none;
  border-left-width: 2px;
  border-left-color: #D3D3D3;
}

#dbgvzfontn .gt_heading {
  background-color: #FFFFFF;
  text-align: center;
  border-bottom-color: #FFFFFF;
  border-left-style: none;
  border-left-width: 1px;
  border-left-color: #D3D3D3;
  border-right-style: none;
  border-right-width: 1px;
  border-right-color: #D3D3D3;
}

#dbgvzfontn .gt_caption {
  padding-top: 4px;
  padding-bottom: 4px;
}

#dbgvzfontn .gt_title {
  color: #333333;
  font-size: 125%;
  font-weight: initial;
  padding-top: 4px;
  padding-bottom: 4px;
  padding-left: 5px;
  padding-right: 5px;
  border-bottom-color: #FFFFFF;
  border-bottom-width: 0;
}

#dbgvzfontn .gt_subtitle {
  color: #333333;
  font-size: 85%;
  font-weight: initial;
  padding-top: 0;
  padding-bottom: 6px;
  padding-left: 5px;
  padding-right: 5px;
  border-top-color: #FFFFFF;
  border-top-width: 0;
}

#dbgvzfontn .gt_bottom_border {
  border-bottom-style: solid;
  border-bottom-width: 2px;
  border-bottom-color: #D3D3D3;
}

#dbgvzfontn .gt_col_headings {
  border-top-style: solid;
  border-top-width: 2px;
  border-top-color: #D3D3D3;
  border-bottom-style: solid;
  border-bottom-width: 2px;
  border-bottom-color: #D3D3D3;
  border-left-style: none;
  border-left-width: 1px;
  border-left-color: #D3D3D3;
  border-right-style: none;
  border-right-width: 1px;
  border-right-color: #D3D3D3;
}

#dbgvzfontn .gt_col_heading {
  color: #333333;
  background-color: #FFFFFF;
  font-size: 100%;
  font-weight: normal;
  text-transform: inherit;
  border-left-style: none;
  border-left-width: 1px;
  border-left-color: #D3D3D3;
  border-right-style: none;
  border-right-width: 1px;
  border-right-color: #D3D3D3;
  vertical-align: bottom;
  padding-top: 5px;
  padding-bottom: 6px;
  padding-left: 5px;
  padding-right: 5px;
  overflow-x: hidden;
}

#dbgvzfontn .gt_column_spanner_outer {
  color: #333333;
  background-color: #FFFFFF;
  font-size: 100%;
  font-weight: normal;
  text-transform: inherit;
  padding-top: 0;
  padding-bottom: 0;
  padding-left: 4px;
  padding-right: 4px;
}

#dbgvzfontn .gt_column_spanner_outer:first-child {
  padding-left: 0;
}

#dbgvzfontn .gt_column_spanner_outer:last-child {
  padding-right: 0;
}

#dbgvzfontn .gt_column_spanner {
  border-bottom-style: solid;
  border-bottom-width: 2px;
  border-bottom-color: #D3D3D3;
  vertical-align: bottom;
  padding-top: 5px;
  padding-bottom: 5px;
  overflow-x: hidden;
  display: inline-block;
  width: 100%;
}

#dbgvzfontn .gt_group_heading {
  padding-top: 8px;
  padding-bottom: 8px;
  padding-left: 5px;
  padding-right: 5px;
  color: #333333;
  background-color: #FFFFFF;
  font-size: 100%;
  font-weight: initial;
  text-transform: inherit;
  border-top-style: solid;
  border-top-width: 2px;
  border-top-color: #D3D3D3;
  border-bottom-style: solid;
  border-bottom-width: 2px;
  border-bottom-color: #D3D3D3;
  border-left-style: none;
  border-left-width: 1px;
  border-left-color: #D3D3D3;
  border-right-style: none;
  border-right-width: 1px;
  border-right-color: #D3D3D3;
  vertical-align: middle;
  text-align: left;
}

#dbgvzfontn .gt_empty_group_heading {
  padding: 0.5px;
  color: #333333;
  background-color: #FFFFFF;
  font-size: 100%;
  font-weight: initial;
  border-top-style: solid;
  border-top-width: 2px;
  border-top-color: #D3D3D3;
  border-bottom-style: solid;
  border-bottom-width: 2px;
  border-bottom-color: #D3D3D3;
  vertical-align: middle;
}

#dbgvzfontn .gt_from_md > :first-child {
  margin-top: 0;
}

#dbgvzfontn .gt_from_md > :last-child {
  margin-bottom: 0;
}

#dbgvzfontn .gt_row {
  padding-top: 8px;
  padding-bottom: 8px;
  padding-left: 5px;
  padding-right: 5px;
  margin: 10px;
  border-top-style: solid;
  border-top-width: 1px;
  border-top-color: #D3D3D3;
  border-left-style: none;
  border-left-width: 1px;
  border-left-color: #D3D3D3;
  border-right-style: none;
  border-right-width: 1px;
  border-right-color: #D3D3D3;
  vertical-align: middle;
  overflow-x: hidden;
}

#dbgvzfontn .gt_stub {
  color: #333333;
  background-color: #FFFFFF;
  font-size: 100%;
  font-weight: initial;
  text-transform: inherit;
  border-right-style: solid;
  border-right-width: 2px;
  border-right-color: #D3D3D3;
  padding-left: 5px;
  padding-right: 5px;
}

#dbgvzfontn .gt_stub_row_group {
  color: #333333;
  background-color: #FFFFFF;
  font-size: 100%;
  font-weight: initial;
  text-transform: inherit;
  border-right-style: solid;
  border-right-width: 2px;
  border-right-color: #D3D3D3;
  padding-left: 5px;
  padding-right: 5px;
  vertical-align: top;
}

#dbgvzfontn .gt_row_group_first td {
  border-top-width: 2px;
}

#dbgvzfontn .gt_summary_row {
  color: #333333;
  background-color: #FFFFFF;
  text-transform: inherit;
  padding-top: 8px;
  padding-bottom: 8px;
  padding-left: 5px;
  padding-right: 5px;
}

#dbgvzfontn .gt_first_summary_row {
  border-top-style: solid;
  border-top-color: #D3D3D3;
}

#dbgvzfontn .gt_first_summary_row.thick {
  border-top-width: 2px;
}

#dbgvzfontn .gt_last_summary_row {
  padding-top: 8px;
  padding-bottom: 8px;
  padding-left: 5px;
  padding-right: 5px;
  border-bottom-style: solid;
  border-bottom-width: 2px;
  border-bottom-color: #D3D3D3;
}

#dbgvzfontn .gt_grand_summary_row {
  color: #333333;
  background-color: #FFFFFF;
  text-transform: inherit;
  padding-top: 8px;
  padding-bottom: 8px;
  padding-left: 5px;
  padding-right: 5px;
}

#dbgvzfontn .gt_first_grand_summary_row {
  padding-top: 8px;
  padding-bottom: 8px;
  padding-left: 5px;
  padding-right: 5px;
  border-top-style: double;
  border-top-width: 6px;
  border-top-color: #D3D3D3;
}

#dbgvzfontn .gt_striped {
  background-color: rgba(128, 128, 128, 0.05);
}

#dbgvzfontn .gt_table_body {
  border-top-style: solid;
  border-top-width: 2px;
  border-top-color: #D3D3D3;
  border-bottom-style: solid;
  border-bottom-width: 2px;
  border-bottom-color: #D3D3D3;
}

#dbgvzfontn .gt_footnotes {
  color: #333333;
  background-color: #FFFFFF;
  border-bottom-style: none;
  border-bottom-width: 2px;
  border-bottom-color: #D3D3D3;
  border-left-style: none;
  border-left-width: 2px;
  border-left-color: #D3D3D3;
  border-right-style: none;
  border-right-width: 2px;
  border-right-color: #D3D3D3;
}

#dbgvzfontn .gt_footnote {
  margin: 0px;
  font-size: 90%;
  padding-left: 4px;
  padding-right: 4px;
  padding-left: 5px;
  padding-right: 5px;
}

#dbgvzfontn .gt_sourcenotes {
  color: #333333;
  background-color: #FFFFFF;
  border-bottom-style: none;
  border-bottom-width: 2px;
  border-bottom-color: #D3D3D3;
  border-left-style: none;
  border-left-width: 2px;
  border-left-color: #D3D3D3;
  border-right-style: none;
  border-right-width: 2px;
  border-right-color: #D3D3D3;
}

#dbgvzfontn .gt_sourcenote {
  font-size: 90%;
  padding-top: 4px;
  padding-bottom: 4px;
  padding-left: 5px;
  padding-right: 5px;
}

#dbgvzfontn .gt_left {
  text-align: left;
}

#dbgvzfontn .gt_center {
  text-align: center;
}

#dbgvzfontn .gt_right {
  text-align: right;
  font-variant-numeric: tabular-nums;
}

#dbgvzfontn .gt_font_normal {
  font-weight: normal;
}

#dbgvzfontn .gt_font_bold {
  font-weight: bold;
}

#dbgvzfontn .gt_font_italic {
  font-style: italic;
}

#dbgvzfontn .gt_super {
  font-size: 65%;
}

#dbgvzfontn .gt_footnote_marks {
  font-style: italic;
  font-weight: normal;
  font-size: 75%;
  vertical-align: 0.4em;
}

#dbgvzfontn .gt_asterisk {
  font-size: 100%;
  vertical-align: 0;
}

#dbgvzfontn .gt_indent_1 {
  text-indent: 5px;
}

#dbgvzfontn .gt_indent_2 {
  text-indent: 10px;
}

#dbgvzfontn .gt_indent_3 {
  text-indent: 15px;
}

#dbgvzfontn .gt_indent_4 {
  text-indent: 20px;
}

#dbgvzfontn .gt_indent_5 {
  text-indent: 25px;
}
</style>
<table class="gt_table">
  
  <thead class="gt_col_headings">
    <tr>
      <th class="gt_col_heading gt_columns_bottom_border gt_left" rowspan="1" colspan="1" scope="col" id="fullText">fullText</th>
      <th class="gt_col_heading gt_columns_bottom_border gt_left" rowspan="1" colspan="1" scope="col" id="expandedUrl">expandedUrl</th>
    </tr>
  </thead>
  <tbody class="gt_table_body">
    <tr><td headers="fullText" class="gt_row gt_left">Maple Tree and Small Birds, by Itō Jakuchū, ca. 1765 -1766 https://t.co/mFxOD2c8oZ</td>
<td headers="expandedUrl" class="gt_row gt_left">https://twitter.com/i/web/status/1588139679483777024</td></tr>
    <tr><td headers="fullText" class="gt_row gt_left">Classic inference for startup ideas:
Deductive➡️ Amazon: Bezos picked books based on industry analysis
Inductive➡️ Quickbooks: Intuit heard complaints from folks using Quicken for business
Abductive➡️ Slack: As Glitch failed, they had an epiphany that their chat tool was valuable https://t.co/ft1vtxOBQG</td>
<td headers="expandedUrl" class="gt_row gt_left">https://twitter.com/i/web/status/1587544654202929153</td></tr>
    <tr><td headers="fullText" class="gt_row gt_left">Spring in Mount Atago, by Kawase Hasui, 1921

#shinhanga https://t.co/58HrPfML0U</td>
<td headers="expandedUrl" class="gt_row gt_left">https://twitter.com/i/web/status/1580547904548356096</td></tr>
    <tr><td headers="fullText" class="gt_row gt_left">A young woman from Kansei period playing with her cat, by Tsukioka Yoshitoshi, 1888

#ukiyoe https://t.co/TKNwiStqh1</td>
<td headers="expandedUrl" class="gt_row gt_left">https://twitter.com/i/web/status/1579664835586838528</td></tr>
    <tr><td headers="fullText" class="gt_row gt_left">One of the most interesting videos I’ve ever seen. https://t.co/ZQeMJyPNfO</td>
<td headers="expandedUrl" class="gt_row gt_left">https://twitter.com/i/web/status/1578807784887705600</td></tr>
    <tr><td headers="fullText" class="gt_row gt_left">Miyamoto Musashi Beating Whale, by Utagawa Kuniyoshi, 1848-1854

#ukiyoe 

https://t.co/pmU1R2bh0H https://t.co/jH8Drh7r0w</td>
<td headers="expandedUrl" class="gt_row gt_left">https://twitter.com/i/web/status/1578858019374465024</td></tr>
    <tr><td headers="fullText" class="gt_row gt_left">Waxwing on maple tree, by Ohara Koson, 1900s

#shinhanga https://t.co/N0dcSSplxW</td>
<td headers="expandedUrl" class="gt_row gt_left">https://twitter.com/i/web/status/1576165024393043968</td></tr>
    <tr><td headers="fullText" class="gt_row gt_left">I really enjoyed presenting "Level up your plots" yesterday at the @RUGatHDSI - talking about design tips and #rstats tricks to enhance the storytelling capabilities of our #dataviz, and how we can apply them within the context of #academic publishing.🧵👇
https://t.co/SFW4xSX4bs</td>
<td headers="expandedUrl" class="gt_row gt_left">https://twitter.com/i/web/status/1575852809995505664</td></tr>
    <tr><td headers="fullText" class="gt_row gt_left">From Pictures of Flowers and Birds, by Okamoto Shūki, 19th century https://t.co/HcTK2mg4Kr</td>
<td headers="expandedUrl" class="gt_row gt_left">https://twitter.com/i/web/status/1576061089703493635</td></tr>
    <tr><td headers="fullText" class="gt_row gt_left">I created a video on how to use #QuartoPub + blastula + GitHub Actions to send automated emails ✉️ on a schedule 🕒. Check it out here!  https://t.co/XA8WUYH1MU
#rstats</td>
<td headers="expandedUrl" class="gt_row gt_left">https://twitter.com/i/web/status/1574734992265121794</td></tr>
    <tr><td headers="fullText" class="gt_row gt_left">Imado Bridge and Matsuchi Hill, by Utagawa Hiroshige, 1862

#ukiyoe https://t.co/qufSAu2hCD</td>
<td headers="expandedUrl" class="gt_row gt_left">https://twitter.com/i/web/status/1573052515335032832</td></tr>
    <tr><td headers="fullText" class="gt_row gt_left">Crow on a Branch by Kawanabe Kyōsai, ca 1887

#nihonga https://t.co/9ei1XtW4dj</td>
<td headers="expandedUrl" class="gt_row gt_left">https://twitter.com/i/web/status/1572902527032561665</td></tr>
    <tr><td headers="fullText" class="gt_row gt_left">Mallards and full moon, by Ohara Koson, 1900s

#shinhanga https://t.co/6db5v1g03o</td>
<td headers="expandedUrl" class="gt_row gt_left">https://twitter.com/i/web/status/1571571003347574791</td></tr>
    <tr><td headers="fullText" class="gt_row gt_left">For something we do all the time (there are 55 million a day!), most organizations don’t spend enough time trying to make meetings better. Here is a synthesis of the literature on effective meetings, compiled into 10 rules (all in the images). Experiment! https://t.co/NfUdjBj50K https://t.co/82i7kntymH</td>
<td headers="expandedUrl" class="gt_row gt_left">https://twitter.com/i/web/status/1569480818258751491</td></tr>
    <tr><td headers="fullText" class="gt_row gt_left">Arranging flowers and replacing a hanging scroll with a new one, from the series A Tea Ceremony Periwinkle, by Mizuno Toshikata, 1897

#ukiyoe https://t.co/CiUHDQpcG4</td>
<td headers="expandedUrl" class="gt_row gt_left">https://twitter.com/i/web/status/1568270002335145984</td></tr>
    <tr><td headers="fullText" class="gt_row gt_left">Teams need to focus on learning, not just doing. 

When teams focus only on getting a job done, they perform worse than teams that also spend time learning from each other. Groups should be encouraged to think about learning for the future, not just on what they are doing now. https://t.co/JBkoFGy3H0</td>
<td headers="expandedUrl" class="gt_row gt_left">https://twitter.com/i/web/status/1567923007586181122</td></tr>
    <tr><td headers="fullText" class="gt_row gt_left">Boy seated on a tree trunk playing a flute, attributed to Katsushika Hokusai, late 18th century-early 19th century

#ukiyoe https://t.co/s5cb5lzo6q</td>
<td headers="expandedUrl" class="gt_row gt_left">https://twitter.com/i/web/status/1567014479409205248</td></tr>
    <tr><td headers="fullText" class="gt_row gt_left">Cockatoo and Pomegranate, by Ohara Shōson (Koson), 20th century

#shinhanga https://t.co/TLfgY3sWnr</td>
<td headers="expandedUrl" class="gt_row gt_left">https://twitter.com/i/web/status/1566368975788843011</td></tr>
    <tr><td headers="fullText" class="gt_row gt_left">Diabutsu Temple Gate, by Yoshida Hiroshi, 1940

#shinhanga https://t.co/5N73vakmUl</td>
<td headers="expandedUrl" class="gt_row gt_left">https://twitter.com/i/web/status/1565762228229324800</td></tr>
    <tr><td headers="fullText" class="gt_row gt_left">Famous Heroes of the Kabuki Stage Played by Frogs, by Utagawa Kuniyoshi, 19th century

#ukiyoe https://t.co/Mgx2agtOn5</td>
<td headers="expandedUrl" class="gt_row gt_left">https://twitter.com/i/web/status/1562757428906639360</td></tr>
    <tr><td headers="fullText" class="gt_row gt_left">Fireworks Like Flowers in Bloom at Ryôgoku Bridge in the Eastern Capital, by Utagawa Toyoharu, 1770s

#ukiyoe https://t.co/e1IOpivB1M</td>
<td headers="expandedUrl" class="gt_row gt_left">https://twitter.com/i/web/status/1562986940726665217</td></tr>
    <tr><td headers="fullText" class="gt_row gt_left">Mice transcribing a book, by Kawanabe Kyōsai, ca. 1870s

#ukiyoe https://t.co/zuyA6np99F</td>
<td headers="expandedUrl" class="gt_row gt_left">https://twitter.com/i/web/status/1562999272722890752</td></tr>
    <tr><td headers="fullText" class="gt_row gt_left">100 years different in athletic ability https://t.co/sv5Kn8pqbg</td>
<td headers="expandedUrl" class="gt_row gt_left">https://twitter.com/i/web/status/1557066043134279686</td></tr>
    <tr><td headers="fullText" class="gt_row gt_left">@unisaacarroyov @sponce1 @USGS @FGazzelloni code: https://t.co/kY7OKUu92B</td>
<td headers="expandedUrl" class="gt_row gt_left">https://twitter.com/i/web/status/1556596923938746373</td></tr>
    <tr><td headers="fullText" class="gt_row gt_left">I pulled 5,000 tweets with the hashtag #rstudioconf2022 using {rtweet} and made a fun lil word cloud with {wordcloud}! Should I be worried python is so prominent? Or is that just people quoting @hadleywickham saying he refuses to learn 🐍?

Code:https://t.co/sfDNlmwA2t
#RStats https://t.co/UWoxxAxK6V</td>
<td headers="expandedUrl" class="gt_row gt_left">https://twitter.com/i/web/status/1555601574034038784</td></tr>
    <tr><td headers="fullText" class="gt_row gt_left">Jianzhen, 1189

#sculputure https://t.co/7oqdwhG7Hx</td>
<td headers="expandedUrl" class="gt_row gt_left">https://twitter.com/i/web/status/1555780203649159168</td></tr>
    <tr><td headers="fullText" class="gt_row gt_left">@datavisFriendly @f2harrell Here’s a ton of resources https://t.co/YkOFHhaRgW</td>
<td headers="expandedUrl" class="gt_row gt_left">https://twitter.com/i/web/status/1555327593359978496</td></tr>
    <tr><td headers="fullText" class="gt_row gt_left">YESSSSS LETS GOOOOO #rstudioconf2022 https://t.co/WTNPyHiqLZ</td>
<td headers="expandedUrl" class="gt_row gt_left">https://twitter.com/i/web/status/1552767410607898624</td></tr>
    <tr><td headers="fullText" class="gt_row gt_left">the cutest guy ever followed me on ig today https://t.co/Jt6wdBZS2p</td>
<td headers="expandedUrl" class="gt_row gt_left">https://twitter.com/i/web/status/1545078485907083265</td></tr>
    <tr><td headers="fullText" class="gt_row gt_left">What a picture… https://t.co/TaSLrbn5sz</td>
<td headers="expandedUrl" class="gt_row gt_left">https://twitter.com/i/web/status/1537398315997970436</td></tr>
    <tr><td headers="fullText" class="gt_row gt_left">Using code from @topepos, tidy tools from @rstudio, {anytime} from @eddelbuettel, and  {trelliscopejs} from @hafenstats, I built a cognostic-guided EDA tool for exploring COVID-19 cases and deaths by state in less than 50 lines of R. #rstats #rmedicine https://t.co/P5h64NXwMl</td>
<td headers="expandedUrl" class="gt_row gt_left">https://twitter.com/i/web/status/1244653973426114566</td></tr>
    <tr><td headers="fullText" class="gt_row gt_left">R Workflow article much improved with automatic Quarto tabs, variable recoding examples, more longitudinal data manipulation examples, creating a pop-up window data dictionary to guide analysis coding #Statistics  https://t.co/AWWpOz2nyW #rstats @vandy_biostat @VUDataScience</td>
<td headers="expandedUrl" class="gt_row gt_left">https://twitter.com/i/web/status/1522607766141034497</td></tr>
    <tr><td headers="fullText" class="gt_row gt_left">Here is the draft agenda for the #rstats for #peopleanalytics 2-day workshop at the @rstudio conference July 25th-26th.  #datascience https://t.co/L52Xau7kn4</td>
<td headers="expandedUrl" class="gt_row gt_left">https://twitter.com/i/web/status/1520025582209277955</td></tr>
    <tr><td headers="fullText" class="gt_row gt_left">want to rename 192 variables w/o writing 192 lines of #RStats code? i got you! 👯

i'm still learning about the power💪of the `!!!` splice operator 🔪💉, but for now i'll happily accept the magic! 💫🧙‍♂️ https://t.co/UDVBjmNxwq https://t.co/IycIDLWHIl</td>
<td headers="expandedUrl" class="gt_row gt_left">https://twitter.com/i/web/status/1497014703473704965</td></tr>
    <tr><td headers="fullText" class="gt_row gt_left">Here's a resource I find myself using all the time. A while ago I made a sort of cheatsheet for the Theme Elements in #ggplot2 in #rstats. I have a terrible memory and it's hard to remember all the names! Maybe you find it useful too. Download it here⬇️ https://t.co/gEJ7PhzsYa https://t.co/62PJYe6TDR</td>
<td headers="expandedUrl" class="gt_row gt_left">https://twitter.com/i/web/status/1496489734457208834</td></tr>
    <tr><td headers="fullText" class="gt_row gt_left">An attempt at summarising how to pass columns as arguments when using tidyverse functions inside a custom function.

#rstats https://t.co/J3bAznl5xT</td>
<td headers="expandedUrl" class="gt_row gt_left">https://twitter.com/i/web/status/1493908215796535296</td></tr>
    <tr><td headers="fullText" class="gt_row gt_left">Lecture slides for my 'Data Science Programming Methods' course STAT 447 from this Fall 2021 at U of Illinois are now accessible via https://t.co/hHewPJY3pl covering shell (incl sed/awk), markdown, git(hub), sql, lots of #Rstats up to packaging, and Docker.  Enjoy! https://t.co/Dv6XSO1ZWj</td>
<td headers="expandedUrl" class="gt_row gt_left">https://twitter.com/i/web/status/1484910046295306245</td></tr>
    <tr><td headers="fullText" class="gt_row gt_left">Lil' #rstats thing I learned today: how to easily ignore all .DS_Store files with the git_vaccinate() function from the usethis package. https://t.co/zzHI0sYcf3</td>
<td headers="expandedUrl" class="gt_row gt_left">https://twitter.com/i/web/status/1478365685390757900</td></tr>
    <tr><td headers="fullText" class="gt_row gt_left">I've written a little R package called tabbycat for tabulating and summarising categorical variables. It's designed to work nicely with the tidyverse. #rstats https://t.co/Wn0v5gtyC2</td>
<td headers="expandedUrl" class="gt_row gt_left">https://twitter.com/i/web/status/1442209392070479875</td></tr>
    <tr><td headers="fullText" class="gt_row gt_left">Michael K. Williams talking to different versions of himself on getting typecast as a black man. https://t.co/uRowCxU4Tf</td>
<td headers="expandedUrl" class="gt_row gt_left">https://twitter.com/i/web/status/1434982712104816640</td></tr>
    <tr><td headers="fullText" class="gt_row gt_left">an amazing directory: /proc https://t.co/717UPpvami</td>
<td headers="expandedUrl" class="gt_row gt_left">https://twitter.com/i/web/status/981159808832286720</td></tr>
    <tr><td headers="fullText" class="gt_row gt_left">This Guardian article has been shared 95,000 times, exposing 'all the data Facebook &amp;amp; Google have on you'.

But it doesn't mention at all that the article itself uses:

- Four sets of Facebook tracking.
- Nine sets of Google tracking.
- Two Google ads, using all that data. https://t.co/V8Jd3zdAJs</td>
<td headers="expandedUrl" class="gt_row gt_left">https://twitter.com/i/web/status/979571712961507328</td></tr>
    <tr><td headers="fullText" class="gt_row gt_left">#rstats package dev Friday afternoon: TFW u teach these guys something they don’t know #roxygen #rstudioshortcut @emilelatour @old_man_chester https://t.co/F9cvCpMb4K</td>
<td headers="expandedUrl" class="gt_row gt_left">https://twitter.com/i/web/status/979866397416210434</td></tr>
    <tr><td headers="fullText" class="gt_row gt_left">Of course I am biased, but reticulate has the qualities I most appreciate in @rstudio’s work:
✔ highly functional
✔ thoughtful interface
✔ documented
@fly_upside_down always leads by example https://t.co/wdsgtSNw9V</td>
<td headers="expandedUrl" class="gt_row gt_left">https://twitter.com/i/web/status/978427020488065025</td></tr>
    <tr><td headers="fullText" class="gt_row gt_left">As a new parent, podcasts and the radio suddenly make way more sense. 👀 👶🐵👿👼 I can thoroughly recommend @DataCamp's 'Dataframed' https://t.co/GjHBl2w2xc 👌</td>
<td headers="expandedUrl" class="gt_row gt_left">https://twitter.com/i/web/status/977672061182033920</td></tr>
    <tr><td headers="fullText" class="gt_row gt_left">Certainly the only one in this office that gets to make rainbow plots #rstats https://t.co/unrexYnl04</td>
<td headers="expandedUrl" class="gt_row gt_left">https://twitter.com/i/web/status/977305268584370176</td></tr>
    <tr><td headers="fullText" class="gt_row gt_left">Calling all BC, WA, OR, etc. #rstats folk! Portland, @Powells, @VoodooDoughnut, above-average public transportation, and generalized weirdness … what’s not to love? https://t.co/z8JfaheWzz</td>
<td headers="expandedUrl" class="gt_row gt_left">https://twitter.com/i/web/status/974830966043688962</td></tr>
    <tr><td headers="fullText" class="gt_row gt_left">👍 series on useful 📦s!
"R packages for summarising data – pt 2" by @adam_medcalf 
https://t.co/cv2N0UuMmt #rstats #ggalley https://t.co/nWg7dvp1fQ</td>
<td headers="expandedUrl" class="gt_row gt_left">https://twitter.com/i/web/status/971385513465794562</td></tr>
    <tr><td headers="fullText" class="gt_row gt_left">Rbloggers My Proposal to the R Consortium https://t.co/vGBt1zhaV7 #rstats #DataScience</td>
<td headers="expandedUrl" class="gt_row gt_left">https://twitter.com/i/web/status/971416157457731584</td></tr>
    <tr><td headers="fullText" class="gt_row gt_left">I really enjoyed @smithjd’s idea of R as a living growing organism, fitting in the highlights of #rstudioconf and #rstats contributors into @hadleywickham’s import/tidy/transform/viz/model/communicate framework. https://t.co/Z1l5tU15Me</td>
<td headers="expandedUrl" class="gt_row gt_left">https://twitter.com/i/web/status/968505864662364160</td></tr>
    <tr><td headers="fullText" class="gt_row gt_left">Thanks everyone for coming! Hope you learned something about #shiny and #rstats. If you didn't come, we still want you to learn - workshop materials are below! https://t.co/jn9MRTQRw4</td>
<td headers="expandedUrl" class="gt_row gt_left">https://twitter.com/i/web/status/971252902244306946</td></tr>
    <tr><td headers="fullText" class="gt_row gt_left">New #rstats post! "hrbrpkgs: list @hrbrmstr's packages"

Using GitHub V4 API thanks to @rOpenSci packages ghql and jqr!

https://t.co/lb6gKv86s0 https://t.co/lSmC5Bk6BK</td>
<td headers="expandedUrl" class="gt_row gt_left">https://twitter.com/i/web/status/970256000715579393</td></tr>
    <tr><td headers="fullText" class="gt_row gt_left">🕵️‍♂️ step-by-step: what can we learn from @drob's code? 
"How To Learn R, Part 1: Learn From A Master Data Scientist's Code" by @mdancho84 
https://t.co/jHWCvY2sxu #rstats #tidyverse https://t.co/5O1ufCjqii</td>
<td headers="expandedUrl" class="gt_row gt_left">https://twitter.com/i/web/status/969935940985778176</td></tr>
    <tr><td headers="fullText" class="gt_row gt_left">One of the best articles I've read  in a long time. This article describes the opioid epidemic in the US and connects it to the loneliness of late stage capitalism in the US. Beautifully written and deeply insightful. Make yourself a favor and read it. https://t.co/fTN3yTXEmi</td>
<td headers="expandedUrl" class="gt_row gt_left">https://twitter.com/i/web/status/967764634185977856</td></tr>
    <tr><td headers="fullText" class="gt_row gt_left">I've documented my workflow for opening @RStudio projects (using on @alfredapp) in  this short video: https://t.co/XvmRyGSsol #rstats</td>
<td headers="expandedUrl" class="gt_row gt_left">https://twitter.com/i/web/status/968624630276804608</td></tr>
    <tr><td headers="fullText" class="gt_row gt_left">Join @rdpeng and me on #DataFramed, a @DataCamp podcast, for a great conversation about #datascience, the environment, #MOOCs, open source &amp;amp; more! It's episode 10 so lets celebrate! https://t.co/5KHqQKlnRM https://t.co/J5Z6AKZRGT</td>
<td headers="expandedUrl" class="gt_row gt_left">https://twitter.com/i/web/status/965967911314747392</td></tr>
    <tr><td headers="fullText" class="gt_row gt_left">I am writing up my notes from #rstduiconf (blog post coming!) and wanted to have a quick picture to go with my thoughts. Remembering @ma_salmon 's post on Faces of R (here: https://t.co/C1sRW3hwVL ), I decided to make a Faces of Rstudioconf! https://t.co/vtNVn2RyHV</td>
<td headers="expandedUrl" class="gt_row gt_left">https://twitter.com/i/web/status/961466502821052416</td></tr>
    <tr><td headers="fullText" class="gt_row gt_left">From Karl Popper's 1945 The Open Society &amp;amp; Its Enemies. It reads like something that it could have been written today. Read starting from: "All political parties .... " https://t.co/GRDoaGU6sd</td>
<td headers="expandedUrl" class="gt_row gt_left">https://twitter.com/i/web/status/964939340219604992</td></tr>
    <tr><td headers="fullText" class="gt_row gt_left">This is a very powerful post from @xieyihui - it made me that-much-more appreciative of my #rstats fam.

For me, well worth the quiet cup of coffee to read properly.

https://t.co/PpjxcRVyJk</td>
<td headers="expandedUrl" class="gt_row gt_left">https://twitter.com/i/web/status/964895839553540096</td></tr>
    <tr><td headers="fullText" class="gt_row gt_left">I'm giving a new &amp;amp; improved #rstats #shiny talk today at #CSP2018! 

+ tidbits from the amazing things I learned @ #rstudioconf:
📊 viz inference/#nullabor a la @visnut
💫 #shinytest/promises for debugging/speed
👩‍💻 #xaringan w/ #rladies theme!

slides: https://t.co/UbY86lb3rj</td>
<td headers="expandedUrl" class="gt_row gt_left">https://twitter.com/i/web/status/964545547775369216</td></tr>
    <tr><td headers="fullText" class="gt_row gt_left">New blog post: The Importance of Sponsorship. Covering what is sponsorship, how it differs from mentorship, how to find sponsors, how to be one, and some personal thank yous: 

https://t.co/k4QH7ULhor</td>
<td headers="expandedUrl" class="gt_row gt_left">https://twitter.com/i/web/status/964153851715751936</td></tr>
    <tr><td headers="fullText" class="gt_row gt_left">What happens when you start #rstats? Well, it's quite simple, it's just... https://t.co/00S7l4dSTj</td>
<td headers="expandedUrl" class="gt_row gt_left">https://twitter.com/i/web/status/961553618196418560</td></tr>
    <tr><td headers="fullText" class="gt_row gt_left">*whispers* check out the R bindings to Python available in the reticulate package. The quietly awesome work behind the TensorFlow bindings. https://t.co/d23fQkKOVS #rstudioconf</td>
<td headers="expandedUrl" class="gt_row gt_left">https://twitter.com/i/web/status/959854846345015296</td></tr>
    <tr><td headers="fullText" class="gt_row gt_left">My new thing is ending every Rmd with a list of links to the forums / SO questions / blogs / github repos that I used to solve the problem #rstats https://t.co/U51KT9kiym</td>
<td headers="expandedUrl" class="gt_row gt_left">https://twitter.com/i/web/status/956835956073615360</td></tr>
    <tr><td headers="fullText" class="gt_row gt_left">So, I mis-tweeted the other day. Today's webinbar was for ISDS, not CDC. They're a great #rstats group &amp;amp; the slides + code for the talk are here: https://t.co/anPyuFsRSQ

They'll have video up at some point &amp;amp; @kyle_e_walker is there next month!!

Got a shout out to @ropensci, too</td>
<td headers="expandedUrl" class="gt_row gt_left">https://twitter.com/i/web/status/956226546041262080</td></tr>
    <tr><td headers="fullText" class="gt_row gt_left">"The Data Analyst as Wanderer: Pre-Exploratory Data Analysis w/ R" ✏️ @daranzolin https://t.co/yBneo8hAtm (w. @nj_tierney's visdat) #rstats https://t.co/0qwpz7H44r</td>
<td headers="expandedUrl" class="gt_row gt_left">https://twitter.com/i/web/status/956165227627806720</td></tr>
    <tr><td headers="fullText" class="gt_row gt_left">Great to have another #DataScience podcast around! Checkout this episode of #DataFramed with @ma_salmon being interviewed about #Datascience in public health &amp;amp; her thoughts on the #rstats community. https://t.co/w3He0Ker4Y</td>
<td headers="expandedUrl" class="gt_row gt_left">https://twitter.com/i/web/status/955880532109389825</td></tr>
    <tr><td headers="fullText" class="gt_row gt_left">I had a great convo w/ @hugobowne on DataFramed, a @DataCamp podcast, which launched today. My episode will be coming out soon; meanwhile, hear from @drob, @ma_salmon, @hmason, and more awesome DS folks! https://t.co/8pzlicFjNi https://t.co/hEMStQegnO</td>
<td headers="expandedUrl" class="gt_row gt_left">https://twitter.com/i/web/status/955563436431245312</td></tr>
    <tr><td headers="fullText" class="gt_row gt_left">New blog post: "Exploring handwritten digit classification: a tidy analysis of the MNIST dataset" https://t.co/fuhnBfuA6o https://t.co/dsJuwW89Xl</td>
<td headers="expandedUrl" class="gt_row gt_left">https://twitter.com/i/web/status/955525515061886979</td></tr>
    <tr><td headers="fullText" class="gt_row gt_left">Which civil and political rights do countries violate? Another #rstats blog post where I used #rvest to scrape and analyze 40 years of complaints to the Human Rights Committee. https://t.co/QWhOkG2geI @CCPR_Centre @UNHumanRights https://t.co/xLD7nReNT5</td>
<td headers="expandedUrl" class="gt_row gt_left">https://twitter.com/i/web/status/952192300217749505</td></tr>
    <tr><td headers="fullText" class="gt_row gt_left">If you currently use RPostgreSQL or RMySQL, check out RPostgres (https://t.co/RhX796QlKa) &amp;amp; RMariaDB (https://t.co/RGsKyZT0sq). These have many tests + modern C++ backends + active maintainer + much polishing. Thanks to @krlmlr + @RConsortium! #rstats</td>
<td headers="expandedUrl" class="gt_row gt_left">https://twitter.com/i/web/status/950378112759541761</td></tr>
    <tr><td headers="fullText" class="gt_row gt_left">The best and most comprehensive twitter summary of Why Information Grows. I couldn't have done better myself. https://t.co/uY1X8kO5sO</td>
<td headers="expandedUrl" class="gt_row gt_left">https://twitter.com/i/web/status/947859349971636225</td></tr>
    <tr><td headers="fullText" class="gt_row gt_left">Introducing the Field Guide to the #rstats Ecosystem https://t.co/Hfrs1fi74u</td>
<td headers="expandedUrl" class="gt_row gt_left">https://twitter.com/i/web/status/947909537859809281</td></tr>
    <tr><td headers="fullText" class="gt_row gt_left">Do you rely on open source tools for your research? If rOpenSci is important to you, here’s a link to donate, matched by @NumFOCUS. At checkout, you can choose to designate your gift to support rOpenSci. https://t.co/Pk9JNJZRe8 https://t.co/VlWsuTewgk</td>
<td headers="expandedUrl" class="gt_row gt_left">https://twitter.com/i/web/status/947120089152626689</td></tr>
    <tr><td headers="fullText" class="gt_row gt_left">RegExr is going directly to my browsers bookmark collection of frequently used tools https://t.co/utNxl61my9</td>
<td headers="expandedUrl" class="gt_row gt_left">https://twitter.com/i/web/status/945675647732432896</td></tr>
  </tbody>
  
  
</table>
</div>

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
