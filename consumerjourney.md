---
title: "Consumer Journey"
author: "Dr. Prashant Mishra"
date: "4/22/2018"
output:
      html_document:
        keep_md: true
---




```r
library(tidyverse)
```

```
## ── Attaching packages ─────────────────────────────────────────────────────────────────────────────── tidyverse 1.2.1 ──
```

```
## ✔ ggplot2 2.2.1     ✔ purrr   0.2.4
## ✔ tibble  1.4.1     ✔ dplyr   0.7.4
## ✔ tidyr   0.7.2     ✔ stringr 1.2.0
## ✔ readr   1.1.1     ✔ forcats 0.3.0
```

```
## ── Conflicts ────────────────────────────────────────────────────────────────────────────────── tidyverse_conflicts() ──
## ✖ dplyr::filter() masks stats::filter()
## ✖ dplyr::lag()    masks stats::lag()
```

```r
library(reshape2)
```

```
## 
## Attaching package: 'reshape2'
```

```
## The following object is masked from 'package:tidyr':
## 
##     smiths
```

```r
library(ggthemes)
```

```
## Warning: package 'ggthemes' was built under R version 3.4.4
```

```r
library(ggrepel)
library(RColorBrewer)
library(ChannelAttribution)
library(markovchain)
```

```
## Package:  markovchain
## Version:  0.6.9.8-1
## Date:     2017-08-15
## BugReport: http://github.com/spedygiorgio/markovchain/issues
```

```r
library(visNetwork)
library(expm)
```

```
## Loading required package: Matrix
```

```
## 
## Attaching package: 'Matrix'
```

```
## The following object is masked from 'package:tidyr':
## 
##     expand
```

```
## 
## Attaching package: 'expm'
```

```
## The following object is masked from 'package:Matrix':
## 
##     expm
```

```r
library(stringr)
library(readr)
library(purrrlyr)
library()
```



```r
CustInteractEnqFull <- read.table("journey data--Customer Transactions and Interactions/d2_cust_intrctn_enq.csv",header = TRUE,sep="\t",stringsAsFactors = TRUE,colClasses=c("rec_creat_dttm"="POSIXct","intrctn_end_dttm"="POSIXct","intrctn_begin_dttm"="POSIXct","prev_intrctn_dttm"="POSIXct","intrctn_dttm"="POSIXct"))
```



```r
head(CustInteractEnqFull)
```

```
##   intrctn_enq_id  intrctn_id        intrctn_dttm intrctn_ln_item_seq_num
## 1    1.98802e+17 1.98802e+17 1988-02-04 21:39:00                       1
## 2    1.98802e+17 1.98802e+17 1988-02-04 21:42:42                       1
## 3    1.98802e+17 1.98802e+17 1988-02-04 21:43:40                       1
## 4    1.98802e+17 1.98802e+17 1988-02-04 21:47:54                       1
## 5    1.98802e+17 1.98802e+17 1988-02-04 21:50:06                       1
## 6    1.98802e+17 1.98802e+17 1988-02-04 21:57:08                       1
##   prev_intrctn_id   prev_intrctn_dttm promo_ofr_id promo_ofr_ref_int_id
## 1     1.98802e+17 1988-02-04 21:23:00 ID1618150004           1618150004
## 2     1.98802e+17 1988-02-04 21:19:00 ID1618150003           1618150003
## 3     1.98802e+17 1988-02-04 21:11:00 ID1618150002           1618150002
## 4     1.98802e+17 1988-02-04 21:31:00 ID1618150005           1618150005
## 5     1.98802e+17 1988-02-04 21:41:00 ID1618150002           1618150002
## 6     1.98802e+17 1988-02-04 22:01:00 ID1618150002           1618150002
##   promo_initv_id promo_initv_ref_int_id  intrctn_type intrctn_dirctn
## 1    ID914920004              914920004 near_to_store        INBOUND
## 2    ID914920003              914920003 near_to_store        INBOUND
## 3    ID914920002              914920002       checkin        INBOUND
## 4    ID914920005              914920005       checkin        INBOUND
## 5    ID914920002              914920002 near_to_store        INBOUND
## 6    ID914920002              914920002       checkin        INBOUND
##   intrctn_ctgry intrctn_sub_ctgry intrctn_ctgry_id intrctn_reason
## 1       ENQUIRY            ACCESS     INTCATID_001      AWARENESS
## 2       ENQUIRY            ACCESS     INTCATID_001      AWARENESS
## 3       ENQUIRY            ACCESS     INTCATID_001       RESEARCH
## 4       ENQUIRY            ACCESS     INTCATID_001       RESEARCH
## 5       ENQUIRY            ACCESS     INTCATID_001      AWARENESS
## 6       ENQUIRY            ACCESS     INTCATID_001       RESEARCH
##    intrctn_begin_dttm    intrctn_end_dttm   terminal_nm act_type_id
## 1 1988-02-04 21:39:00 1988-02-04 21:39:00 COUNTER_NO_11    12090001
## 2 1988-02-04 21:42:42 1988-02-04 21:42:42 COUNTER_NO_12    12090001
## 3 1988-02-04 21:43:40 1988-02-04 21:43:40 COUNTER_NO_14    12090001
## 4 1988-02-04 21:47:54 1988-02-04 21:47:54  COUNTER_NO_4    12090001
## 5 1988-02-04 21:50:06 1988-02-04 21:50:06  COUNTER_NO_1    12090001
## 6 1988-02-04 21:57:08 1988-02-04 21:57:08 COUNTER_NO_10    12090001
##   act_type_cd    act_type      act_id emp_id  emp_nm chnl_id  chnl_cd
## 1  CD12090001 INTERACTION 1.98802e+16  50004 CD50004  380013 CD380013
## 2  CD12090001 INTERACTION 1.98802e+16  50003 CD50003  380013 CD380013
## 3  CD12090001 INTERACTION 1.98802e+16  50003 CD50003  380013 CD380013
## 4  CD12090001 INTERACTION 1.98802e+16  50004 CD50004  380013 CD380013
## 5  CD12090001 INTERACTION 1.98802e+16  50004 CD50004  380013 CD380013
## 6  CD12090001 INTERACTION 1.98802e+16  50004 CD50004  380013 CD380013
##       chnl_nm tch_pnt_id tch_pnt_cd tch_pnt_nm    cust_id cust_ref_int_id
## 1 Geo-Spatial     200004   CD200004   In-Store CUST100003          100003
## 2 Geo-Spatial     200004   CD200004   In-Store CUST100002          100002
## 3 Geo-Spatial     200004   CD200004   In-Store CUST100001          100001
## 4 Geo-Spatial     200004   CD200004   In-Store CUST100004          100004
## 5 Geo-Spatial     200004   CD200004   In-Store CUST100005          100005
## 6 Geo-Spatial     200004   CD200004   In-Store CUST100007          100007
##            cust_nm bsn_unit_id bsn_unit_cd bsn_unit_nm locn_id    locn_cd
## 1     Rowan Morris      100031  BSN_100031   AREA00031  100003 LCN_100003
## 2    Zane Harrison      100004  BSN_100004   AREA00004  100010 LCN_100010
## 3 Victoria Santana      100043  BSN_100043   AREA00043  100011 LCN_100011
## 4    Jakob Nichols      100023  BSN_100023   AREA00023  100003 LCN_100003
## 5 Selena Whitehead      100039  BSN_100039   AREA00039  100010 LCN_100010
## 6     Maurice Moon      100020  BSN_100020   AREA00020  100011 LCN_100011
##        locn_nm   item_id item_ref_int_id item_nm mrhrc_hier_grp_id
## 1 N_LCN_A00003 CD5130001         5130001     NAN            300001
## 2 N_LCN_A00010 CD5130001         5130001     NAN            300001
## 3 N_LCN_A00011 CD5130001         5130001     NAN            300001
## 4 N_LCN_A00003 CD5130001         5130001     NAN            300001
## 5 N_LCN_A00010 CD5130001         5130001     NAN            300001
## 6 N_LCN_A00011 CD5130001         5130001     NAN            300001
##   mrhrc_hier_grp_nm st_id st_cd ofr_intrctn_type         intrctn_event
## 1  MRHRC L3 Group 1    36     S          SUSPECT InStore_NearToInStore
## 2  MRHRC L3 Group 1    36     S          SUSPECT InStore_NearToInStore
## 3  MRHRC L3 Group 1    36     S          SUSPECT       InStore_CheckIn
## 4  MRHRC L3 Group 1    36     S          SUSPECT       InStore_CheckIn
## 5  MRHRC L3 Group 1    36     S          SUSPECT InStore_NearToInStore
## 6  MRHRC L3 Group 1    36     S          SUSPECT       InStore_CheckIn
##   item_intrctn_type               intrctn_desc batch_run_id job_run_id
## 1         AWARENESS Probable customer activity           NA         NA
## 2         AWARENESS Probable customer activity           NA         NA
## 3          RESEARCH         Walked in to store           NA         NA
## 4          RESEARCH         Walked in to store           NA         NA
## 5         AWARENESS Probable customer activity           NA         NA
## 6          RESEARCH         Walked in to store           NA         NA
##   src_sys_id src_sys_nm tenant_id rec_creat_by      rec_creat_dttm
## 1       SRC1         NA    100001           NA 1988-02-04 21:39:00
## 2       SRC1         NA    100001           NA 1988-02-04 21:42:42
## 3       SRC1         NA    100001           NA 1988-02-04 21:43:40
## 4       SRC1         NA    100001           NA 1988-02-04 21:47:54
## 5       SRC1         NA    100001           NA 1988-02-04 21:50:06
## 6       SRC1         NA    100001           NA 1988-02-04 21:57:08
```

