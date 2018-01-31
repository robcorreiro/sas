## Comments

```
/* This is a  
   Multi-line Comment */ 
 
* Statement comment; (usually for single lines) 
 
*-----------------------------------------* 
| Statement comments end at a semicolon   | 
*-----------------------------------------*; 
```
 
---
 
# Printing 

<pre>
/* Select specific vars when printing and sums specific */ 
proc print data=orion.sales; 
    var Last_Name First_Name Salary; 
    <b>sum Salary;  * order usually not important </b>
run; 
</pre>

---

# Selecting 


Can only have ONE WHERE statement PER step, last one wins.


The **var** keyword picks what variables are included in the output.

<pre>
proc print data=orion.sales; 
    var Last_Name First_Name Salary; 
    <b>where Salary < 25500;  \* no quotes needed for numeric</b>
run; 

proc print data=orion.sales; 
    var Last_Name First_Name Salary; 
    <b>where Gender='M';  * quotes needed and value is case sensitive</b>
run;
</pre>


## Date Constant
```
'ddmmm<yy>yy'd
```

## Name Literal

Permit special characters in data set names.

```
'foo'n

orionx.'Australia$'n
```

Other WHERE select examples:

<pre>
where Gender = ' ';
where Salary ^= .;
where Salary >= 50000;
where Hire_Date < '01Jan2000'd
`where Country in ('AU', 'US'); <b>OR</b> logic
where Country in ('AU' 'US'); 
where City not in ('London', 'Rome', 'Paris');
where Order_Type in (1,2,3);
where Job_Title ? 'Rep';  * contains
</pre>

<pre>
where Gender eq ' ';
where Salary ne .;
where Salary le 50000;
where Hire_Date lt '01Jan2000'd;
where Job_Title contains 'Rep';
</pre>

### Logical Operators

Can use **and** + **or** for additional filtering in a WHERE statement.

### Special WHERE Operators


```
where salary between 50000 and 100000 
where salary >= 50000 and salary <= 100000
where 50000 <= salary <= 100000
```

```
where same and Gender='F';  * augments previously defined WHERE to add more logic
where also Gender='F';  * same logic
```

```
where EMPLOYEE_ID is missing;
```

% - any number of characters
_ - single character


```
where Name like '%N';
where Name like 'T_m';
where Name like 
```

Determines the key column.
```
proc print data=...;
    where Customer_Age = 21;
    id Customer_ID;
    ...;
run;
```

---

# Sorting

- By default rearranges in place, affecting input data set (dangerous with a WHERE statement, can lose data)
- Can create a new data set using the <b>OUT=</b> option.


```
proc sort data=orion.sales out=work.sales;
    by Salary;
run;
```

- Default is ASCENDING, must explicitly state for descending

```
 by descending Last First;
 by Last descending First;
 by descending Last descending First;
```

## Advanced Sorting

- Can sort by multiple variables and output multiple tables separated by group

```proc sort data=orion.sales 
          out=work.sales;
    by Country descending Salary;
run;

proc print data=work.sales noobs;
    by Country;  * doesn't sort, tells SAS to expect data in this order, displays output in separate tables
run;
```

## Sorting Examples

```
/*proc contents data=orion.employee_payroll;*/
/*run;*/

proc sort data=orion.employee_payroll
		  out=work.sort_sal;
	by Employee_Gender descending Salary;
	where Employee_Term_Date is missing and 
		  Salary > 65000;
run;

/*proc print data=work.sort_sal;*/
/*run;*/

proc print data=work.sort_sal
	sumlabel='Sub-Total' grandtotal_label='Grand Total';
	id Employee_ID;
	var Salary Marital_Status;
	by Employee_Gender;
	sum Salary;	
run;

```

---

# Titles and Footnotes

- Default: The SAS System
- Up to 10 titles
- Up to 10 footnotes

```
title1 'Orion Star Sales Staff';
title2 'Salary Report';

footnote1 'Confidential';

proc print data=...;
    ...
run;

