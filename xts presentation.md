xts : the awesome timeseries class
========================================================
author: Chinmay Patil
date: 14 May 2014
incremental: true
transition : none
css : custom.css

Structure of xts
========================================================

`xts` is an extended `zoo` class

The 3 main components of `xts` objects are
- **coredata** : a matrix
- **index** : a vector of any of `Date`, `POSIXct`, `chron`, `yearmon`, `yearqtr` or `timeDate` clasess
- **xtsAttributes** : Arbitrary attributes that may be assigned and removed from the object without causing issues with the data's display or otherwise.

Let's load up some sample data
========================================================


```r
require(xts)
data(sample_matrix)
class(sample_matrix)
```

```
[1] "matrix"
```

```r
str(sample_matrix)
```

```
 num [1:180, 1:4] 50 50.2 50.4 50.4 50.2 ...
 - attr(*, "dimnames")=List of 2
  ..$ : chr [1:180] "2007-01-02" "2007-01-03" "2007-01-04" "2007-01-05" ...
  ..$ : chr [1:4] "Open" "High" "Low" "Close"
```


sample data
========

```r
head(sample_matrix, 3)
```

```
            Open  High   Low Close
2007-01-02 50.04 50.12 49.95 50.12
2007-01-03 50.23 50.42 50.23 50.40
2007-01-04 50.42 50.42 50.26 50.33
```

```r
tail(sample_matrix, 3)
```

```
            Open  High   Low Close
2007-06-28 47.68 47.70 47.57 47.61
2007-06-29 47.64 47.78 47.62 47.66
2007-06-30 47.67 47.94 47.67 47.77
```



Converting to xts : as.xts function
========================================================
Most common usage.
```
as.xts(x, order.by)
```

**x** : a numeric vector, matrix or a factor.

**order.by** : an index vector with unique entries by which the observations in x are ordered. 



Converting matrix to xts
========================================================


```r
matrix_xts <- as.xts(sample_matrix,dateFormat='Date')

str(matrix_xts)
```

```
An 'xts' object on 2007-01-02/2007-06-30 containing:
  Data: num [1:180, 1:4] 50 50.2 50.4 50.4 50.2 ...
 - attr(*, "dimnames")=List of 2
  ..$ : NULL
  ..$ : chr [1:4] "Open" "High" "Low" "Close"
  Indexed by objects of class: [Date] TZ: UTC
  xts Attributes:  
 NULL
```


Converting data.frame to xts
========================================================

```r
df_xts <- as.xts(as.data.frame(sample_matrix),
       important='very important info!')

str(df_xts)
```

```
An 'xts' object on 2007-01-02/2007-06-30 containing:
  Data: num [1:180, 1:4] 50 50.2 50.4 50.4 50.2 ...
 - attr(*, "dimnames")=List of 2
  ..$ : NULL
  ..$ : chr [1:4] "Open" "High" "Low" "Close"
  Indexed by objects of class: [POSIXct,POSIXt] TZ: 
  xts Attributes:  
List of 1
 $ important: chr "very important info!"
```


Creating xts object from scratch
========================================================
```
xts(x = NULL,
    order.by = index(x),
    frequency = NULL,
    unique = TRUE,
    tzone = Sys.getenv("TZ"),
    ...)
```

```r
xts(1:5, Sys.Date()+1:5)
```

```
           [,1]
2014-05-16    1
2014-05-17    2
2014-05-18    3
2014-05-19    4
2014-05-20    5
```


The best feature of xts : subsetting
========================================================
type: section

Lets create equivalent data.frame for comparison
========================================================

```r
DF <- as.data.frame(sample_matrix)
row.names(DF) <- NULL
DF$index <- as.Date(row.names(sample_matrix))
head(DF)
```

```
   Open  High   Low Close      index
1 50.04 50.12 49.95 50.12 2007-01-02
2 50.23 50.42 50.23 50.40 2007-01-03
3 50.42 50.42 50.26 50.33 2007-01-04
4 50.37 50.37 50.22 50.33 2007-01-05
5 50.24 50.24 50.11 50.18 2007-01-06
6 50.13 50.22 49.99 49.99 2007-01-07
```