```r
str(CustInteractEnqFull)
```

```
## 'data.frame':	5343 obs. of  58 variables:
##  $ intrctn_enq_id         : num  1.99e+17 1.99e+17 1.99e+17 1.99e+17 1.99e+17 ...
##  $ intrctn_id             : num  1.99e+17 1.99e+17 1.99e+17 1.99e+17 1.99e+17 ...
##  $ intrctn_dttm           : POSIXct, format: "1988-02-04 21:39:00" "1988-02-04 21:42:42" ...
##  $ intrctn_ln_item_seq_num: int  1 1 1 1 1 1 1 1 1 2 ...
##  $ prev_intrctn_id        : num  1.99e+17 1.99e+17 1.99e+17 1.99e+17 1.99e+17 ...
##  $ prev_intrctn_dttm      : POSIXct, format: "1988-02-04 21:23:00" "1988-02-04 21:19:00" ...
##  $ promo_ofr_id           : Factor w/ 6 levels "ID1618150001",..: 4 3 2 5 2 2 5 5 4 5 ...
##  $ promo_ofr_ref_int_id   : int  1618150004 1618150003 1618150002 1618150005 1618150002 1618150002 1618150005 1618150005 1618150004 1618150005 ...
##  $ promo_initv_id         : Factor w/ 6 levels "ID914920001",..: 4 3 2 5 2 2 5 5 4 5 ...
##  $ promo_initv_ref_int_id : int  914920004 914920003 914920002 914920005 914920002 914920002 914920005 914920005 914920004 914920005 ...
##  $ intrctn_type           : Factor w/ 34 levels "add_items_into_cart",..: 21 21 5 5 21 5 5 5 5 15 ...
##  $ intrctn_dirctn         : Factor w/ 1 level "INBOUND": 1 1 1 1 1 1 1 1 1 1 ...
##  $ intrctn_ctgry          : Factor w/ 1 level "ENQUIRY": 1 1 1 1 1 1 1 1 1 1 ...
##  $ intrctn_sub_ctgry      : Factor w/ 10 levels "ACCESS","ACTION",..: 1 1 1 1 1 1 1 1 1 1 ...
##  $ intrctn_ctgry_id       : Factor w/ 10 levels "INTCATID_001",..: 1 1 1 1 1 1 1 1 1 1 ...
##  $ intrctn_reason         : Factor w/ 4 levels "AWARENESS","ENGAGE",..: 1 1 4 4 1 4 4 4 4 4 ...
##  $ intrctn_begin_dttm     : POSIXct, format: "1988-02-04 21:39:00" "1988-02-04 21:42:42" ...
##  $ intrctn_end_dttm       : POSIXct, format: "1988-02-04 21:39:00" "1988-02-04 21:42:42" ...
##  $ terminal_nm            : Factor w/ 14 levels "COUNTER_NO_1",..: 3 4 6 9 1 2 13 7 3 4 ...
##  $ act_type_id            : int  12090001 12090001 12090001 12090001 12090001 12090001 12090001 12090001 12090001 12090001 ...
##  $ act_type_cd            : Factor w/ 1 level "CD12090001": 1 1 1 1 1 1 1 1 1 1 ...
##  $ act_type               : Factor w/ 1 level "INTERACTION": 1 1 1 1 1 1 1 1 1 1 ...
##  $ act_id                 : num  1.99e+16 1.99e+16 1.99e+16 1.99e+16 1.99e+16 ...
##  $ emp_id                 : int  50004 50003 50003 50004 50004 50004 50004 50002 50002 50003 ...
##  $ emp_nm                 : Factor w/ 4 levels "CD50001","CD50002",..: 4 3 3 4 4 4 4 2 2 3 ...
##  $ chnl_id                : int  380013 380013 380013 380013 380013 380013 380013 380013 380013 380013 ...
##  $ chnl_cd                : Factor w/ 7 levels "CD380002","CD380003",..: 7 7 7 7 7 7 7 7 7 7 ...
##  $ chnl_nm                : Factor w/ 7 levels "Geo-Spatial",..: 1 1 1 1 1 1 1 1 1 1 ...
##  $ tch_pnt_id             : int  200004 200004 200004 200004 200004 200004 200004 200004 200004 200004 ...
##  $ tch_pnt_cd             : Factor w/ 4 levels "CD200001","CD200002",..: 4 4 4 4 4 4 4 4 4 4 ...
##  $ tch_pnt_nm             : Factor w/ 4 levels "App","In-Store",..: 2 2 2 2 2 2 2 2 2 2 ...
##  $ cust_id                : Factor w/ 947 levels "CUST100001","CUST100002",..: 3 2 1 4 5 7 6 8 11 2 ...
##  $ cust_ref_int_id        : int  100003 100002 100001 100004 100005 100007 100006 100008 100011 100002 ...
##  $ cust_nm                : Factor w/ 919 levels "Aaliyah Copeland",..: 796 913 900 429 827 680 227 858 616 913 ...
##  $ bsn_unit_id            : int  100031 100004 100043 100023 100039 100020 100007 100004 100034 100026 ...
##  $ bsn_unit_cd            : Factor w/ 98 levels "BSN_100001","BSN_100002",..: 31 4 43 23 39 20 7 4 34 26 ...
##  $ bsn_unit_nm            : Factor w/ 98 levels "AREA00001","AREA00002",..: 31 4 43 23 39 20 7 4 34 26 ...
##  $ locn_id                : int  100003 100010 100011 100003 100010 100011 100001 100009 100005 100005 ...
##  $ locn_cd                : Factor w/ 28 levels "LCN_100001","LCN_100002",..: 3 10 11 3 10 11 1 9 5 5 ...
##  $ locn_nm                : Factor w/ 28 levels "N_LCN_A00001",..: 3 10 11 3 10 11 1 9 5 5 ...
##  $ item_id                : Factor w/ 444 levels "CD5130001","CD5130002",..: 1 1 1 1 1 1 1 1 1 1 ...
##  $ item_ref_int_id        : int  5130001 5130001 5130001 5130001 5130001 5130001 5130001 5130001 5130001 5130001 ...
##  $ item_nm                : Factor w/ 212 levels "1 Ton A/C","1.2 Ton A/C",..: 142 142 142 142 142 142 142 142 142 142 ...
##  $ mrhrc_hier_grp_id      : int  300001 300001 300001 300001 300001 300001 300001 300001 300001 300001 ...
##  $ mrhrc_hier_grp_nm      : Factor w/ 3 levels "MRHRC L2 Group 3",..: 2 2 2 2 2 2 2 2 2 2 ...
##  $ st_id                  : int  36 36 36 36 36 36 36 36 36 36 ...
##  $ st_cd                  : Factor w/ 1 level "S": 1 1 1 1 1 1 1 1 1 1 ...
##  $ ofr_intrctn_type       : Factor w/ 6 levels "CUSTOMER","EVANGELIST",..: 6 6 6 6 6 6 6 6 6 6 ...
##  $ intrctn_event          : Factor w/ 59 levels "InStore_AddItemsInCart",..: 11 11 4 4 11 4 4 4 4 7 ...
##  $ item_intrctn_type      : Factor w/ 4 levels "AWARENESS","ENGAGE",..: 1 1 4 4 1 4 4 4 4 4 ...
##  $ intrctn_desc           : Factor w/ 26 levels "Benefits of loyalty program",..: 22 22 26 26 22 26 26 26 26 22 ...
##  $ batch_run_id           : logi  NA NA NA NA NA NA ...
##  $ job_run_id             : logi  NA NA NA NA NA NA ...
##  $ src_sys_id             : Factor w/ 1 level "SRC1": 1 1 1 1 1 1 1 1 1 1 ...
##  $ src_sys_nm             : logi  NA NA NA NA NA NA ...
##  $ tenant_id              : int  100001 100001 100001 100001 100001 100001 100001 100001 100001 100001 ...
##  $ rec_creat_by           : logi  NA NA NA NA NA NA ...
##  $ rec_creat_dttm         : POSIXct, format: "1988-02-04 21:39:00" "1988-02-04 21:42:42" ...
```


