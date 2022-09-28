Lab05
================
VY
2022-09-28

``` r
library(dplyr)
library(data.table)
library(ggplot2)
library(tidytext)
library(tidyverse)
library(dtplyr)
library(knitr)
library(forcats)
library(utils)
```

## Read in data

``` r
if (!file.exists("mtsamples.csv"))
  download.file(
    url = "https://raw.githubusercontent.com/USCbiostats/data-science-data/master/00_mtsamples/mtsamples.csv",
    destfile = "mtsamples.csv",
    method   = "libcurl",
    timeout  = 60
    )
mtsamples <- read.csv("mtsamples.csv")
mts <- as_tibble(mtsamples)
```

## What specialties do we have? What are the frequencies of each?

``` r
specialties <- count(mts, medical_specialty)

specialties %>%
    arrange(desc(n)) %>% 
      knitr::kable()
```

| medical_specialty             |    n |
|:------------------------------|-----:|
| Surgery                       | 1103 |
| Consult - History and Phy.    |  516 |
| Cardiovascular / Pulmonary    |  372 |
| Orthopedic                    |  355 |
| Radiology                     |  273 |
| General Medicine              |  259 |
| Gastroenterology              |  230 |
| Neurology                     |  223 |
| SOAP / Chart / Progress Notes |  166 |
| Obstetrics / Gynecology       |  160 |
| Urology                       |  158 |
| Discharge Summary             |  108 |
| ENT - Otolaryngology          |   98 |
| Neurosurgery                  |   94 |
| Hematology - Oncology         |   90 |
| Ophthalmology                 |   83 |
| Nephrology                    |   81 |
| Emergency Room Reports        |   75 |
| Pediatrics - Neonatal         |   70 |
| Pain Management               |   62 |
| Psychiatry / Psychology       |   53 |
| Office Notes                  |   51 |
| Podiatry                      |   47 |
| Dermatology                   |   29 |
| Cosmetic / Plastic Surgery    |   27 |
| Dentistry                     |   27 |
| Letters                       |   23 |
| Physical Medicine - Rehab     |   21 |
| Sleep Medicine                |   20 |
| Endocrinology                 |   19 |
| Bariatrics                    |   18 |
| IME-QME-Work Comp etc.        |   16 |
| Chiropractic                  |   14 |
| Diets and Nutritions          |   10 |
| Rheumatology                  |   10 |
| Speech - Language             |    9 |
| Autopsy                       |    8 |
| Lab Medicine - Pathology      |    8 |
| Allergy / Immunology          |    7 |
| Hospice - Palliative Care     |    6 |

There are 40 medical specialties. The frequency is not evenly
distributed. Surgery is most common with 1103 entries, Hospice -
Palliative Care is the least frequent with 6 entries. Some are related -
ie Consult - History and Physical and Discharge Summary are not
specialty-specific.

``` r
specialties %>% 
  top_n(10) %>%
  ggplot(aes(x=n, y= fct_reorder(medical_specialty, n))) +
  geom_col()
```

    ## Selecting by n

![](Lab06_files/figure-gfm/graph%20specialties-1.png)<!-- --> \##
Tokenize words in transcription and look at top 20