E.g. Extract entire month of March 2007 in data.frame
========================================================

```r
res <- DF[DF$index >= as.Date('2007-03-01') &  DF$index <= as.Date('2007-03-31') , ]
head(res, 3)
```

```
    Open  High   Low Close      index
59 50.82 50.82 50.56 50.57 2007-03-01
60 50.61 50.72 50.51 50.62 2007-03-02
61 50.73 50.73 50.41 50.41 2007-03-03
```

```r
tail(res, 3)
```

```
    Open High   Low Close      index
87 48.59 48.7 48.57 48.70 2007-03-29
88 48.75 49.0 48.75 48.94 2007-03-30
89 48.96 49.1 48.96 48.97 2007-03-31
```



E.g. Extract entire month of March 2007
========================================================

```r
res2 <- matrix_xts['2007-03']
head(res2, 3)
```

```
            Open  High   Low Close
2007-03-01 50.82 50.82 50.56 50.57
2007-03-02 50.61 50.72 50.51 50.62
2007-03-03 50.73 50.73 50.41 50.41
```

```r
tail(res2, 3)
```

```
            Open High   Low Close
2007-03-29 48.59 48.7 48.57 48.70
2007-03-30 48.75 49.0 48.75 48.94
2007-03-31 48.96 49.1 48.96 48.97
```


Benchmarking
========================================================
Lets create some sample data

```r
DF <- data.frame(x = rnorm(1e6), index = as.POSIXct('1970-01-01') + 1:1e6)

str(DF)
```

```
'data.frame':	1000000 obs. of  2 variables:
 $ x    : num  -0.328 -0.514 -0.713 1.501 0.362 ...
 $ index: POSIXct, format: "1970-01-01 00:00:01" "1970-01-01 00:00:02" ...
```


=======
And equivalent xts data

```r
XTS <- as.xts(DF$x, order.by=DF$index)
str(XTS)
```

```
An 'xts' object on 1970-01-01 00:00:01/1970-01-12 13:46:40 containing:
  Data: num [1:1000000, 1] -0.328 -0.514 -0.713 1.501 0.362 ...
  Indexed by objects of class: [POSIXct,POSIXt] TZ: 
  Original class: 'double'  
  xts Attributes:  
 NULL
```


Let's benchmark
========

```r
require(microbenchmark)

microbenchmark(dfres <- DF[DF$index >= as.POSIXct('1970-01-10 00:00:00') & DF$index < as.POSIXct('1970-01-11 00:00:00'),],
times = 1)
```

```
Unit: milliseconds
                                                                                                             expr
 dfres <- DF[DF$index >= as.POSIXct("1970-01-10 00:00:00") & DF$index <      as.POSIXct("1970-01-11 00:00:00"), ]
   min    lq median    uq   max neval
 459.2 459.2  459.2 459.2 459.2     1
```


========

```r
microbenchmark(xtsres <- XTS['1970-01-10'],
times = 1)
```

```
Unit: milliseconds
                        expr   min    lq median    uq   max neval
 xtsres <- XTS["1970-01-10"] 9.864 9.864  9.864 9.864 9.864     1
```


Just to make sure data returned is same 
=======

```r
head(dfres, 3)
```

```
             x               index
777600 -0.9099 1970-01-10 00:00:00
777601  0.3418 1970-01-10 00:00:01
777602  0.6148 1970-01-10 00:00:02
```

```r
head(xtsres, 3)
```

```
                       [,1]
1970-01-10 00:00:00 -0.9099
1970-01-10 00:00:01  0.3418
1970-01-10 00:00:02  0.6148
```


Just to make sure data returned is same 
=======

```r
tail(dfres, 3)
```

```
             x               index
863997 -0.1094 1970-01-10 23:59:57
863998  0.7202 1970-01-10 23:59:58
863999  0.1719 1970-01-10 23:59:59
```

