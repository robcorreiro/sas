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

### Date Constant
```
'ddmmm<yy>yy'd
```

Other WHERE select examples:

<pre>
where Gender = ' ';
where Salary ^= .;
where Salary >= 50000;
where Hire_Date < '01Jan2000'd
where Country in ('AU', 'US'); <b>OR</b> logic
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


## Sorting










