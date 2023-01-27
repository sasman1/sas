# SAS
## Table of contents
* [Read Data](#read-data)
    * [Direct](#read-data-direct)
    * [Read data from .txt](#read-data-from-txt)
    * [Read data from xlsx](#read-data-from-xlsx)
* [Process Data](#process-data)
    * [Combine tables](#combine-tables)
        * [By data operation](#by-data-operation)
	    * [By proc sql operation](#by-proc-sql-operation)
    * [Join tables](#join-tables)
        * [By data operation](#by-data-operation)
	    * [By proc sql operation](#by-proc-sql-operation)
* [Lineare Regression](#lineare-regression)
	* [Multiple linear regression](#multiple-linear-regression)
___

## Read Data

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

## Process Data

#### Combine tables

##### By data operation
```
data table_2;
	merge table_21 table_22;
	by patient;
run;
```

##### By proc sql operation
```
proc sql;
    create table table_1 as
    select patient, geschlecht
    from table_11
    union 
    select *    
    from table_12
    ;
quit;
```

oder:

```
proc sql;
    create table table_2 as
    select *
    from table_21 join table_22 
    on table_21.patient = table_22.patient
    ;
quit;
```

#### Join tables

##### By data operation
```
data table_1;
	set table_11 table_12;
run;
```

##### By proc sql operation
```
proc sql;
    create table table_2 as
    select table_21.*, table_22.schuhgroesse
    from table_21, table_22
  	where table_21.patient=table_22.patient
    ;
quit;
```
oder:
```
proc sql;
    create table table_2 as
    select *
    from table_21 join table_22 
    on table_21.patient = table_22.patient
    ;
quit;
```
___

## Lineare Regression
doc. [PROC REG](https://documentation.sas.com/doc/en/statug/15.2/statug_reg_toc.htm)
#### Syntax
```
PROC REG <options>;

    <label:> MODEL dependents = <regressors> </ options>;
    
    BY variables;
    
    OUTPUT <OUT=SAS-data-set> <keyword=names> <…keyword=names>;

    PAINT <condition |ALLOBS> </ options> |<STATUS |UNDO>;

    PLOT <yvariable*xvariable> <=symbol> <…yvariable*xvariable> <=symbol> </ options>;

    PRINT <options> <ANOVA> <MODELDATA>;

    <label:> TEST equation, <, …, equation> </ option>;
run;
```

#### The Model
```
MODEL dependents = <regressors> </ options>;
```
Useful options for the model
```
clb : 95% confidence interval
```


### Multiple linear regression
```
proc reg data=DATANAME;
	model y = x1 x2;
	title "title of the reg";
run;
```
