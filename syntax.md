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

- Can be used in a DATA step

```
/* Using statements */
select (a);
    when (1) x=x*10;
    when (2);
    when (3,4,5) x=x*100;
    otherwise;  /* MUST BE PRESENT even if not using it */
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

/* Can test multiple values, not limited to testing ONLY country */
/* More flexible, not limited to testing only equality, can do <, >, etc. */
data usa australia other;
    set orion.employees;
    select;
        when (country='US') output usa;
        when (country='AU') output australia;
        otherwise output other;
    end;
run;
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

## Merging with Non-Matches

**IN=** data set option creates a variable which indicates whether the data set contributed to building the current row.

```
MERGE SAS-data-set (IN=variable) ...

0 - Indicates the data set DID NOT contribute
1 - Indicates the data set DID contribute


data empsauc;
    merge empsau(in=Emps)
          phonec(in=Cell);
    by EmpID;
    if Emps=1 and Cell=0;  /* Employees who DO NOT have phones */
    if Emps and not Cell;  /* Alternate syntax */
    if not Emps and Cell;  /* Invalid employee ID with phone */
    if not Emps or not Cell;  /* For non-matches. NOTE THE OR, instead of and. */
    ...
run;    
```

Use a subsetting IF when the subsetting variable is NOT in ALL data sets in MERGE statement.

# Input and Output

- The **output** statement can be used to explicitly output the contents of PDV to data set.
- There is no more implicit output at the end of a data step iteration.

```
/* Writes PDV to the data set eing created */
data forecast;
    set orion.growth;
    Year=1;
    Total_Employees=Total_Employees*(1+Increase);
    output;
    Year=2;
    Total_Employees=Total_Employees*(1+Increase);
    output;  /* Need explicit output */
run;
```

## Multiple Output Data Sets

- Specify multiple data sets in the data statement for multiple potential outputs.
- Can only print ONE data set at a time.

```
data usa australia other;
    set orion.employee_addresses;
    if Country='AU' then output australia;
    else if Country='US' then output usa;
    else output other;
run;

/* Alternate */
data usa australia other;
    set orion.employee_addresses;
    select (Country);
        when ('US') output usa;
        when ('AU') output australia;
        otherwise output other;
    end;
run;
```


## Data Set Options

### DROP=, KEEP=

```
/* Drops variables from specific output data sets */
data new-data-set(drop=variable(s));
    set old-data-set;
    ...;
run;

/* Can also use KEEP */
data usa(keep=Employee_Name City State)
     australia(drop=Street_ID State)
     other;
    set orion.employees;
    ...;
run;    

/* Can remove data on the input data set */
data new-data-set;
    set old-data-set(drop=variable(s));
    ...
run;
```

### FIRSTOBS=, OBS

Can only be used with **input** data sets. Cannot be used with output data sets.

```
/* Stop after processing observation 100 */
data australia;
    set orion.employee_address (obs=100);
    ...;
run;

/* start at a specific observation */
data australia;
    set orion.employee_address (firstobs=50 obs=100);
    ...;
run;    

/* INFILEs also work */
data employees;
    infile "..." firstobs=11 obs=15;  /* parens NOT used */
    input ...;
run;    

/* Can be used in PROC steps */
proc print data=origin.employee_addresses (firstobs=10 obs=15);
    var ...;
run;    
```

# Summarizing Data

## Accumulating Variables

```
/* Retain the value of a variable AND set and initial value. */
data mnthtot;
    set orion.aprsales;
    retain Mth2Dte 0;
    Mth2Dte = sum(Mth2Dte, SaleAmt);  /* SUM ignores missing values */
run;    
```

## Sum Statement

In the following code block, **Mth2Dte** will:

- be initialized to 0
- automatically retained
- increased by **SaleAmt** for each observation
- ignores missing values of SaleAmt

```
data mthtot2;
    set work.aprsales2;
    Mth2Dte + SaleAmt;  /* variable + expression */
run;    
```

## Totals for a Group of Data

Subtotals for groups of data.

```
/* Need to sort first */
proc sort data=... out=...;
    by Dept;  /* copied down below into DATA step */
run;

/* incomplete version */
data deptsals(keep=Dept DeptSal);
    set salsort;
    by Dept;  /* creates two temp vars for each var listed, First.Dept and Last.Dept */
    ...  /* Can also be used to create a unique list of keys, e.g. output all First.variable = 1 */
run;    

/* complete version */
data deptsals(keep=Dept DeptSal);
    set SalSort;
    by Dept;  /* sets up temp vars */
    if First.Dept then DeptSal=0;  /* reset for each dept */
    DeptSal + Salary;  /* accumulation variable */
    if Last.Dept;  /* output only after processing last row */
