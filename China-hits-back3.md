China hits back as much as the size of its claim
================
Mitsuo Shiota
2019-06-21

  - [Warning](#warning)
  - [Summary](#summary)
  - [Repeat UN Comtrade excercise](#repeat-un-comtrade-excercise)
  - [Confirm UN Comtrade data match China’s General Administration of
    Customs
    data](#confirm-un-comtrade-data-match-chinas-general-administration-of-customs-data)
  - [Get HS 8 digit codes data from Global Trade
    Atlas](#get-hs-8-digit-codes-data-from-global-trade-atlas)
      - [Prepare csv file for download
        automation](#prepare-csv-file-for-download-automation)
      - [Download automation utilizing UI.Vision
        Kantu](#download-automation-utilizing-ui.vision-kantu)
      - [Batch convert from xls to
        xlsx](#batch-convert-from-xls-to-xlsx)
      - [Read xlsx files](#read-xlsx-files)
  - [Finally, calculate on HS 8 digit
    codes](#finally-calculate-on-hs-8-digit-codes)

Updated: 2019-06-21

## Warning

This study is not reproducible, as I drop the data from IHS Markit’s
Global Trade Atlas, which does not allow me to disseminate their data.

## Summary

I was wrong on [my guess that Chinese calculated their retaliation
values on HS 6 digit codes by ignoring 8 digit
codes](China-hits-back.md). My guess was based on the data from the US
customs. In this study, I use the data from China customs, and have
found that Chinese caluculated on HS 8 digit codes, not on 6 digit
codes. I admit my errors, and confirm Chinese claims. I was naive about
HS (Harmonized Commodity Description Coding System).

## Repeat UN Comtrade excercise

I got the HS 6 digit data reported by China from [UN
Comtrade](https://comtrade.un.org/) in the former study, [“China hits
back, calculated from China data, not from the US
data”](https://github.com/mitsuoxv/us-tariffs-on-china/blob/master/China-hits-back2.md).
First I repeat it here.

I correct text2df function, which had dropped 0s in the right.

``` r
# download to temporary pdf file, and scratch text from pdf
url2text <- function(url) {
  tf <- tempfile(fileext = ".pdf")
  
  httr::GET(url, httr::write_disk(tf))
  
  pdftools::pdf_text(tf)
}

# scratch pattern from text
text2df <- function(text, pattern, tariff) {
  tariff_list <- text %>% 
    str_extract_all(pattern) %>% 
    unlist()
  
  tibble(hs = tariff_list, tariff = tariff)
}
```

    ##    id  text
    ## 1 156 China
    ## 2 842   USA

When I calculate based on HS 6 digit codes, the values in “3b”, “34b”,
“16b” and “60b” are 2.97, 34.81, 15.81 and 65.14 billion dollars
respectively. Ratios to the Chinese claims are 0.99, 1.02, 0.99 and
1.09.

## Confirm UN Comtrade data match China’s General Administration of Customs data

China imports from US in 2017, in total, amount to 154.442 billion
dollars, which matches 153.943 billion dollars (2017, CIF basis)
reported by China’s General Administration of Customs, according to the
US Congressional Research Service report [“What’s the Difference? —
Comparing U.S. and Chinese Trade
Data”](https://crsreports.congress.gov/product/pdf/RS/RS22640) by
Michael F. Martin.

## Get HS 8 digit codes data from Global Trade Atlas

### Prepare csv file for download automation

I make a csv file of HS 2, 4, 6 digit codes from UN Comtrade data

``` r
for_csv <- df2017_6 %>% 
  mutate(
    hs2 = str_sub(hs, start = 1L, end = 2L),
    hs4 = str_sub(hs, start = 1L, end = 4L)
  ) %>% 
  select(hs2, hs4, hs)

for_csv %>% 
  write.csv("output/china_import_hs_2017.csv", fileEncoding = "CP932",
            row.names = FALSE)
```

### Download automation utilizing UI.Vision Kantu

I make UI.Vision Kantu for Chrome to repeat downloading Excel files
(.xls) by using this csv file. As [IHS Markit’s Global Trade
Atlas](https://www.gtis.com/English/GTIS_GTA.html) prohibits direct
access via API, I have chosen UI.Vision Kantu for Chrome, which works
through Chrome. It automates input to Global Trade Atlas web page, and
loops by each row of the csv file.

It takes more than 30 hours. I have got 4300 .xls files.

### Batch convert from xls to xlsx

And I batch convert from xls to xlsx by using convert-xls-xlsx.vbs by
revosftw in [Batch convert XLS to
XLSX](https://superuser.com/questions/524119/batch-convert-xls-to-xlsx).

It takes 20 minutes.

### Read xlsx files

I put 4300 .xlsx files in data/xlsx, and read them by using readxl
package. As I don’t upload them, you can’t reproduce this study.

``` r
files <- dir("data/xlsx", pattern = "\\.xlsx$", full.names = TRUE)

gta <- vector("list", length(files))

for (i in seq_along(files)) {
  gta[[i]] <- read_excel(files[[i]], skip = 4)
}

gta_all <- gta %>% 
  map_dfr(~ list(hs = .x[[1]],
                 value = .x[[4]]))

gta_all <- gta_all %>% 
  drop_na(value)

gta_6 <- gta_all %>% 
  filter(str_length(hs) == 6)

gta_8 <- gta_all %>% 
  filter(str_length(hs) == 8)
```

The caluculated total is 148.094, while Global Trade Atlas says the
total is 148.413 billion dollars. There must be some HS 6 digit codes
which exist in Global Trade Atlas, but not in UN Comtrade. As the
difference is so small, I decide to ignore it.

Rather the problem is the values in Global Trade Atlas are 4 percent
less than the values in UN Comtrade data.

``` r
un_gta_6 <- df2017_6 %>% 
  left_join(gta_6, by = "hs") %>% 
  rename(
    un = value.x,
    gta = value.y
  )
```

Among 4327 items, only 906 items have different values between UN
Comtrade and Global Trade Atlas. This suggests that Global Trade Atlas
data are also CIF based.

27 items, which have values in UN Comtrade, but not in Global Trade
Atlas, have total values of 1.094 billion dollars.

I give up to explain the differences between UN Comtrade and Global
Trade Atlas, which is only 4 percent anyway.

## Finally, calculate on HS 8 digit codes

When I calculate based on HS 8 digit codes, the values in “3b”, “34b”,
“16b” and “60b” are 2.97, 33.82, 14.45 and 52.82 billion dollars
respectively. Ratios to the Chinese claims are 0.99, 0.99, 0.9 and 0.88.
Looks like Chinese calculated their retaliation values on HS 8 digit
codes. I was wrong on [my guess that Chinese calculated their
retaliation values on HS 6 digit codes by ignoring 8 digit
codes](China-hits-back.md)

EOL