```r
tail(xtsres,3)
```

```
                       [,1]
1970-01-10 23:59:57 -0.1094
1970-01-10 23:59:58  0.7202
1970-01-10 23:59:59  0.1719
```



More subsetting
========================================================
E.g. Extract all the data from the beginning through January 7, 2007.

```r
matrix_xts['/2007-01-07']
```

```
            Open  High   Low Close
2007-01-02 50.04 50.12 49.95 50.12
2007-01-03 50.23 50.42 50.23 50.40
2007-01-04 50.42 50.42 50.26 50.33
2007-01-05 50.37 50.37 50.22 50.33
2007-01-06 50.24 50.24 50.11 50.18
2007-01-07 50.13 50.22 49.99 49.99
```


More subsetting
========================================================
E.g. Extract all the data between specific period

```r
matrix_xts['2007-01-07/2007-01-18']
```

```
            Open  High   Low Close
2007-01-07 50.13 50.22 49.99 49.99
2007-01-08 50.04 50.10 49.97 49.99
2007-01-09 49.99 49.99 49.80 49.91
2007-01-10 49.91 50.13 49.91 49.97
2007-01-11 49.89 50.24 49.89 50.24
2007-01-12 50.21 50.36 50.17 50.29
2007-01-13 50.32 50.48 50.32 50.41
2007-01-14 50.46 50.62 50.46 50.60
2007-01-15 50.62 50.69 50.47 50.49
2007-01-16 50.62 50.74 50.57 50.68
2007-01-17 50.74 50.77 50.45 50.49
2007-01-18 50.48 50.61 50.40 50.58
```


first and last functions
========================================================


```r
## first 1 week of the data
first(matrix_xts, '1 week')
```

```
            Open  High   Low Close
2007-01-02 50.04 50.12 49.95 50.12
2007-01-03 50.23 50.42 50.23 50.40
2007-01-04 50.42 50.42 50.26 50.33
2007-01-05 50.37 50.37 50.22 50.33
2007-01-06 50.24 50.24 50.11 50.18
2007-01-07 50.13 50.22 49.99 49.99
```


first and last functions
========================================================


```r
## last 2 week of data
last(matrix_xts, '2 weeks')
```

```
            Open  High   Low Close
2007-06-18 47.43 47.56 47.36 47.36
2007-06-19 47.46 47.73 47.46 47.67
2007-06-20 47.71 47.82 47.67 47.67
2007-06-21 47.71 47.71 47.61 47.63
2007-06-22 47.57 47.59 47.33 47.33
2007-06-23 47.23 47.25 47.09 47.25
2007-06-24 47.24 47.30 47.21 47.23
2007-06-25 47.20 47.43 47.13 47.43
2007-06-26 47.44 47.62 47.44 47.62
2007-06-27 47.62 47.72 47.60 47.63
2007-06-28 47.68 47.70 47.57 47.61
2007-06-29 47.64 47.78 47.62 47.66
2007-06-30 47.67 47.94 47.67 47.77
```


first and last functions
========================================================


```r
## first 3 days of the last week of the data.
first(last(matrix_xts,'1 week'),'3 days')
```

```
            Open  High   Low Close
2007-06-25 47.20 47.43 47.13 47.43
2007-06-26 47.44 47.62 47.44 47.62
2007-06-27 47.62 47.72 47.60 47.63
```






merge : Merging timeseries by matching the indices
==========
type : section


Let's create some sample data (again!)
============

```r
data1 <- xts(1:5, 
    order.by=Sys.Date()+( 1:5) )

names(data1) <- 'x'

data2 <- xts(101:105, 
    order.by=Sys.Date()+(-2:2))

names(data2) <- 'y'
```

...contd
============

```r
data1
```

```
           x
2014-05-16 1
2014-05-17 2
2014-05-18 3
2014-05-19 4
2014-05-20 5
```

```r
data2
```

```
             y
2014-05-13 101
2014-05-14 102
2014-05-15 103
2014-05-16 104
2014-05-17 105
```



lets merge
===========