run;    
```

What if we want to summarize data for a subset of a group? (e.g. Project within a Department)

**IMPORTANT FOR EXAM **

```
/* Remember, need to sort first. Now with 2 vars */
proc sort data=orion.projsals out=projsort;
    by Proj Dept;
run;    

data pdsals; 
    set projsort;
    by Proj Dept;  /* Higher level = First.Proj, Last.Proj, Lower Level = First.Dept, Last.Dept */
    if First.Dept then do;  /* only need to care about the inner-most level */
        DeptSal = 0;
        NumEmps = 0;
    end;
    DeptSal + Salary;
    NumEmps + 1;
    if Last.Dept;  /* higher level changes implicitly changes the lower level .
```

# Data Transformations

## Character Transformations

```
/* Alternatives to entering all var names */
data contrib;
    set ...;
    Total=sum(of Qtr1-Qtr4);  /* keyword OF precedes variable list */
    Total=sum(of Tot:);  /* name prefix list, e.g. sum all vars which start with Tot */
    Total=sum(of _Numeric_);  /* sums all numeric vars */
run;
```

**SUBSTR()**

<pre>
<em>NewVar</em>=<b>SUBSTR</b>(<em>string,start[,length]</em>);
</pre>

```
Org_Code=substr(Acct_code, 4);  /* AQI2 --> 2 */
```

**LENGTH()**

<pre>
<em>NewVar</em>=<b>LENGTH</b>(<em>argument</em>);
</pre>

```
Code='ABCD   ';
Last_NonBlank=length(Code);  /* 4 */
```

**PROPCASE()**

<pre>
<em>NewVar</em>=<b>PROPCASE</b>(<em>argument [,delimiter(s)]</em>);  * Default delimiters: Blank / - ( . tab;
</pre>

```
Name='SURF&LINK SPORTS';
foo=propcase(Name);  /* Surf&link Sports */
bar=propcase(Name, ' &');  /*  Surf&Link Sports */ /* space AND & used as delimiters */
```

```
data charities;  /* 2 represents charities for example */
    length ID $ 5;
    set ...;
    if substr(Acct_Code, length(Acct_Code), 1) = '2';  /* Acct_Code = 'AQI2' */
    ID=substr(Acct_Code, 1, length(Acct_Code)-1);  /* ID = 'AQI' */
    Name=propcase(Name);
run;
```

**SCAN()**

- Returns the nth word of a character value
- Missing returned if fewer than n words in string
- Use negative n value to scan from end of string 
- 

<pre>
<em>NewVar</em>=<b>SCAN</b>(<em>string,n[,charlist]</em>);
</pre>

```
Name='Farr,Sue';
FMName=scan(Name, 2, ',');  /* FMName = Sue */
```

**CATX()**

Other CAT functions:

 - CAT doesn't remove leading or trailing blanks
 - CATS removes leading and trailing blanks
 - CATT removes trailing blanks

<pre>
<em>NewVar</em>=<b>CATX</b>(<em>sep, string1, ... , stringn</em>);
</pre>

```
FMName='Sue';
LName='Farr';
FullName=catx(' ', FMName, LName);  /* Sue Farr */
```

**Concatenation Operator**

<pre>
<em>NewVar</em>=<em>string1</em> <b>!!</b> <em>string2</em>;
<em>NewVar</em>=<em>string1</em> <b>||</b> <em>string2</em>;
</pre>

```
area = '919'; Number = '531-0000';
Phone = '(' !! area !! ') ' !! Number;  /* (919) 531-0000 */
Phone = '(' || area || ') ' || Number;  /* Alternate with vertical bars */
```

**FIND(string,substring<,modifiers,startpos)**

Returns column number where substring is found within string, or 0 if not found.

```
data find;
    Text='AUSTRALIA, DENMARK, US';
    Pos1=find(Text,'US');  /* 2  */
    Pos2=find(Text,' US');  /* 20 */
    Pos3=find(Text,'us'):  /* 0 */
    Pos4=find(Text,'us','I');  /* 2 */  /* optional 'I' modifier ignores case, 'T' would ignore trailing blanks */
    Pos5=find(Text,'us','I',10);  /* 21 */ /* optional start modifier */
```

**TRANWRD(source,target,replacement)**

Replaces all occurrences of one word with another.

- Does NOT remove trailing blanks from *target* or *replacement*.
- Default length of NewVar is given length 200
- Does nothing if target not found

**COMPRESS(source<,chars>)**

Removes the *chars* from the *source*.

```
ID=20 01-005 024';
foo=compress(ID);  /* '2001-005024' */
bar=compress(ID,'-');  /* '20 01005 024' */
foobar=compress(ID,' -');  /* '2001005024' */
```

Other Functions which remove blanks:

 - TRIM(string) removes trailing blanks
 - STRIP(string) removes leading AND trailing blanks
 - COMPBL(string) replaces 2+ consecutive blanks with a single blank

```
data correct;
    set ...;
    if find(Product, 'Mittens', 'I') > 0 then do;  
        substr(Product_ID, 9, 1) = '5';
        Product = Tranwrd(Product, 'Luci ', 'Lucky ');
    end;
