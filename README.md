# SAS
## Table of contents
* [Read Data](#read-data)
    * [Direct](#read-data-direct)
    	* [Categorical Data](#read-direct-categorical-data)
    * [Read data from .txt](#read-data-from-txt)
    * [Read data from xlsx](#read-data-from-xlsx)
    * [Read data from csv](#read-data-from-csv)
* [Conditions](#conditions)
* [Process Data](#process-data)
    * [Data Statement](#data-statement)
    * [Combine tables](#combine-tables)
		* [By data operation](#by-data-operation)
		* [By proc sql operation](#by-proc-sql-operation)
    * [Join tables](#join-tables)
		* [By data operation](#by-data-operation)
		* [By proc sql operation](#by-proc-sql-operation)
    * [Modify dataset](#modify-dataset)
    * [Delete rows from dataset](#delete-rows-from-dataset)
    * [Delete columns from dataset](#delete-columns-from-dataset)
    * [Sort dataset](#sort-dataset)
* [Lineare Regression](#lineare-regression)
	* [Multiple linear regression](#multiple-linear-regression)
		* [Forward selection (Bottom-Up)](#forward-selection-bottom-up)
		* [Category Variables with Interaction](#category-variables-with-interaction)
	* [Category Variables](#lineare-regression-with-a-category-variables)
* [Logistic Regression](#logistic-regression)
	* [Category Variables](#logistic-regression-with-a-category-variables)
* [Plots](#plots)
	* [Simple Plot](#simple-plot)
	* [Scatter Plot](#scatter-plot)
	* [Histgram](#histogram)
* [Metrics](#metrics)
	* [Means](#means)
	* [Pearson correlations](#pearson-correlations)
* [Tests](#tests)
	* [Two-sample t-test](#two-sample-t-test)
	* [Kruskal-Wallis-Test](#kruskal-wallis-test)
	* [Tukey-HSD-Test](#tukey-hsd-test)
* [Tips and Tricks](#tips-and-tricks)
___

## Read Data

#### Read data direct
```sas
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
```sas
data dataset;
input group value gender $10. @@;
datalines;   
1 16 female 1 21 male 1 32 female 1 12 female
; 
run;
```

##### Read direct categorical data
if you want to read categorical data, you may use this syntax
```sas
data dataset;
input methode $ value;
datalines; 
Methode1 15
Methode1 24
Methode1 32
Methode1 43
Methode2 555
Methode2 65
Methode2 671
Methode2 617
Methode3 65
Methode3 6762
Methode3 663
Methode3 626
;  
run; 
```

#### Read data from `txt`
```sas
proc import file="/home/USERID/sasuser.v94/data.txt"
    out=DATANAME
    dbms=tab
    replace;
run;
```

#### Read data from `xlsx`
```sas
proc import datafile = '/home/USERID/sasuser.v94/data.xlsx'
	out  =  DATANAME
	dbms =  xlsx
	replace
	;
run;
```

#### Read data from `csv`
```sas
proc import datafile = '/home/USERID/sasuser.v94/data.csv'
	out  =  DATANAME
	dbms =  csv
	replace;
	delimiter=",";
run;
```
___

## Conditions

```sas
data bsp1_modif_3;
	set bsp1_modif;
	length ergebnis $200.;
	if wert2 lt 100 then ergebnis="Kleiner Wert";
	else if wert2 ge 100 then ergebnis="Gro�er Wert";
	else put "INFO: Please check value of variable wert2 for = " wert2;
run;
```
___

## Process Data

### Data Statement
[doc](https://documentation.sas.com/doc/en/pgmsascdc/9.4_3.5/ds2ref/n1ew9uzutoyamjn1kzz8f194xhes.htm)

e.g. split data with `if`
```sas
data DATA1 DATA2;
	set INPUT_DATA;
	if x lt 10 then output DATA1;
	else if x ge 10 then output DATA2;
	drop COL1 COL2;
run;
```
or u can `delete`
```sas
if x gt 10 then delete;
```


#### Combine tables

##### By data operation
```sas
data table_2;
	merge table_21 table_22;
	by patient;
run;
```

##### By proc sql operation
```sas
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

```sas
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
```sas
data table_1;
	set table_11 table_12;
run;
```

##### By proc sql operation
```sas
proc sql;
    create table table_2 as
    select table_21.*, table_22.schuhgroesse
    from table_21, table_22
  	where table_21.patient=table_22.patient
    ;
quit;
```
oder:
```sas
proc sql;
    create table table_2 as
    select *
    from table_21 join table_22 
    on table_21.patient = table_22.patient
    ;
quit;
```

#### Modify dataset

```sas
data bsp1_modif;
	set bsp1; 
	length wert2 8.0; /* specify length of variable */
	wert2 = wert**2;
run;
```

#### Delete rows from dataset

```sas
data bsp1_modif_1;
	set bsp1;
	if gruppe lt 2 then delete;
run;
```

#### Delete columns from dataset

By selecting the columns that should be deleted:

```sas
data bsp1_modif_2;
	set bsp1;
	drop wert;
run;
```
By selecting the columns that should be kept:

```sas
data bsp1_modif_2;
	set bsp1;
	keep gruppe;
run;
```

#### Sort dataset
```sas
proc sort data=bsp1 out=bsp1_sorted; /* default of out is the dataset from data */
	by /* default is descending */ gruppe;
run;
```
___

## Lineare Regression
doc. [PROC REG](https://documentation.sas.com/doc/en/statug/15.2/statug_reg_toc.htm)
#### Syntax
```sas
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
Usefull options for `proc reg`
```sas
OUTEST=DATASETNAME : Outputs a data set that contains parameter estimates and other model fit summary statistics.

plots=diagnostics(stats=(default AIC)) : plots the AIC
```

#### The Model
```sas
MODEL dependents = <regressors> </ options>;
```
Useful options for the `model`
```sas
clb : 95% confidence interval

SELECTION=name : name can be FORWARD, BACKWARD, STEPWISE, MAXR, MINR, RSQUARE, ADJRSQ, CP, or NONE (use the full model).

SLENTRY=value : specifies the significance level for entry into the model used in the FORWARD and STEPWISE methods. The defaults are 0.50 for FORWARD and 0.15 for STEPWISE.

SLSTAY=value : specifies the significance level for staying in the model for the BACKWARD and STEPWISE methods. The defaults are 0.10 for BACKWARD and 0.15 for STEPWISE.

noint : no intercept

SS3 : Displays Type III sum of squares

solution: show parameter estimates in glm
```

#### The Output
```sas
OUTPUT <OUT=SAS-data-set> <keyword=names> <…keyword=names>;
```
Usefull options for the `output`
```sas
output out=DATASETNAME h=h_hat cookd=D; : Hebelwerte h_hat und Cook-Distanzen D
```

### Multiple linear regression
```sas
proc reg data=DATANAME;
	model y = x1 x2;
	title "title of the reg";
run;
```

#### Forward selection (Bottom-Up)
Plots the AIC to the diagnostics output. 
```sas
proc reg data=DATANAME plots=diagnostics(stats=(default AIC));
	model y = x1 x2 x3 x4 / selection=forward;
run;
```

#### Category Variables with Interaction
e.g.
* kv1 can be `low`, `middle` and `high`
* kv2 can be `a`, `b` and `c`
```sas
proc glm data=DATANAME;
	class kv1 kv2;
	model y = x kv1 kv2 kv1*kv2 / solution ss3;
run;
```


### Lineare Regression with a category variables
with `glm` ([docs](https://documentation.sas.com/doc/en/statug/15.2/statug_glm_toc.htm))
default encoding: reference-cell coding (Dummy-Coding)
e.g.
* kv can be `low`, `middle` and `high`.
* `(ref='high')` : sets the reference level (category) to: `high` 
```sas
proc glm data=DATANAME;
	class kv (ref='high');
	model y = kv / solution ss3;; 
run;
```
with `glmselect`
```sas
proc glmselect data=DATANAME;
	class kv (ref='high');
	model y = kv;; 
run;
```

## Logistic Regression
### Logistic Regression with a category variables
* kv can be `low`, `middle` and `high`.
* `(ref='high')` : sets the reference level (category) to: `high` 
* `param=reference` : sets the coding to: Reference cell coding (Dummy-Coding)
```sas
proc logistic data=DATANAME;
	class kv (ref='high') param=reference;
	model y = kv;
run;
```

___

## Plots

### Simple Plot
`x` vs `y`
```sas
proc gplot data=DATANAME;
	plot y*x;
	title "x vs y";
run;
```

### Scatter Plot
[doc](https://documentation.sas.com/doc/en/pgmsascdc/9.4_3.5/grstatproc/n0yjdd910dh59zn1toodgupaj4v9.htm)

including regression line
```sas
proc sgplot data=DATANAME noautolegend;
	reg y=YVALUES x=XVALUES;
	title "x vs y";
run;
```

### Histogram
with density curve
```sas
proc sgplot data=DATANAME;
	histogram x;
	density x;
run;
```

___

## Metrics
### Means
```sas
proc means data=DATANAME maxdec=2 min max mean median Q1 Q3 std;
	var x;
run;
```

### Pearson correlations
The Pearson correlations of `x` and `y`.
```sas
proc corr data=DATANAME;
	var x y;
run;
```
___

## Tests

### Two-sample t-test
* make a Two-sample t-test with a categorical variable: `methode`
* save the t-value and p-value to `pvalOut`
* H0: μx = μy  vs  H1: μx != μy
```
proc ttest data = DATANAME(where=(methode ne 'Methode3'));
	class methode; 
	var value;
	ods output ttests=pvalOut(where=(method eq 'Gepoolt'));
run;
```

### Kruskal-Wallis-Test
* categorical variable: `methode`
* values of the variable: `value`

example data: [here](#read-direct-categorical-data)
```sas
proc npar1way data=DATANAME wilcoxon;
	class methode;
	var value;
run;
```

### Tukey-HSD-Test
* categorical variable: `methode`
* values of the variable: `value`

example data: [here](#read-direct-categorical-data)
```sas
proc glm data=DATANAME;
	class methode;
	model value = methode;
	means methode / tukey cldiff ;
run;
```

___
## Tips and Tricks
Gegen Streuung der Daten: `log`
```sas
data DATANAME;
	set DATANAME;
	log_x = log(x);
run;
```
