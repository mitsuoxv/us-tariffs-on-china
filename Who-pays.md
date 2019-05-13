Who pays tariffs?
================
Mitsuo Shiota
2019-05-13

  - [Summary](#summary)
  - [Extract HTS 8 digit codes from pdf
    files](#extract-hts-8-digit-codes-from-pdf-files)
  - [Get monthly import data](#get-monthly-import-data)
  - [Transform monthly data into quarterly
    data](#transform-monthly-data-into-quarterly-data)
  - [Look at the price changes from 2018
    Q2](#look-at-the-price-changes-from-2018-q2)

Updated: 2019-05-13

## Summary

On May 11, 2019, Japanese newspaper Nikkei reported that China pays most
of the tariffs by cutting export prices, basically confirming [the US
President’s
tweet](https://twitter.com/realDonaldTrump/status/1126815126584266753).
I was surprised, as [US import price index:
China](https://fred.stlouisfed.org/series/CHNTOT) has not yet declined
so much. So I have decided to check how US import price from China
changed from 2018 Q2, just before the first tranche “34b” became
effective, for each HTS 10 digit code item, and compare the changes by
tariff schedule category.

Result: Although the median price of the first tranche “34b” dropped
nearly 10 percent in 2019 Q1, the others (“16b” and “200b”) dropped only
2 percent, as much as non-tariff levied items (“rest”) dropped. The
value-weighted mean prices, when I trim somewhat arbitraily by removing
more than 6 times price increase items, are almost same across tariff
schedule categories including non-tariff-levied category. I can’t
confirm the Nikkei report.

## Extract HTS 8 digit codes from pdf files

I repeat the HTS 8 digit code scratching, as I have done in [the
original analysis](README.md).

## Get monthly import data

The number of HTS 10 digit code items are 14773.

## Transform monthly data into quarterly data

I change data from monthly to quarterly, and calculate unit price as
value divided by quantity. Next, I index unit price as of 2018 2Q equals
to one, and add tariff schedule category (“34b”, “16b”, “200b”, “rest”)
to each item.

Now, the number of HTS 10 digit code items are 11800.

## Look at the price changes from 2018 Q2

Look at 2018 Q3.

    ## # A tibble: 4 x 2
    ##   category median
    ##   <fct>     <dbl>
    ## 1 34b       1.02 
    ## 2 16b       1.00 
    ## 3 200b      1.000
    ## 4 rest      1.02

![](Who-pays_files/figure-gfm/boxplot_2018_Q3-1.png)<!-- -->

    ## # A tibble: 4 x 2
    ##   category w_mean
    ##   <fct>     <dbl>
    ## 1 34b        1.13
    ## 2 16b        1.16
    ## 3 200b       1.04
    ## 4 rest       1.01

Look at 2018 Q4.

    ## # A tibble: 4 x 2
    ##   category median
    ##   <fct>     <dbl>
    ## 1 34b       0.976
    ## 2 16b       1.04 
    ## 3 200b      0.985
    ## 4 rest      1.01

![](Who-pays_files/figure-gfm/boxplot_2018_Q4-1.png)<!-- -->

    ## # A tibble: 4 x 2
    ##   category w_mean
    ##   <fct>     <dbl>
    ## 1 34b        1.08
    ## 2 16b        1.16
    ## 3 200b       1.04
    ## 4 rest       1.01

Look at 2019 Q1.

    ## # A tibble: 4 x 2
    ##   category median
    ##   <fct>     <dbl>
    ## 1 34b       0.906
    ## 2 16b       0.988
    ## 3 200b      0.977
    ## 4 rest      0.984

![](Who-pays_files/figure-gfm/boxplot_2019_Q1-1.png)<!-- -->

    ## # A tibble: 4 x 2
    ##   category w_mean
    ##   <fct>     <dbl>
    ## 1 34b       0.885
    ## 2 16b       0.920
    ## 3 200b      0.923
    ## 4 rest      0.900

EOL