run;
```

## Numeric Value Transformation

**ROUND(argument<,rounding-unit)**

Rounded to the nearest multiple of rounding unit.

```
data truncate;
    foo = round(12.12);  /* 12 */
    bar = round(42.65, .1);  /* 42.7 */
    foobar = round(-6.478);  /* -6 */
    barfoo = round(96.47, 10);  /* 100 */
    foo = roud(42.65, .5)  /* . */
run;    
```

**CEIL(argument)**

Returns smallest integer >= argument.

```
x = ceil(4.4); /* 5 */
```

**FLOOR(arguent)**

Returns greatest integrer <= argument.

```
y=floor(3.6);  /* 3 */
```

**INT(argument)**

Returns integer portion of argument.

```
foo = 6.478;
bar = -6.478;

ceil(foo);  /* 7 */
floor(foo);  /* 6 */
int(foo);  /* 6 */

ceil(bar);  /* -6 */
floor(bar);  /* -7 */
int(bar);  /* -6 */
```

## Statistical Functions

- SUM
- MEAN
- MIN
- MAX
- N
- NMISS
- CMISS

## Converting Variable Types

### Implicit Conversion

#### Character to Numeric

SAS can automatically convert when the character string (containing a numeric value) is in **standard** form.

```
data hrdata;
    keep EmpID;
    set orion.convert;
    EmpID = ID + 11000;  /* ID in (36, 48, 52, ...) */
run;    

/* fails because GrossPay NOT in standard form */
data hrdata;
    keep GrossPay Bonus;
    set orion.convert;
    Bonus = GrossPay * 0.10;  /* GrossPay in ('52,000', '32,000', '49,000', ...) */
```

### Numeric to Character

Automatically converts when:

- assigning to a char var
- concat operation
- function which accepts char args


### Explicit Conversion

**INPUT(source,informat)** - character to numeric

```
data conversions;
    CVar1='32000';
    Cvar2='32.000';
    Cvar3='03may2008';
    Cvar4='030508';
    NVar1=input(CVar1, 5.);
    NVar2=input(CVar2, commax6.);
    NVar3=input(CVar3, date9.);
    NVar4=input(CVar4, ddmmyy6.);
run;    
```

**PUT(source, format)** - numeric to character

Returns val produced when *source* is written with *format*. Always returns char string.

```
data conversion;
    NVar1=614;
    NVar2=55000;
    NVar3=366;
    CVar1=put(NVar1, 3.);
    CVar2=put(NVar2, dollar7.);
    CVar3=put(NVar3, date9.);
run;    
```


### Converting a Var to Another Type

- Rename
- Use **put()** or **input()** as needed to change 
- Assign new val to old name

```
data hrdata(drop=CharGross);
    set orion.convert(rename=(GrossPay=CharGross));
    GrossPay=input(CharGross, comma6.);
run;    
```


# Iteration

## DO LOOPs

<pre>
<b>DO</b> <em>index=start</em> <b>TO</b> <em>stop</em> [<b>BY</b> <em>increment</em>];
<b>END;</b>
</pre>

```
data invest;
    do Year=2008 to 2010;
        Capital+5000;
        Capital+(Capital*0.045);
    end;
run;  /* will output Year 2011, because Year gets incremented after last iteration of loop */
```

## DO UNTIL Loops

- Statements are executed AT LEAST once. 
- Think of as a DO WHILE in other languages.
- In a DO UNTIL, the condition is checked BEFORE index is incremented.

```
/* How many years will it take for the Capital to exceeed 1Mil? */
data invest;
    do until (Capital > 100000000);
        Year+1;
        Capital+5000;
        Capital+(Capital * 0.045);
    end;
run;
```

## DO WHILE Loops

- Statements in loop never execute if expression is initially false
- Think of as a regular WHILE loop in other languages.
- In a DO WHILE, the condition is checked AFTER index is incremented.

```
/* Putting an upper bound on the # of iterations */
data invest;
    do Year=1 to 30 while (Capital <=250000);
        Capital+5000;
        Capital+(Capital * 0.045);
    end;
run;
```

## Nested DO Loops

```
data invest(drop=Quarter);
    set orion.banks;  /* FOR EACH ROW, will iterate over all observations in data set */
    Capital=0;
    do Year=1 to 5;
        Capital+5000;
        do Quarter=1 to 4;
            Capital+(Capital * Rate/4));
        end;
    end;
run;

/* iteration within the 
```