```r
summary(CustInteractEnqFull)
```

```
##  intrctn_enq_id        intrctn_id         intrctn_dttm                
##  Min.   :1.988e+17   Min.   :1.988e+17   Min.   :1988-02-04 00:05:54  
##  1st Qu.:1.991e+17   1st Qu.:1.991e+17   1st Qu.:1991-03-07 05:50:00  
##  Median :1.994e+17   Median :1.994e+17   Median :1994-04-10 16:16:50  
##  Mean   :1.993e+17   Mean   :1.993e+17   Mean   :1993-08-09 14:35:39  
##  3rd Qu.:1.994e+17   3rd Qu.:1.994e+17   3rd Qu.:1994-05-16 05:09:11  
##  Max.   :2.000e+17   Max.   :2.000e+17   Max.   :2000-06-19 06:12:30  
##                                                                       
##  intrctn_ln_item_seq_num prev_intrctn_id     prev_intrctn_dttm            
##  Min.   : 1.000          Min.   :1.988e+17   Min.   :1988-02-04 00:05:54  
##  1st Qu.: 1.000          1st Qu.:1.991e+17   1st Qu.:1991-03-07 05:56:50  
##  Median : 3.000          Median :1.994e+17   Median :1994-04-10 16:27:00  
##  Mean   : 2.929          Mean   :1.993e+17   Mean   :1993-08-09 09:02:26  
##  3rd Qu.: 4.000          3rd Qu.:1.994e+17   3rd Qu.:1994-05-15 04:08:20  
##  Max.   :10.000          Max.   :2.000e+17   Max.   :2000-06-19 04:49:26  
##                                                                           
##        promo_ofr_id promo_ofr_ref_int_id     promo_initv_id
##  ID1618150001:850   Min.   :1.618e+09    ID914920001:850   
##  ID1618150002:878   1st Qu.:1.618e+09    ID914920002:878   
##  ID1618150003:936   Median :1.618e+09    ID914920003:936   
##  ID1618150004:881   Mean   :1.618e+09    ID914920004:881   
##  ID1618150005:905   3rd Qu.:1.618e+09    ID914920005:905   
##  ID1618150006:893   Max.   :1.618e+09    ID914920006:893   
##                                                            
##  promo_initv_ref_int_id              intrctn_type  intrctn_dirctn
##  Min.   :914920001      cart_abondoned     : 425   INBOUND:5343  
##  1st Qu.:914920002      cart_checkout      : 356                 
##  Median :914920004      add_items_into_cart: 349                 
##  Mean   :914920004      login              : 326                 
##  3rd Qu.:914920005      product_enquiry    : 317                 
##  Max.   :914920006      checkin            : 284                 
##                         (Other)            :3286                 
##  intrctn_ctgry  intrctn_sub_ctgry     intrctn_ctgry_id   intrctn_reason
##  ENQUIRY:5343   ACCESS  :1110     INTCATID_001:1110    AWARENESS:1264  
##                 SELECT  :1005     INTCATID_008:1005    ENGAGE   : 987  
##                 ENQUIRY : 835     INTCATID_003: 835    PURCHASE :1858  
##                 OPINION : 748     INTCATID_002: 748    RESEARCH :1234  
##                 TRANSACT: 518     INTCATID_005: 518                    
##                 EXIT    : 425     INTCATID_010: 425                    
##                 (Other) : 702     (Other)     : 702                    
##  intrctn_begin_dttm            intrctn_end_dttm             
##  Min.   :1988-02-04 00:05:54   Min.   :1988-02-04 00:05:54  
##  1st Qu.:1991-03-07 05:50:00   1st Qu.:1991-03-07 05:51:02  
##  Median :1994-04-10 16:16:50   Median :1994-04-10 16:16:50  
##  Mean   :1993-08-09 14:35:39   Mean   :1993-08-09 14:39:51  
##  3rd Qu.:1994-05-16 05:09:11   3rd Qu.:1994-05-16 05:09:11  
##  Max.   :2000-06-19 06:12:30   Max.   :2000-06-19 06:12:30  
##                                                             
##         terminal_nm    act_type_id           act_type_cd  
##  COUNTER_NO_10: 517   Min.   :12090001   CD12090001:5343  
##  COUNTER_NO_6 : 441   1st Qu.:12090001                    
##  COUNTER_NO_14: 429   Median :12090001                    
##  COUNTER_NO_1 : 414   Mean   :12090001                    
##  COUNTER_NO_8 : 412   3rd Qu.:12090001                    
##  COUNTER_NO_4 : 392   Max.   :12090001                    
##  (Other)      :2738                                       
##         act_type        act_id              emp_id          emp_nm    
##  INTERACTION:5343   Min.   :1.988e+16   Min.   :50001   CD50001:4003  
##                     1st Qu.:1.991e+16   1st Qu.:50001   CD50002: 429  
##                     Median :1.994e+16   Median :50001   CD50003: 441  
##                     Mean   :1.993e+16   Mean   :50002   CD50004: 470  
##                     3rd Qu.:1.994e+16   3rd Qu.:50002                 
##                     Max.   :2.000e+16   Max.   :50004                 
##                                                                       
##     chnl_id           chnl_cd                      chnl_nm    
##  Min.   :380002   CD380002:  83   Geo-Spatial          :1327  
##  1st Qu.:380007   CD380003: 108   In-Store Shopping App:1365  
##  Median :380009   CD380004:  72   Instagram            : 519  
##  Mean   :380009   CD380006: 519   Pinterest            :  72  
##  3rd Qu.:380009   CD380007:1869   Retailer Website     :1869  
##  Max.   :380013   CD380009:1365   Twitter              :  83  
##                   CD380013:1327   You Tube             : 108  
##    tch_pnt_id       tch_pnt_cd          tch_pnt_nm         cust_id    
##  Min.   :2e+05   CD200001: 782   App         :1365   CUST300043:  21  
##  1st Qu.:2e+05   CD200002:1869   In-Store    :1327   CUST300138:  19  
##  Median :2e+05   CD200003:1365   Social Media: 782   CUST500131:  19  
##  Mean   :2e+05   CD200004:1327   Website     :1869   CUST300023:  18  
##  3rd Qu.:2e+05                                       CUST300048:  17  
##  Max.   :2e+05                                       CUST500005:  17  
##                                                      (Other)   :5232  
##  cust_ref_int_id              cust_nm      bsn_unit_id    
##  Min.   :100001   Kayla Cain      :  26   Min.   :100001  
##  1st Qu.:200052   Tia Ramos       :  21   1st Qu.:200001  
##  Median :300098   Lilyana Grimes  :  19   Median :200017  
##  Mean   :298959   Martha Murray   :  19   Mean   :175189  
##  3rd Qu.:400145   Micah Carver    :  18   3rd Qu.:200033  
##  Max.   :500199   Braylen Mcfadden:  17   Max.   :200049  
##                   (Other)         :5223                   
##      bsn_unit_cd      bsn_unit_nm      locn_id             locn_cd    
##  BSN_200044: 106   SITE00044: 106   Min.   :100001   WEB_200012: 317  
##  BSN_200042: 104   SITE00042: 104   1st Qu.:200001   WEB_200003: 313  
##  BSN_200002:  97   SITE00002:  97   Median :200005   WEB_200001: 300  
##  BSN_200012:  95   SITE00012:  95   Mean   :175171   WEB_200002: 300  
##  BSN_200003:  93   SITE00003:  93   3rd Qu.:200010   WEB_200010: 299  
##  BSN_200008:  92   SITE00008:  92   Max.   :200014   WEB_200008: 293  
##  (Other)   :4756   (Other)  :4756                    (Other)   :3521  
##          locn_nm          item_id     item_ref_int_id  
##  N_WEB_A00012: 317   CD5130001:3394   Min.   :5130001  
##  N_WEB_A00003: 313   CD5130051:  11   1st Qu.:5130001  
##  N_WEB_A00001: 300   CD5130314:  11   Median :5130001  
##  N_WEB_A00002: 300   CD5130438:  11   Mean   :5130083  
##  N_WEB_A00010: 299   CD5130031:  10   3rd Qu.:5130145  
##  N_WEB_A00008: 293   CD5130034:  10   Max.   :5130446  
##  (Other)     :3521   (Other)  :1896                    
##            item_nm     mrhrc_hier_grp_id        mrhrc_hier_grp_nm
##  NAN           :3394   Min.   :200003    MRHRC L2 Group 3: 782   
##  Godrej Interio:  87   1st Qu.:300001    MRHRC L3 Group 1:1327   
##  Home Town     :  82   Median :300002    MRHRC L3 Group 2:3234   
##  Royal Oak     :  74   Mean   :285366                            
##  Evok          :  68   3rd Qu.:300002                            
##  Black Pottery :  56   Max.   :300002                            
##  (Other)       :1582                                             
##      st_id    st_cd       ofr_intrctn_type               intrctn_event 
##  Min.   :36   S:5343   CUSTOMER   : 705    InStore_CheckIn      : 284  
##  1st Qu.:36            EVANGELIST : 987    Mobile_CartAbondoned : 245  
##  Median :36            LEAD       :1337    Soc_Like             : 196  
##  Mean   :36            OPPORTUNITY: 467    ONL_Login            : 181  
##  3rd Qu.:36            PROSPECT   : 791    ONL_CartCheckOut     : 177  
##  Max.   :36            SUSPECT    :1056    InStore_NearToInStore: 176  
##                                            (Other)              :4084  
##  item_intrctn_type                            intrctn_desc  batch_run_id  
##  AWARENESS:1264    Looking for purchase of item     : 539   Mode:logical  
##  ENGAGE   : 987    Displays interest in product     : 477   NA's:5343     
##  PURCHASE :1671    Displays dis-satisfaction        : 425                 
##  RESEARCH :1421    Looking for offers for purchase  : 417                 
##                    Customer engagement towards brand: 374                 
##                    Displays willingness to buy      : 349                 
##                    (Other)                          :2762                 
##  job_run_id     src_sys_id  src_sys_nm       tenant_id     rec_creat_by  
##  Mode:logical   SRC1:5343   Mode:logical   Min.   :1e+05   Mode:logical  
##  NA's:5343                  NA's:5343      1st Qu.:1e+05   NA's:5343     
##                                            Median :1e+05                 
##                                            Mean   :1e+05                 
##                                            3rd Qu.:1e+05                 
##                                            Max.   :1e+05                 
##                                                                          
##  rec_creat_dttm               
##  Min.   :1988-02-04 00:05:54  
##  1st Qu.:1991-03-07 05:51:02  
##  Median :1994-04-10 16:16:50  
##  Mean   :1993-08-09 14:39:51  
##  3rd Qu.:1994-05-16 05:09:11  
##  Max.   :2000-06-19 06:12:30  
## 
```


