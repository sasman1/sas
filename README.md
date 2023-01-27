# SAS
## Table of contents
* Read Data
    * [Direct](#read-data-direct)
    * [Read data from .txt](#read-data-from-txt)

___

#### Read data direct
```
data dataset;
	input group value gender $10.;
	datalines;
	1 16 female
	1 21 male
	1 32 female
	1 12 female
	; 
run;
```
`$10.` change length of the string to `10`
equivalent to
```
data dataset;
	input group value gender $10. @@;
	datalines;   
	1 16 female 1 21 male 1 32 female 1 12 female
	; 
run;
```

#### Read data from txt
```
proc import file="/home/USERID/sasuser.v94/data.txt"
    out=DATANAME
    dbms=tab
    replace;
run;
```
