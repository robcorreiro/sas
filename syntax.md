## Comments

```
/* This is a  
   Multi-line Comment */ 
 
* Statement comment; (usually for single lines) 
 
*-----------------------------------------* 
| Statement comments end at a semicolon   | 
*-----------------------------------------*; 
```
 
# Printing 

<pre>
/* Select specific vars when printing and sums specific */ 
proc print data=orion.sales; 
    var Last_Name First_Name Salary; 
    <b>sum Salary;  * order usually not important </b>
run; 
</pre>

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

Split words in column headers using labels and a specified character.

<pre>
proc print data=... <b>split='*'</b>;
    var ...
    label Last_Name = 'Last<b>*</b>Name'
          Salary = 'Annual<b>*</b>Salary';
run;
</pre>

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

## Numeric Formats

```
$w. - standard character
w.d - standard numeric
COMMAw.d
DOLLARw.d
COMMAXw.d
EUROXw.d
MMDDYY10.
```

## Date Formats

```
MMDDYY10.  /* 01/01/1960 */
MMDDYY8.  /* 01/01/60 */
MMDDYY6.  /* 010160 */
DDMMYY10.  /* 31/12/1960 */
DDMMYY8.
DDMMYY6.
DATE7.  /* 31DEC59 */
DATE9.  /* 31DEC1959 */
WORDDATE.  /* January 1, 1960 */
WEEKDATE.  /* Friday, January 1, 1960 */
MONYY7.  /* JAN1960 */
YEAR4.  /* 1960 */

/* yymmddx */
format ... yymmddd10.;

yymmddx, replace x with:
Default = dash
b = blanks
c = colons
d = hyphens/dashes
p = periods
s = slashes
```

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

# Reading Spreadsheet Data

Requires <b>SAS/ACCESS Interface to PC Files</b> license.
 
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

# Types of Data

## Standard Numeric Data

Contains: +, -, decimal points, or E notation

```
58
67.23
-23
1.2E-2
```

## Nonstandard Numeric Data

```
(23)
$67.23
5,823
01/12/2010
12May2009
```

# Reading Raw (Flat) Data Files

## Standard (List) Input

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

---

# Reading Nonstandard Delimited Data

## Informats

- required to read nonstandard numeric data
- describe the data value and tells SAS how to convert to internal representation

```
input Employee_ID First_Name :$12.;  /* ':' denotes an informat */
```

```
COMMA.  $12,345 --> 12345
DOLLAR 
COMMAX.
DOLLARX
EUROX.
$CHAR. - retains leading spaces (removed by default without informat)
$UPCASE.
MMDDYY. 	010160	 -->	 0
DDMMYY.
DATE.		31DEC59	 --> 	-1
```

```
input Birth_Date :date. Hire_Date :mmddyy.;
```

## Additional SAS Statements

Can be used AFTER input statement.

```
/* if, keep, label, and format OK to use since they are AFTER input statement */
data work.sales;
    infile ...;
    input ...;
    if Country='AU';
    keep ...;
    label ...;
    format ...;
run;
```

## Datalines

**DATALINES** statement supplies data within a program.

```
data work.newemps;
    input ...;
datalines;  /* MUST be last in data step */
Steven Worton Auditor $40,450
Merle Hieds Trainee $24,025
;
run;
```

## DSD

- Changes default delimiter to comma
- Treats consecutive delimiters as missing
- Allows embedded delimiters if surrounded by quotation marks.

```
data ...;
    infile "&path\phone2.csv" dsd;  /* usually a best practice to use */
    ...
run;
```

## MISSOVER

- Prevents SAS from loading new record when end of record is reached.
- Handles case when you're missing a variable at the end of a record.

```
data ...;
    infile "..." missover;  /* can be used with other options (e.g. dsd, dlm) */
    ...;
run;
```

# Reading Formatted Input

## Column Format

Pointer controls:

- @n - moves pointer to column 'n'.
- +n - moves pointer n positions.

<pre>
<b>INPUT</b> <em>pointer-control variable informat . . .;</em>
</pre>

```
/* example informat */
data work.discounts;
    infile ...;
    input @1 Cust_type 4.
          @5 Offer_dt mmddyy8.
          @14 Item_gp $8.
run;          
```

## PAD

- Goes on **INFILE** statement
- Pads the input buffer with blanks
- Useful if you have a variable-length field at the end of each record

## LRECL - Logical record length

- Goes in **INFILE** statement
- Set to a number so SAS doesn't truncate a record if records are very long.

```
INFILE "&PATH\FILENAME.DAT" PAD LRECL=2000;
```

# Multiple INPUT Statements

What if we have multiple records (lines) in a file and want to put it in ONE observation?