```r
merge(data1,data2)
```

```
            x   y
2014-05-13 NA 101
2014-05-14 NA 102
2014-05-15 NA 103
2014-05-16  1 104
2014-05-17  2 105
2014-05-18  3  NA
2014-05-19  4  NA
2014-05-20  5  NA
```



cbind works the same as well
============

```r
cbind(data1,data2)
```

```
            x   y
2014-05-13 NA 101
2014-05-14 NA 102
2014-05-15 NA 103
2014-05-16  1 104
2014-05-17  2 105
2014-05-18  3  NA
2014-05-19  4  NA
2014-05-20  5  NA
```



Some interesting functions in xts package
========================================================
type: section

Periodicity
========================================================
The periodicity function provides a quick summary as to the underlying periodicity of most time-series like objects.

```r
periodicity(matrix_xts)
```

```
Daily periodicity from 2007-01-02 to 2007-06-30 
```


Find endpoints by time
========================================================
Common use case with timeseries data is to identify the endpoints with respect to time.


```r
endpoints(matrix_xts,on='months')
```

```
[1]   0  30  58  89 119 150 180
```

```r
endpoints(matrix_xts,on='months', k = 2)
```

```
[1]   0  58 119 180
```


Endpoints continued
========================================================

```r
matrix_xts[endpoints(matrix_xts,on='months')]
```

```
            Open  High   Low Close
2007-01-31 50.07 50.23 50.07 50.23
2007-02-28 50.69 50.77 50.60 50.77
2007-03-31 48.96 49.10 48.96 48.97
2007-04-30 49.14 49.34 49.11 49.34
2007-05-31 47.83 47.84 47.74 47.74
2007-06-30 47.67 47.94 47.67 47.77
```


Change periodicity
========================================================
One of the most ubiquitous type of data in finance is OHLC data (Open-High-Low-Close). Often is is necessary to change the periodicity of this data to something coarser - e.g. take daily data and aggregate to weekly or monthly.

```r
to.period(matrix_xts,'months', name ="")
```

```
           .Open .High  .Low .Close
2007-01-31 50.04 50.77 49.76  50.23
2007-02-28 50.22 51.32 50.19  50.77
2007-03-31 50.82 50.82 48.24  48.97
2007-04-30 48.94 50.34 48.81  49.34
2007-05-31 49.35 49.69 47.52  47.74
2007-06-30 47.74 47.94 47.09  47.77
```


Change periodicity
========================================================
Wrapper `to.monthly` changes teh index to something more appropriate

```r
to.monthly(matrix_xts, name ="")
```

```
         .Open .High  .Low .Close
Jan 2007 50.04 50.77 49.76  50.23
Feb 2007 50.22 51.32 50.19  50.77
Mar 2007 50.82 50.82 48.24  48.97
Apr 2007 48.94 50.34 48.81  49.34
May 2007 49.35 49.69 47.52  47.74
Jun 2007 47.74 47.94 47.09  47.77
```


Periodically apply a function
========================================================

Often it is desirable to be able to calculate a particular statistic, or evaluate a
function, over a set of non-overlapping time periods.

- period.apply
- apply.daily
- apply.monthly
- apply.quarterly
- apply.yearly

period.apply example
========================================================

```r
period.apply(matrix_xts[,4],
    INDEX=endpoints(matrix_xts),FUN=max)
```

```
           Close
2007-01-31 50.68
2007-02-28 51.18
2007-03-31 50.62
2007-04-30 50.33
2007-05-31 49.59
2007-06-30 47.77
```


========================================================
Same results can be achieved using `apply.monthly`

```r
apply.monthly(matrix_xts[,4],FUN=max)
```

```
           Close
2007-01-31 50.68
2007-02-28 51.18
2007-03-31 50.62
2007-04-30 50.33
2007-05-31 49.59
2007-06-30 47.77
```



Thank you
=====================
type : section

Credits go to authors of xts package : 

Jeff Ryan, Joshua Ulrich


My contact details : chinmay.patil@gmail.com 


