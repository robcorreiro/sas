## Comments

```
/* This is a  
   Multi-line Comment */ 
 
* Statement comment; (usually for single lines) 
 
*-----------------------------------------* 
| Statement comments end at a semicolon   | 
*-----------------------------------------*; 
```
 
## Printing 

<pre>
/* Select specific vars when printing and sums specific */ 
proc print data=orion.sales; 
    var Last_Name First_Name Salary; 
    <b>sum Salary;  * order usually not important </b>
run; 
</pre>


## Selecting 


Can only have ONE WHERE statement PER step, last one wins.


The **var** keyword picks what variables are included in the output.

<pre>
proc print data=orion.sales; 
    var Last_Name First_Name Salary; 
    <b>where Salary < 25500;  * no quotes needed for numeric</b>
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

# Advanced Sorting

- Can sort by multiple variables and output multiple tables separated by group

```proc sort data=orion.sales 
          out=work.sales;
    by Country descending Salary;
run;

proc print data=work.sales noobs;
    by Country;  * doesn't sort, tells SAS to expect data in this order, displays output in separate tables
run;
```

# Examples

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

# SPLIT=

- Split words in column headers using labels and a specified character.

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

# Formats

<pre>
$w. - standard character
w.d - standard numeric
COMMAw.d
DOLLARw.d
COMMAXw.d
EUROXw.d
MMDDYY10.
</pre>

# Examples

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