```
data contacts;
    infile "&path\address.dat";
    input FullName $30.;
    input;
    input Address2 $25.;
    input Phone $8.;
run;

/* can be done with one INPUT */
/* '/' tells SAS to load next record */
data contacts;
    infile "&path\address.dat";
    input FullName $30. /  /
          Address2 $25. /
          Phone$8.;
run;          
```

## Mixed Record Types 

**IMPORTANT** for CASE STUDY.

Trailing **@** holds raw data record in input buffer until:
 - executes an INPUT without @
 - begins next DATA step iteration
 
 ```
 data salesQ1;
    infile ...;
    input @6 Location $3. @;  /* holds input buffer */
    if Location='USA' then
        input @10 SaleDate mmddyy10.
              @20 Amount 7.;  /* until the end of this input */
    else if Location='EUR' then
        input @10 SaleDate date9.
              @20 Amount commax7.;  /* or this one */
run;              
 ```

# Manipulating Data with Functions

Date Functions: (taking *SAS-date* as input)
 - YEAR
 - QTR
 - MONTH
 - DAY
 - WEEKDAY

Other Date Functions:
 - TODAY()
 - DATE()
 - MDY(month,day,year) returns a SAS-date
 
 ## Using SAS Functions
 
 ```
 /* variables can be used in functions */
 BonusMonth = month(Hire_Date)
 AnnivBonus = mdy(BonusMonth, 15, 2008);
 
 /* can be part of any SAS expression */
 if month(Hire_Date) = 12;
 
 /* functional composition */
 AnnivBonus = mdy(month(Hire_Date), 15, 2012);
 ```
 
## Function Example

```
data work.birthday;
	set orion.customer;
	Bday2012 = mdy(month(birth_date), day(birth_date), 2012);
	BdayDOW2012 = weekday(Bday2012);
	Age2012 = (bday2012 - birth_date) / 365.25;
	keep customer_name birth_date bday2012 bdaydow2012 age2012;
	format bday2012 date9. age2012 3.;
run;

proc print data=work.birthday;
run;
```

# Conditionals

```
/* IF */
data work.comp;
    set orion.sales;
    if Job_Title='Sales Rep' then
        Bonus=1000;
    ....
run;

/* ELSE-IF */
data work.comp;
    set orion.sales;
    if Job_Title='Sales Rep. IV' then
        Bonus=1000;
    else if Job_Title='Sales Manager' then
        Bonus=1500;
run;     

/* ELSE */
data work.com;
    set orion.sales;
    if ... then ....;  /* can exist on same line */
    else Bonus=500;
run;    

/* OR */
data work.comp;
    set ...;
    if ... or ... then ...;
run;    
```

## Executing Multiple Statements using DO ... END

By default, only a single statement can be used after a conditional.

<pre>
<b>IF</b> <em>expression</em> <b>THEN</b> <em>statement;</em>
</pre>

```
/* DO ... END */
data work.bonus;
    set orion.sales;
    if Country='US' then do;  /* can be used with a single statement for consistency */
        Bonus=500;
        Freq='Once a Year';
    end;
    else if ... then ... do;
        ...;
    end;
run;
```

## Case Testing using SELECT ... WHEN

```
/* Using statements */
select (a);
    when (1) x=x*10;
    when (2);
    when (3,4,5) x=x*100;
    otherwise;
end;    

/* Using DO groups */
select (payclass);
    when ('monthly') amt=salary;
    when ('hourly')
      do;
          amt=hrlywage*min(hrs,40);
          if hrs>40 then put 'CHECK TIMECARD';
      end;                                     /* end of do */
    otherwise put 'PROBLEM OBSERVATION';
end;                                           /* end of select */    
```


# Concatenating Data Sets

- Any # of data sets can be included
- PDV reinitialized before processing next data set

```
data ...;
    set SAS-data-set1 SAS-data-set2 ...;
run;
```

## RENAME=

 - Changes the name of a variable.
 - Affects the PDV
 - Input data set not changed.

```
data ...;
    set SAS-data-set(RENAME=(old-name-1=new-name-1
                             old-name-2=new-name-2
                             ...
                             old-name-n=new-name-2));
run;

data empsall2;
    set empscn empsjp(rename=(Region=Country));
run;
```

# Merging Data Sets

## One-to-One

MERGE statement joins observations from two or more SAS data sets into single observations.

```
/* Must sort data sets BEFORE match-merge */
proc sort data=input-SAS-data-set <out=output-data-set>;
	by <descending> by-variable(s);
run;	

data ...;
    merge sas-data-set-1 sas-data-set2;
    by by-variable(s);  /* indicates a match-merge and lists variable(s) to match
run;

e.g.
data empsauh;
    merge empsau phoneh;
    by EmpID;  /* must be common to all data sets */
run;
```

## One-to-Many / Many-to-One

- Results are the same between the two types of merges
- Though the order of variables is different



# Merging with Non-Matches
