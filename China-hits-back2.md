China hits back, calculated from China data, not from the US data
================
Mitsuo Shiota
2019-06-18

  - [Summary](#summary)
  - [Libraries](#libraries)
  - [Get retaliation tariff lists in HS 8 digit
    codes](#get-retaliation-tariff-lists-in-hs-8-digit-codes)
  - [Cut 8 to 6 digit codes](#cut-8-to-6-digit-codes)
  - [Get China import from US data in 2017 in HS 6 digit
    codes](#get-china-import-from-us-data-in-2017-in-hs-6-digit-codes)

Updated: 2019-06-18

## Summary

Since [I guessed that Chinese calculated their retaliation values on HS
6 digit codes by ignoring 8 digit codes based on the data from the US
customs](China-hits-back.md), I have looked for the data from China
customs, and found [UN Comtrade](https://comtrade.un.org/) has HS 6
digit code data reported by China.

## Libraries

As usual, I load tidyverse package.

I prepare some functions.

## Get retaliation tariff lists in HS 8 digit codes

I download Excel and PDF files from the Chinese government sites. I
scratch HS 8 digit codes from those files. And I get the tariff list of
each iteration or tranche like “3b”, “34b”, “16b” and “60b”.

The difference from [the original study](China-hits-back.md) in this
part is that I don’t omit 0s in the codes in the right.

## Cut 8 to 6 digit codes

I cut HS codes from 8 to 6 digits.

## Get China import from US data in 2017 in HS 6 digit codes

I get the id’s of both China and the US by basically following [Using
the UN Comtrade data API with
R](https://comtrade.un.org/Data/Doc/api/ex/r) page.

``` r
string <- "http://comtrade.un.org/data/cache/partnerAreas.json"

reporters <- jsonlite::fromJSON(string)

reporters$results %>% 
  filter(text %in% c("China", "USA"))
```

    ##    id  text
    ## 1 156 China
    ## 2 842   USA

I make query to extract China import from the US on HS 6 digit in 2017.

``` r
q_list <- list(
  max=50000,
  type="C",
  freq="A",
  px="HS",
  ps=2017,
  r=156,
  p=842,
  rg=1,
  cc="AG6",
  fmt="json"
)

response <- httr::GET(
  url = "http://comtrade.un.org/api/get",
  query = q_list
)

res <- httr::content(response)

df2017_6 <- res$dataset %>% 
  map_dfr(~ list(hs = .x$cmdCode,
                 value = .x$TradeValue))

total <- df2017_6 %>% 
  summarize(value = sum(value)) %>% 
  as.numeric() / 1000000000 
```

China imports from US in 2017 amount to 154 billion dollars in total. I
have got data by HS 6 digit codes in “df2017\_6”, a data frame of 2
columns of “hs” and “value.”

The results in “3b”, “34b”, “16b” and “60b” are 2.97, 30.12, 11.06 and
51.35 billion dollars respectively. Ratios to the Chinese claims are
0.99, 0.89, 0.69 and 0.86.

These numbers, which are calculated from the data reported by China,
support my guess that Chinese calculated their retaliation values on HS
6 digit codes by ignoring 8 digit codes. However, I can’t be completely
sure, until I get China customs data by HS 8 digit. [IHS Markit’s Global
Trade Atlas](https://www.gtis.com/English/GTIS_GTA.html) has them, but
so far I failed to extract data. Instead I have found that IHS Markit
prohibits direct access via API. Sigh.

EOL
