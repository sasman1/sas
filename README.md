# SAS
## Table of contents
* Read Data
    * [Direct](#read-data-direct)
    * [Read data from .txt](#read-data-from-txt)
    * [Read data from xlsx](#read-data-from-xlsx)
* [multiple linear regression](#multiple-linear-regression)
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
`$10.` changes the length of the string to `10`

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

#### Read data from xlsx
```
proc import datafile = '/home/USERID/sasuser.v94/data.xlsx'
	out  =  DATANAME
	dbms =  xlsx
	replace
	;
run;
```
___

#### multiple linear regression
```
proc reg data=DATANAME;
	model y = x1 x2;
	title "title of the reg";
run;

```