```r
library(dplyr)
```


```r
CustInteractEnq <- CustInteractEnqFull %>% mutate(conversion = (CustInteractEnqFull$item_intrctn_type == "PURCHASE"))%>% select(c("cust_id","intrctn_dttm","chnl_nm","conversion"))
```


```r
CustInteractEnq <- CustInteractEnq %>%
  arrange(cust_id,intrctn_dttm) %>%
  group_by(cust_id) %>%
  summarise(path=paste(chnl_nm,collapse = ' > '),
            conv = 1,
            conv_null=0) %>%
  ungroup()
```


```r
mod2 <- markov_model(CustInteractEnq,
                     var_path = 'path',
                     var_conv = 'conv',
                     var_null = 'conv_null',
                     out_more = TRUE)
```


```r
df_hm <- CustInteractEnq %>%
        mutate(channel_name_ft = sub('>.*', '', path),
               channel_name_ft = sub(' ', '', channel_name_ft),
               channel_name_lt = sub('.*>', '', path),
               channel_name_lt = sub(' ', '', channel_name_lt))
```


```r
# first-touch conversions
df_ft <- df_hm %>%
        group_by(channel_name_ft) %>%
        summarise(FtouchConv = sum(conv)) %>%
        ungroup()
```


```r
# last-touch conversions
df_lt <- df_hm %>%
        group_by(channel_name_lt) %>%
        summarise(LtouchConv = sum(conv)) %>%
        ungroup()
```