title;  * best practice to clear all previous titles and footnotes
footnote;  * don't want to accidentally put on another report
```

- A new title statement will replace the existing title and clear ALL with a higher number

---

# Labels

- Displays descriptive column headings
- Up to 256 chars including blanks
- PRINT procedure uses labels ONLY when LABEL or SPLIT= is used.

<pre>
proc print data=orion.sales <b>label</b>;
  var ...;
  <b>label Employee_ID='Sales ID'
        Last_Name='Last Name';</b>
run;
</pre>

## Permanent Labels

 - Define the labels in the DATA step to be stored permanently in the descriptor portion of the data set.
 - Can be overridden with the LABEL option in a PROC step.

<pre>
data work.subset1;
    set ...;
    label Job_Title = 'Sales Title'
          Hire_Date = 'Date Hired';
run;          

/* Display within PRINT using the label option * /
proc print data=work.subset1 <b>label</b>;
run;
</pre>


## SPLIT=

- Split words in column headers using labels and a specified character.

<pre>
proc print data=... <b>split='*'</b>;
    var ...
    label Last_Name = 'Last<b>*</b>Name'
          Salary = 'Annual<b>*</b>Salary';
run;
</pre>

---

# Formatting Data

<pre>
<b>FORMAT</b> <em>variable(s) format ...</em>;
</pre>


<pre>
proc print data=...;
    <b>format Salary dollar8. Hire_Data mmddyy10.;</b>
    var ...
run;    
</pre>

## Formats

<pre>
$w. - standard character
w.d - standard numeric
COMMAw.d
DOLLARw.d
COMMAXw.d
EUROXw.d
MMDDYY10.
</pre>

## Permanent Formats

 - Define formats in the DATA step to be stored permanently in the descriptor portion of the data set.
 - Can be overridden with the FORMAT option in a PROC step.

<pre>
data work.subset1;
    set ...;
    Bonus = Salary * 0.10;
    format Salary commax8. Bonux commax8.2;
run;          

/* Display within PRINT using the label option * /
proc print data=work.subset1 <b>label</b>;
run;
</pre>

## Format Examples

<pre>
title 'Contents of Orion.Sales';
proc contents data=orion.sales;
run;

title1 'US Sales Employees';
title2 'Earning Under $26,000';

proc print data=orion.sales label split='*';
	id employee_id;
	where salary < 26000 and country = 'US';
	var first_name last_name job_title salary hire_date; 
	label first_name = 'First*Name'
	      last_name = 'Last*Name'
	      job_title = 'Title'
	      hire_date = 'Date*Hired';
	format salary dollar8. hire_date monyy7.;
run;
title;
</pre>

## User-Defined Formats

<pre>
<b>PROC FORMAT</b>;
    <b>VALUE</b> <em>format-name range1 = 'label'
                          range2 = 'label'</em>
                                  ...;
RUN;
</pre>

- Must begin with '$' for character formats
- Letter or underscore required at beginning and after '$'
- Range can be single value, range of values, or a list
- Labels must be enclosed in quotations


Create a custom format. A new folder gets created in the WORK library named Formats which contains the custom created formats.

<pre>
proc format;
	value <b>$ctrymt</b> 'AU'='Australia'
                         'US'='United States'
                         other='Miscoded';
run;

/* Ranges */
proc format;
    value tiers       0-49999='Tier 1'
                  50000-99999='Tier 2';
run;

/* The < symbol EXCLUDES the endpoint from a range, allowing continuous values * /
proc format;
    value tiers     low -< 20000    = 'Tier 0'  *  keyword representing lowest possible value
                    20000 -< 50000  = 'Tier 1'  * don't include 50,000
                    50000 -< 100000 = 'Tier 2'
                    100000 - high   = 'Tier 3';  * keyword representing highest possible value
run;

proc format;
    value mnthfmt   1,2,3 = 'Qty1'
                        . = 'missing'  * replacing missing numerics
                    other = 'unknown';
run;
</pre>

Then apply it.

<pre>
proc print data=...;
	var ...;
    <b>format Country $ctryfmt.;</b>  * Note the required period 
run;
</pre>

---

# Reading SAS Data Sets

## Subsets

- Create new or update existing variables with an **assignment** statement.
- WHERE statement must go below the **set** statement, the variables don't exist until data is brought in.

```
data work.subset1;  /* OUTPUT */
    set orion.sales;  /* INPUT - read one at a time */
    where Country='AU' and
          Job_Title contains 'Rep' and
          Hire_Date < '01jan2000'd;
    Bonus = Salary * 0.10;  /* assignment */
run;          
```

## Customizing

DROP <b>excludes</b> variables from output data set, others will be kept.

<pre>
data work.subset1;
    set orion.sales;
    <b>drop Employee_ID Gender Country;</b>
run;    
</pre>

KEEP <b>includes</b> variables in the output data set, others will be dropped.

<pre>
data work.subset1;
    set orion.sales;
    <b>keep First_Name Employee_ID Gender Country;</b>
run;    
</pre>

IF tests a condition, to determine where the observation should be processed. Note, this test CANNOT go in a WHERE statement.

<pre>
/* Subsetting IF statement */
data work.auemps;
    set orion.sales;
    where Country = 'AU';
    Bonus = Salary * 0.10;
    <b>if Bonus >= 3000;</b>
run;  

/* More efficient, less observations processed * /
data work.auemps;
    set orion.sales;
    where Country = 'AU' and
        salary * 0.10 >= 3000;
    Bonus = Salary * 0.10;
run;    
</pre>

---

# Reading Data

## Spreadsheets

- Requires <b>SAS/ACCESS Interface to PC Files</b> license
 
 Add access as a library.
 
 <pre>
 <b>LIBNAME</b> <em>libref <engine> "workbook-name" options;</em>
 </pre>
 
 ```
 /* Bit counts of SAS and MS Office are the SAME */
 libname orionx excel "&path\sales.xls";
 
 /* Bit counts of SAS and MS Office are DIFFERENT */
 libname orionx pcfiles path="&path\sales.xls";
 ```

If SAS has a libref assigned to an Excel sheet, the sheet cannot be opened in Excel as RW, only RO.

```
/* Clear out a libref */
libname orionx clear;
```

---

# Types of Data

## Standard Data

### Numeric

- Contains: +, -, decimal points, or E notation

```
58
67.23
-23
1.2E-2
```

## Nonstandard Data

### Numeric

```
(23)
$67.23
5,823
01/12/2010
12May2009
```

---

# Reading Raw Data Files

## List Input

- Default delimiter is a space (blank) ' '
- Fields read left to right
- Standard and nonstandard can be read
- Default length for all variables is 8 bytes

```
data work.subset;
    infile "&path\sales.csv" dlm=',';  /* double quotes used for referencing macro variable */
    input Employee_ID First_Name $;    /* '$' indicates a character variable */
run;          
```

## Length

Defines the type and length of a variable when reading in data from a file.

<pre>
<b>LENGTH</b> <em>variable(s) [$] length;</em>
</pre>

```
data work.subset;
    length First_Name $ 12 Last_Name $ 18;  /* must be BEFORE input statement */
    infile ...;
    input First_Name $ Last_Name $;  /* actually don't need '$'s, length identifies the characters */
run;
```
