Extract HTS codes from USTR site
================
Mitsuo Shiota
2019-11-08

  - [Summary](#summary)
  - [Libraries and functions](#libraries-and-functions)
  - [Extract HTS 8 digit codes from USTR
    lists](#extract-hts-8-digit-codes-from-ustr-lists)
  - [Extract HTS 10 digit codes from exclusion
    lists](#extract-hts-10-digit-codes-from-exclusion-lists)

Updated: 2020-10-07

## Summary

I extract 8 digit HTS (Harmonized Tariff Scedule of the United States)
codes from the USTR tariff lists (pdf format). I also extract 10 digit
HTS codes from granted product exclusion lists. These lists can be found
on [this USTR
page](https://ustr.gov/issue-areas/enforcement/section-301-investigations/tariff-actions).

## Libraries and functions

As usual, I attach tidyverse package.

I make a function to extract hts codes from specified pages in url and
page\_range. I utilize pdftools package to read pdf files, and httr
package to get data from URL.

``` r
extract_hts <- function(hts, url, page_range = NULL) {
  tf <- tempfile(fileext = ".pdf")
  
  httr::GET(url, httr::write_disk(tf))
  
  text <- pdftools::pdf_text(tf)
  
  if (!is.null(page_range)) {
    text <- text[page_range]
  }
  
  text %>% 
    str_extract_all(hts) %>% 
    unlist() %>% 
    str_replace_all("\\.", "")
}
```

## Extract HTS 8 digit codes from USTR lists

pdftools::pdf\_text lets me scan a pdf file by page. stringr package in
tidyverse helps me to extract 8 digits.

``` r
hts8 <- "([0-9]{4})[.]([0-9]{2})[.]([0-9]{2})"

# First tranche 34 billion dollars, 25 percent, effective on July 6, 2018
# https://ustr.gov/issue-areas/enforcement/section-301-investigations/section-301-china/34-billion-trade-action

df_list_34b <- tibble(
  trf_34b = TRUE,
  hs8 = extract_hts(
    hts = hts8,
    url = "https://ustr.gov/sites/default/files/2018-13248.pdf",
    page_range = 5:9
    )
  )

# Second tranche 16 billion dollars, 25 percent, August 23, 2018
# https://ustr.gov/issue-areas/enforcement/section-301-investigations/section-301-china/16-billion-trade-action

df_list_16b <- tibble(
  trf_16b = TRUE,
  hs8 = extract_hts(
    hts = hts8,
    url = "https://ustr.gov/sites/default/files/enforcement/301Investigations/2018-17709.pdf",
    page_range = 4:5
    )
  )

# 200 billion dollars, 10 percent, September 24, 2018
# https://ustr.gov/issue-areas/enforcement/section-301-investigations/section-301-china/200-billion-trade-action

df_list_200b <- tibble(
  trf_200b = TRUE,
  hs8 = extract_hts(
    hts = hts8,
    url = "https://ustr.gov/sites/default/files/enforcement/301Investigations/Tariff%20List%20%2883%20FR%2047974%2C%20as%20amended%20and%20modified%20by%2083%20FR%2049153%29.pdf",
    page_range = 1:194
    )
  )

# 15 percent, September 1, 2019
df_list_300b_a <- tibble(
  trf_300b_a = TRUE,
  hs8 = extract_hts(
    hts = hts8,
    url = "https://ustr.gov/sites/default/files/enforcement/301Investigations/Notice_of_Modification_%28List_4A_and_List_4B%29.pdf",
    page_range = 4:25
    )
  )

# 15 percent, December 15, 2019, postponed
df_list_300b_c <- tibble(
  trf_300b_c = TRUE,
  hs8 = extract_hts(
    hts = hts8,
    url = "https://ustr.gov/sites/default/files/enforcement/301Investigations/Notice_of_Modification_%28List_4A_and_List_4B%29.pdf",
    page_range = 142:145
    )
  )
```

USTR says the numbers of HTS 8 digit items are 818, 279 and 5745 for the
first tranche 34b, the second tranche 16b and the last 200b,
respectively. My caluculation says the numbers are 818, 283 and 5765.
Although there are small differences, I think I can ignore.

## Extract HTS 10 digit codes from exclusion lists

USTR announces exclusions periodically. Exclusions are specified by HTS
10 digit codes.

``` r
hts10 <- "([0-9]{4})[.]([0-9]{2})[.]([0-9]{4})"

exclusion_list <- vector("list", length = 35)

# 34b: granted December 21, 2018
exclusion_list[[1]] <- extract_hts(
  hts = hts10,
  url = "https://ustr.gov/sites/default/files/enforcement/301Investigations/2018-28277.pdf",
  page_range = 3:5
  )

# granted March 25, 2019
exclusion_list[[2]] <- extract_hts(
  hts = hts10,
  url = "https://ustr.gov/sites/default/files/enforcement/301Investigations/84_FR_11152.pdf",
  page_range = 3:6
  )

# granted April 18, 2019
exclusion_list[[3]] <- extract_hts(
  hts = hts10,
  url = "https://ustr.gov/sites/default/files/enforcement/301Investigations/84_FR_16310.pdf",
  page_range = 3:5
  )

# granted May 14, 2019
exclusion_list[[4]] <- extract_hts(
  hts = hts10,
  url = "https://ustr.gov/sites/default/files/enforcement/301Investigations/84_FR_21389.pdf",
  page_range = 2:3
  )

# granted June 4, 2019
exclusion_list[[5]] <- extract_hts(
  hts = hts10,
  url = "https://ustr.gov/sites/default/files/enforcement/301Investigations/84_FR_25895.pdf",
  page_range = 2:4
  )

# granted July 9, 2019
exclusion_list[[6]] <- extract_hts(
  hts = hts10,
  url = "https://ustr.gov/sites/default/files/enforcement/301Investigations/Notice_of_Product_Exclusions.pdf",
  page_range = 3:5
  )

# granted September 20, 2019
exclusion_list[[7]] <- extract_hts(
  hts = hts10,
  url = "https://ustr.gov/sites/default/files/enforcement/301Investigations/%2434_Billion_Exclusions_Granted_September.pdf",
  page_range = 3:26
  )

# granted October 2, 2019
exclusion_list[[8]] <- extract_hts(
  hts = hts10,
  url = "https://ustr.gov/sites/default/files/enforcement/301Investigations/%2434_Billion_Exclusions_Granted_October_2019.pdf",
  page_range = 3:12
  )

# granted December 17, 2019
exclusion_list[[9]] <- extract_hts(
  hts = hts10,
  url = "https://ustr.gov/sites/default/files/enforcement/301Investigations/%2434_Billion_Notice_of_Product_Exclusion_and_Amendments.pdf",
  page_range = 2:2
  )

# granted February 11, 2020
exclusion_list[[10]] <- extract_hts(
  hts = hts10,
  url = "https://ustr.gov/sites/default/files/enforcement/301Investigations/%2434_Billion_Notice_of_Product_Exclusions_and_Amendments_February_2020.pdf",
  page_range = 2:4
  )

# 16b: granted July 31, 2019
exclusion_list[[11]] <- extract_hts(
  hts = hts10,
  url = "https://ustr.gov/sites/default/files/enforcement/301Investigations/16_Billion_Exclusions_Granted.pdf",
  page_range = 2:4
  )

# 16b: granted September 20, 2019
exclusion_list[[12]] <- extract_hts(
  hts = hts10,
  url = "https://ustr.gov/sites/default/files/enforcement/301Investigations/%2416_Billion_Exclusions_Granted_September.pdf",
  page_range = 3:11
  )

# 16b: granted October 2, 2019
exclusion_list[[13]] <- extract_hts(
  hts = hts10,
  url = "https://ustr.gov/sites/default/files/enforcement/301Investigations/%2416_Billion_Exclusions_Granted_October_2019.pdf",
  page_range = 3:13
  )

# 16b: granted February 25, 2020
exclusion_list[[14]] <- extract_hts(
  hts = hts10,
  url = "https://ustr.gov/sites/default/files/enforcement/301Investigations/%2416_Billion_Exclusions_Granted_February.pdf",
  page_range = 5:5
  )

# 16b: Exclusions Granted July 10, 2020
exclusion_list[[15]] <- extract_hts(
  hts = hts10,
  url = "https://ustr.gov/sites/default/files/enforcement/301Investigations/%2416_Billion_Exclusion_Granted_July_10_2020.pdf",
  page_range = 2:2
  )

# 200b: granted August 2, 2019
exclusion_list[[16]] <- extract_hts(
  hts = hts10,
  url = "https://ustr.gov/sites/default/files/enforcement/301Investigations/200_Billion_Exclusions_Granted.pdf",
  page_range = 6:7
  )

# 200b: granted September 20, 2019
exclusion_list[[17]] <- extract_hts(
  hts = hts10,
  url = "https://ustr.gov/sites/default/files/enforcement/301Investigations/%24200_Billion_Exclusions_Granted_September.pdf",
  page_range = 3:6
  )

# 200b: granted October 28, 2019 
exclusion_list[[18]] <- extract_hts(
  hts = hts10,
  url = "https://ustr.gov/sites/default/files/enforcement/301Investigations/%24200_Billion_Exclusions_Granted_October.pdf",
  page_range = 3:5
  )

# 200b: granted November 7, 2019 
exclusion_list[[19]] <- extract_hts(
  hts = hts10,
  url = "https://ustr.gov/sites/default/files/enforcement/301Investigations/%24200_Billion_Exclusions_Granted_November.pdf",
  page_range = 2:3
  )

# 200b: granted November 29, 2019 
exclusion_list[[20]] <- extract_hts(
  hts = hts10,
  url = "https://ustr.gov/sites/default/files/enforcement/301Investigations/%24200_Billion_Exclusions_Granted-November.pdf",
  page_range = 3:4
  )

# 200b: granted December 17, 2019
exclusion_list[[21]] <- extract_hts(
  hts = hts10,
  url = "https://ustr.gov/sites/default/files/enforcement/301Investigations/%24200_Billion_Exclusions_Granted_December.pdf",
  page_range = 3:4
  )

# 200b: granted December 31, 2019
exclusion_list[[22]] <- extract_hts(
  hts = hts10,
  url = "https://ustr.gov/sites/default/files/enforcement/301Investigations/%24200_Billion_Exclusions_Granted_December.pdf",
  page_range = 6:11
  )

# 200b: granted February 5, 2020
exclusion_list[[23]] <- extract_hts(
  hts = hts10,
  url = "https://ustr.gov/sites/default/files/enforcement/301Investigations/%24200_Billion_Exclusions_Granted_January_31.pdf",
  page_range = 3:12
  )

# 200b: granted February 20, 2020
exclusion_list[[24]] <- extract_hts(
  hts = hts10,
  url = "https://ustr.gov/sites/default/files/enforcement/301Investigations/%24200_Billion_Exclusions_Granted_February.pdf",
  page_range = 2:4
  )

# 200b: granted March 16, 2020
exclusion_list[[25]] <- extract_hts(
  hts = hts10,
  url = "https://ustr.gov/sites/default/files/enforcement/301Investigations/%24200_Billion_Exclusions_Granted_March.pdf",
  page_range = 3:4
  )

# 200b: granted March 26, 2020
exclusion_list[[26]] <- extract_hts(
  hts = hts10,
  url = "https://ustr.gov/sites/default/files/enforcement/301Investigations/%24200_Billion_Exclusions_Granted_March_20_2020.pdf",
  page_range = 3:18
  )

# 200b: granted April 24, 2020
exclusion_list[[27]] <- extract_hts(
  hts = hts10,
  url = "https://ustr.gov/sites/default/files/enforcement/301Investigations/%24200_Billion_Exclusions_Granted_April_2020.pdf",
  page_range = 3:11
  )

# 200b: granted May 8, 2020
exclusion_list[[28]] <- extract_hts(
  hts = hts10,
  url = "https://ustr.gov/sites/default/files/enforcement/301Investigations/%24200_Billion_Exclusions_Granted_May.pdf",
  page_range = 3:16
  )

# 200b: granted May 21, 2020
exclusion_list[[29]] <- extract_hts(
  hts = hts10,
  url = "https://ustr.gov/sites/default/files/enforcement/301Investigations/%24200_Billion_Exclusions_Granted_May_2020.pdf",
  page_range = 2:5
  )

# 200b: Exclusions Granted June 19, 2020
exclusion_list[[30]] <- extract_hts(
  hts = hts10,
  url = "https://ustr.gov/sites/default/files/enforcement/301Investigations/%24200_Billion_Exclusions_Granted_June_2020.pdf",
  page_range = 2:3
  )

# 300b_a: granted March 10, 2020     
exclusion_list[[31]] <- extract_hts(
  hts = hts10,
  url = "https://ustr.gov/sites/default/files/enforcement/301Investigations/%24300_Billion_Exclusions_Granted_March.pdf",
  page_range = 3:3
  )

# 300b_a: granted March 17, 2020
exclusion_list[[32]] <- extract_hts(
  hts = hts10,
  url = "https://ustr.gov/sites/default/files/enforcement/301Investigations/%24300_Billion_Exclusions_Granted_March_2020.pdf",
  page_range = 4:5
  )

# 300b_a: granted March 31, 2020
exclusion_list[[33]] <- extract_hts(
  hts = hts10,
  url = "https://ustr.gov/sites/default/files/enforcement/301Investigations/%24300_Billion_Exclusions_Granted_March_25_2020.pdf",
  page_range = 3:3
  )

# 300b_a: granted May 13, 2020 
exclusion_list[[34]] <- extract_hts(
  hts = hts10,
  url = "https://ustr.gov/sites/default/files/enforcement/301Investigations/%24300_Billion_Exclusions_Granted_May.pdf",
  page_range = 3:3
  )

# 300b_a: granted June 8, 2020 
# no 10 digit codes

# 300b_a: Exclusions Granted July 7, 2020 
# no 10 digit codes

# 300b_a: Exclusions Granted July 20, 2020 
# no 10 digit codes

# 300b_a: Exclusions Granted August 5, 2020
exclusion_list[[35]] <- extract_hts(
  hts = hts10,
  url = "https://ustr.gov/sites/default/files/enforcement/301Investigations/%24300_Billion_Exclusions_Granted_August_2020.pdf",
  page_range = 6:7
  )
```

I save tariff lists of “34b”, “16b”, “200b”, “300b\_a” and “300b\_c”,
and a granted product exclusion list in an rdata file.

``` r
save(df_list_34b, df_list_16b, df_list_200b,
     df_list_300b_a, df_list_300b_c, exclusion_list, 
     file = "data/tariff_list.rdata")
```

EOL