```r
#h_mod2 <- heuristic_models(CustInteractEnq, var_path = 'path', var_conv = 'conv')
h_mod2 <- merge(df_ft, df_lt, by.x = 'channel_name_ft', by.y = 'channel_name_lt')
```


```r
# merging all models
all_models <- merge(h_mod2, mod2$result, by.x = 'channel_name_ft', by.y = 'channel_name')
colnames(all_models)[c(1, 4)] <- c('channel_name', 'attribModConv')
```


```r
mod2
```

```
## $result
##            channel_name total_conversions
## 1           Geo-Spatial         209.85063
## 2             Instagram         221.46893
## 3      Retailer Website         220.65428
## 4 In-Store Shopping App         165.15537
## 5              You Tube          33.32758
## 6             Pinterest          45.17721
## 7               Twitter          51.36600
## 
## $transition_matrix
##             channel_from            channel_to transition_probability
## 1                (start)           Geo-Spatial            0.304118268
## 2                (start)      Retailer Website            0.298838437
## 3                (start) In-Store Shopping App            0.191129884
## 4                (start)             Instagram            0.079197466
## 5                (start)             Pinterest            0.049630412
## 6                (start)              You Tube            0.027455121
## 7                (start)               Twitter            0.049630412
## 8            Geo-Spatial          (conversion)            0.660550459
## 9            Geo-Spatial             Instagram            0.201834862
## 10           Geo-Spatial              You Tube            0.015290520
## 11           Geo-Spatial               Twitter            0.009174312
## 12           Geo-Spatial      Retailer Website            0.091743119
## 13           Geo-Spatial In-Store Shopping App            0.018348624
## 14           Geo-Spatial             Pinterest            0.003058104
## 15             Instagram           Geo-Spatial            0.065326633
## 16             Instagram          (conversion)            0.653266332
## 17             Instagram      Retailer Website            0.032663317
## 18             Instagram In-Store Shopping App            0.145728643
## 19             Instagram               Twitter            0.042713568
## 20             Instagram             Pinterest            0.035175879
## 21             Instagram              You Tube            0.025125628
## 22      Retailer Website          (conversion)            0.828908555
## 23      Retailer Website             Instagram            0.085545723
## 24      Retailer Website In-Store Shopping App            0.041297935
## 25      Retailer Website           Geo-Spatial            0.020648968
## 26      Retailer Website             Pinterest            0.008849558
## 27      Retailer Website              You Tube            0.005899705
## 28      Retailer Website               Twitter            0.008849558
## 29 In-Store Shopping App           Geo-Spatial            0.018726592
## 30 In-Store Shopping App          (conversion)            0.636704120
## 31 In-Store Shopping App             Instagram            0.202247191
## 32 In-Store Shopping App      Retailer Website            0.044943820
## 33 In-Store Shopping App               Twitter            0.037453184
## 34 In-Store Shopping App             Pinterest            0.026217228
## 35 In-Store Shopping App              You Tube            0.033707865
## 36              You Tube          (conversion)            0.346153846
## 37              You Tube             Instagram            0.442307692
## 38              You Tube In-Store Shopping App            0.153846154
## 39              You Tube               Twitter            0.057692308
## 40             Pinterest             Instagram            0.972222222
## 41             Pinterest      Retailer Website            0.013888889
## 42             Pinterest          (conversion)            0.013888889
## 43               Twitter             Instagram            0.975903614
## 44               Twitter          (conversion)            0.012048193
## 45               Twitter           Geo-Spatial            0.012048193
## 
## $removal_effects
##            channel_name removal_effects
## 1           Geo-Spatial        0.337454
## 2             Instagram        0.356137
## 3      Retailer Website        0.354827
## 4 In-Store Shopping App        0.265581
## 5              You Tube        0.053593
## 6             Pinterest        0.072648
## 7               Twitter        0.082600
```


