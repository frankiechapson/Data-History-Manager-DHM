# Data History Manager DHM

## Oracle SQL and PL/SQL solution to log data changes

**( the full manual is in the doc file! )**

## Why?
The Oracle has not got an audit log function which can show the data changes on row or column level. The built-in auditing can only show that who and what did with a table eg. update, but it could not show which rows and/or which data has changed from what to what.  
The Oracle DBMS_FGA solution can only show the sql commands what executed.  
The WorkSpace Manager has a history possibility but it stores the whole row data. It is not too economical way.  
That is not so easy to provide a generic solution for the audit log problem, because there are a lot of data types and the user can define additional types eg nested types.  
So, a generic solution could not be prepared for all these things.  
But in most of cases a simple solution could be enough to manage the native data types.  
I made this tool for these cases.


## How?
The DHM could be in a separate schema. The tables of this schema are editable via DHM package, and are readable via views, and it uses triggers for logging, but this triggers are in its property, so they are not visible for the logged schema users.  
But you can install it into the production schema too.

## Way of working
Every log of every schema, table rows, columns will be stored in the same place. There are two levels of logs:

### Row
In the row level there are who, when, which program with, what (insert, update or delete) did on which table which row. The rows are identified its primary key. That means every logged table must be a primary key. This is the first restriction.

### Column
The column level log adds the previous value of the modified column to the row level log. It shows the real changes but only the past. The present value is in the table. This means  
* The INSERT command creates only a row log.
* The UPDATE command create a row and one or more column logs with the previous values of the changed columns.
* The DELETE command stores the whole data row (every columns) 

The previous value of the columns are stored in a (very long) VARCHAR2 field. The solution can handle only number, string and date (so native) data types. These data types are convertable to VARCHAR2.


The set of history managed tables and columns are stored in a config table.

The logging of changes managed by database triggers which are owned by DHMGR schema (if the DHMGR has installed into separated schema), so they are not visible for the logged table owners.  
These triggers are generated by strored procedures but the generator procedures do not run atomatically. Every time when a table structure has changed the user has to start the generators for the table manually, if it necessary.


## Limits and restrictions
It can log the following data types only:  
CHAR, DATE,FLOAT,NCHAR,NUMBER,NVARCHAR2,VARCHAR2, TIMESTAMP

The whole length of primary key data must be 400 chars max.

The length of the column log field is 4000 char, so only the first 4000 chars will be stored of the data fields. (in Unicode only 2000 chars)

 ... see doc for more!
