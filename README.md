# SAS
## Table of contents
* Read Data
    * [Read data from .txt](#read-data-from-txt)

___

#### Read data from txt
```
proc import file="/home/USERID/sasuser.v94/data.txt"
    out=DATANAME
    dbms=tab
    replace;
run;
```