```r
df_plot_trans <- mod2$transition_matrix

cols <- c("#e7f0fa", "#c9e2f6", "#95cbee", "#0099dc", "#4ab04a", "#ffd73e", "#eec73a",
          "#e29421")
t <- max(df_plot_trans$transition_probability)

ggplot(df_plot_trans, aes(y = channel_from, x = channel_to, fill = transition_probability)) +
        theme_minimal() +
        geom_tile(colour = "white", width = .9, height = .9) +
        scale_fill_gradientn(colours = cols, limits = c(0, t),
                             breaks = seq(0, t, by = t/4),
                             labels = c("0", round(t/4*1, 2), round(t/4*2, 2), round(t/4*3, 2), round(t/4*4, 2)),
                             guide = guide_colourbar(ticks = T, nbin = 50, barheight = .5, label = T, barwidth = 10)) +
        geom_text(aes(label = round(transition_probability, 2)), fontface = "bold", size = 4) +
        theme(legend.position = 'bottom',
              legend.direction = "horizontal",
              panel.grid.major = element_blank(),
              panel.grid.minor = element_blank(),
              plot.title = element_text(size = 20, face = "bold", vjust = 2, color = 'black', lineheight = 0.8),
              axis.title.x = element_text(size = 24, face = "bold"),
              axis.title.y = element_text(size = 24, face = "bold"),
              axis.text.y = element_text(size = 8, face = "bold", color = 'black'),
              axis.text.x = element_text(size = 8, angle = 90, hjust = 0.5, vjust = 0.5, face = "plain")) +
        ggtitle("Transition matrix heatmap")
```

![](consumerjourney_files/figure-html/unnamed-chunk-15-1.png)<!-- -->



```r
# models comparison
all_mod_plot <- melt(all_models, id.vars = 'channel_name', variable.name = 'conv_type')
all_mod_plot$value <- round(all_mod_plot$value)

# slope chart
pal <- colorRampPalette(brewer.pal(10, "Set1"))
```

```
## Warning in brewer.pal(10, "Set1"): n too large, allowed maximum for palette Set1 is 9
## Returning the palette you asked for with that many colors
```

```r
ggplot(all_mod_plot, aes(x = conv_type, y = value, group = channel_name)) +
        theme_solarized(base_size = 18, base_family = "", light = TRUE) +
        scale_color_manual(values = pal(10)) +
        scale_fill_manual(values = pal(10)) +
        geom_line(aes(color = channel_name), size = 2.5, alpha = 0.8) +
        geom_point(aes(color = channel_name), size = 5) +
        geom_label_repel(aes(label = paste0(channel_name, ': ', value), fill = factor(channel_name)),
                         alpha = 0.7,
                         fontface = 'bold', color = 'white', size = 5,
                         box.padding = unit(0.25, 'lines'), point.padding = unit(0.5, 'lines'),
                         max.iter = 100) +
        theme(legend.position = 'none',
              legend.title = element_text(size = 16, color = 'black'),
              legend.text = element_text(size = 16, vjust = 2, color = 'black'),
              plot.title = element_text(size = 20, face = "bold", vjust = 2, color = 'black', lineheight = 0.8),
              axis.title.x = element_text(size = 24, face = "bold"),
              axis.title.y = element_text(size = 16, face = "bold"),
              axis.text.x = element_text(size = 16, face = "bold", color = 'black'),
              axis.text.y = element_blank(),
              axis.ticks.x = element_blank(),
              axis.ticks.y = element_blank(),
              panel.border = element_blank(),
              panel.grid.major = element_line(colour = "grey", linetype = "dotted"),
              panel.grid.minor = element_blank(),
              strip.text = element_text(size = 16, hjust = 0.5, vjust = 0.5, face = "bold", color = 'black'),
              strip.background = element_rect(fill = "#f0b35f")) + 
        labs(x = 'Model', y = 'Conversions') +
        ggtitle('Models comparison') +
        guides(colour = guide_legend(override.aes = list(size = 4)))
```

![](consumerjourney_files/figure-html/unnamed-chunk-16-1.png)<!-- -->


```r
CustInteractEnq <- CustInteractEnqFull %>% mutate(conversion = (CustInteractEnqFull$item_intrctn_type == "PURCHASE"))%>% select(c("cust_id","intrctn_dttm","chnl_nm","conversion"))

CustInteractEnq <- CustInteractEnq %>%
  group_by(cust_id) %>%
  ungroup() %>%
  arrange(cust_id,intrctn_dttm) 

##### splitting paths #####
df_paths <- CustInteractEnq %>%
        group_by(cust_id) %>%
        mutate(path_no = ifelse(is.na(lag(cumsum(conversion))), 0, lag(cumsum(conversion))) + 1) %>%
        ungroup()
```



```r
df_paths_1 <- df_paths %>%
        filter(path_no == 1) %>%
        select(-path_no)
```


```r
##### replace some channels #####
df_path_1_clean <- df_paths_1 %>%
        # removing NAs
        filter(!is.na(chnl_nm))
```



```r
##### one- and multi-channel paths #####
df_path_1_clean <- df_path_1_clean %>%
        group_by(cust_id) %>%
        mutate(uniq_channel_tag = ifelse(length(unique(chnl_nm)) == 1, TRUE, FALSE)) %>%
        ungroup()
 
df_path_1_clean_uniq <- df_path_1_clean %>%
        filter(uniq_channel_tag == TRUE) %>%
        select(-uniq_channel_tag)
 
df_path_1_clean_multi <- df_path_1_clean %>%
        filter(uniq_channel_tag == FALSE) %>%
        select(-uniq_channel_tag)


### experiment ###
# attribution model for all paths
df_all_paths <- df_path_1_clean %>%
        group_by(cust_id) %>%
        summarise(path = paste(chnl_nm, collapse = ' > '),
                  conversion = sum(conversion)) %>%
        ungroup() %>%
        filter(conversion == 1)
 
mod_attrib <- markov_model(df_all_paths,
                           var_path = 'path',
                           var_conv = 'conversion',
                           out_more = TRUE)
```



```r
mod_attrib$removal_effects
```

```
##            channel_name removal_effects
## 1           Geo-Spatial        0.267837
## 2      Retailer Website        0.437924
## 3 In-Store Shopping App        0.336915
## 4             Instagram        0.076729
## 5             Pinterest        0.024197
## 6              You Tube        0.011558
## 7               Twitter        0.020632
```

```r
mod_attrib$result
```

```
##            channel_name total_conversions
## 1           Geo-Spatial         154.89913
## 2      Retailer Website         253.26616
## 3 In-Store Shopping App         194.84926
## 4             Instagram          44.37496
## 5             Pinterest          13.99394
## 6              You Tube           6.68438
## 7               Twitter          11.93218
```

```r
d_all <- data.frame(mod_attrib$result)
```


```r
# attribution model for splitted multi and unique channel paths
df_multi_paths <- df_path_1_clean_multi %>%
        group_by(cust_id) %>%
        summarise(path = paste(chnl_nm, collapse = ' > '),
                  conversion = sum(conversion)) %>%
        ungroup() %>%
        filter(conversion == 1)
```


```r
mod_attrib_alt <- markov_model(df_multi_paths,
                           var_path = 'path',
                           var_conv = 'conversion',
                           out_more = TRUE)
mod_attrib_alt$removal_effects
```

```
##            channel_name removal_effects
## 1             Instagram        0.631126
## 2 In-Store Shopping App        0.595330
## 3           Geo-Spatial        0.368397
## 4      Retailer Website        0.392534
## 5             Pinterest        0.202215
## 6              You Tube        0.096990
## 7               Twitter        0.172415
```

```r
mod_attrib_alt$result
```

```
##            channel_name total_conversions
## 1             Instagram         20.789370
## 2 In-Store Shopping App         19.610245
## 3           Geo-Spatial         12.135044
## 4      Retailer Website         12.930119
## 5             Pinterest          6.660988
## 6              You Tube          3.194863
## 7               Twitter          5.679372
```


```r
# adding unique paths
df_uniq_paths <- df_path_1_clean_uniq %>%
        filter(conversion == 1) %>%
        group_by(chnl_nm) %>%
        summarise(conversions = sum(conversion)) %>%
        ungroup()
 
d_multi <- data.frame(mod_attrib_alt$result)
 
d_split <- full_join(d_multi, df_uniq_paths, by = c('channel_name' = 'chnl_nm')) %>%
        mutate(result = total_conversions + conversions)
 
sum(d_all$total_conversions)
```

```
## [1] 680
```

```r
sum(d_split$result)
```

```
## [1] NA
```

```r
##### Generic Probabilistic Model #####
df_all_paths_compl <- df_path_1_clean %>%
        group_by(cust_id) %>%
        summarise(path = paste(chnl_nm, collapse = ' > '),
                  conversion = sum(conversion)) %>%
        ungroup() %>%
        mutate(null_conversion = ifelse(conversion == 1, 0, 1))
 
mod_attrib_complete <- markov_model(
        df_all_paths_compl,
        var_path = 'path',
        var_conv = 'conversion',
        var_null = 'null_conversion',
        out_more = TRUE
)
 
trans_matrix_prob <- mod_attrib_complete$transition_matrix %>%
        dmap_at(c(1, 2), as.character)

##### viz #####
edges <-
        data.frame(
                from = trans_matrix_prob$channel_from,
                to = trans_matrix_prob$channel_to,
                label = round(trans_matrix_prob$transition_probability, 2),
                font.size = trans_matrix_prob$transition_probability * 100,
                width = trans_matrix_prob$transition_probability * 15,
                shadow = TRUE,
                arrows = "to",
                color = list(color = "#95cbee", highlight = "red")
        )
 
nodes <- data_frame(id = c( c(trans_matrix_prob$channel_from), c(trans_matrix_prob$channel_to) )) %>%
        distinct(id) %>%
        arrange(id) %>%
        mutate(
                label = id,
                color = ifelse(
                        label %in% c('(start)', '(conversion)'),
                        '#4ab04a',
                        ifelse(label == '(null)', '#ce472e', '#ffd73e')
                ),
                shadow = TRUE,
                shape = "box"
        )
 
visNetwork(nodes,
           edges,
           height = "2000px",
           width = "100%",
           main = "Generic Probabilistic model's Transition Matrix") %>%
        visIgraphLayout(randomSeed = 123) %>%
        visNodes(size = 5) %>%
        visOptions(highlightNearest = TRUE) 
```

<!--html_preserve--><div id="htmlwidget-4564e4d2b970747a2049" style="width:100%;height:2000px;" class="visNetwork html-widget"></div>
<script type="application/json" data-for="htmlwidget-4564e4d2b970747a2049">{"x":{"nodes":{"id":["(conversion)","(null)","(start)","Geo-Spatial","In-Store Shopping App","Instagram","Pinterest","Retailer Website","Twitter","You Tube"],"label":["(conversion)","(null)","(start)","Geo-Spatial","In-Store Shopping App","Instagram","Pinterest","Retailer Website","Twitter","You Tube"],"color":["#4ab04a","#ce472e","#4ab04a","#ffd73e","#ffd73e","#ffd73e","#ffd73e","#ffd73e","#ffd73e","#ffd73e"],"shadow":[true,true,true,true,true,true,true,true,true,true],"shape":["box","box","box","box","box","box","box","box","box","box"],"x":[-1,0.821055772239431,0.292690489403757,-0.0655114247552928,-0.380840612691973,0.357777642933924,1,-0.162113667283209,0.289177153143684,0.60849048624836],"y":[0.269999118421544,0.0524352975421589,0.512878420841226,-0.268935350909702,-0.501426278744026,-0.00599395763400967,1,0.814966299367758,-1,-0.780007558702917]},"edges":{"from":["(start)","(start)","(start)","(start)","(start)","(start)","(start)","Geo-Spatial","Geo-Spatial","Geo-Spatial","Geo-Spatial","Geo-Spatial","Geo-Spatial","Retailer Website","Retailer Website","Retailer Website","In-Store Shopping App","In-Store Shopping App","Instagram","Instagram","Instagram","Instagram","Instagram","Instagram","Instagram","Pinterest","Pinterest","Twitter","Twitter","Twitter","You Tube","You Tube","You Tube","You Tube"],"to":["Geo-Spatial","Retailer Website","In-Store Shopping App","Instagram","Pinterest","You Tube","Twitter","(conversion)","(null)","Retailer Website","You Tube","In-Store Shopping App","Twitter","(conversion)","(null)","Instagram","(conversion)","Instagram","(null)","In-Store Shopping App","Twitter","Retailer Website","You Tube","Geo-Spatial","Pinterest","Instagram","(null)","Instagram","(null)","Geo-Spatial","Instagram","(null)","In-Store Shopping App","Twitter"],"label":[0.3,0.3,0.19,0.08,0.05,0.03,0.05,0.53,0.37,0.08,0.01,0.02,0,0.94,0.05,0,1,0,0.63,0.19,0.06,0.05,0.02,0.01,0.04,0.98,0.02,0.97,0.02,0.02,0.61,0.21,0.12,0.06],"font.size":[30.4118268215417,29.8838437170011,19.1129883843717,7.91974656810982,4.96304118268215,2.7455121436114,4.96304118268215,52.7397260273973,36.986301369863,7.53424657534247,0.684931506849315,1.71232876712329,0.342465753424658,94.2857142857143,5.3968253968254,0.317460317460317,99.5652173913043,0.434782608695652,62.7358490566038,18.8679245283019,5.66037735849057,4.71698113207547,2.35849056603774,1.41509433962264,4.24528301886792,98.2142857142857,1.78571428571429,96.7741935483871,1.61290322580645,1.61290322580645,60.6060606060606,21.2121212121212,12.1212121212121,6.06060606060606],"width":[4.56177402323126,4.48257655755016,2.86694825765576,1.18796198521647,0.744456177402323,0.411826821541711,0.744456177402323,7.91095890410959,5.54794520547945,1.13013698630137,0.102739726027397,0.256849315068493,0.0513698630136986,14.1428571428571,0.80952380952381,0.0476190476190476,14.9347826086957,0.0652173913043478,9.41037735849057,2.83018867924528,0.849056603773585,0.707547169811321,0.35377358490566,0.212264150943396,0.636792452830189,14.7321428571429,0.267857142857143,14.5161290322581,0.241935483870968,0.241935483870968,9.09090909090909,3.18181818181818,1.81818181818182,0.909090909090909],"shadow":[true,true,true,true,true,true,true,true,true,true,true,true,true,true,true,true,true,true,true,true,true,true,true,true,true,true,true,true,true,true,true,true,true,true],"arrows":["to","to","to","to","to","to","to","to","to","to","to","to","to","to","to","to","to","to","to","to","to","to","to","to","to","to","to","to","to","to","to","to","to","to"],"color.color":["#95cbee","#95cbee","#95cbee","#95cbee","#95cbee","#95cbee","#95cbee","#95cbee","#95cbee","#95cbee","#95cbee","#95cbee","#95cbee","#95cbee","#95cbee","#95cbee","#95cbee","#95cbee","#95cbee","#95cbee","#95cbee","#95cbee","#95cbee","#95cbee","#95cbee","#95cbee","#95cbee","#95cbee","#95cbee","#95cbee","#95cbee","#95cbee","#95cbee","#95cbee"],"color.highlight":["red","red","red","red","red","red","red","red","red","red","red","red","red","red","red","red","red","red","red","red","red","red","red","red","red","red","red","red","red","red","red","red","red","red"]},"nodesToDataframe":true,"edgesToDataframe":true,"options":{"width":"100%","height":"100%","nodes":{"shape":"dot","physics":false,"size":5},"manipulation":{"enabled":false},"edges":{"smooth":false},"physics":{"stabilization":false}},"groups":null,"width":"100%","height":"2000px","idselection":{"enabled":false,"style":"width: 150px; height: 26px","useLabels":true},"byselection":{"enabled":false,"style":"width: 150px; height: 26px","multiple":false,"hideColor":"rgba(200,200,200,0.5)"},"main":{"text":"Generic Probabilistic model's Transition Matrix","style":"font-family:Georgia, Times New Roman, Times, serif;font-weight:bold;font-size:20px;text-align:center;"},"submain":null,"footer":null,"background":"rgba(0, 0, 0, 0)","igraphlayout":{"type":"square"},"highlight":{"enabled":true,"hoverNearest":false,"degree":1,"algorithm":"all","hideColor":"rgba(200,200,200,0.5)","labelOnly":true},"collapse":{"enabled":false,"fit":false,"resetHighlight":true,"clusterOptions":null}},"evals":[],"jsHooks":[]}</script><!--/html_preserve-->

