# Google Dorking
> Google hacking, also named Google dorking,is a computer hacking technique that uses Google Search and other Google applications to find security holes in the configuration and computer code that websites use. it can return information that is difficult to locate through simple search queries. That description includes information that is not intended for public viewing but that has not been adequately protected.

In August 2014, the United States Department of Homeland Security (DHS), the FBI and the National Counterterrorism Center issued a bulletin warning agencies to guard against the potential for Google dorking on their sites. One of the first intrusion prevention measures proposed is to conduct Google dorking expeditions using likely attack parameters to discover what type of information an intruder could access.

## Let's get access to a CCTV camera

Go to google.com and search for 
```
inurl:view/shtml
```
![google search](img/random/cctvsearch.png)

Now let's navigate to the 6th result and see what we will be greeted with

![cctv](img/random/cctv.png)

We can see that we just got access to one of the cctv camera with a simple google dork.

In this way we can get access to various hidden files,unintended files etc by concating various dorks or using them alone.

There are many similar advanced operators which can be used to exploit insecure websites:

| Operator   	| Purpose                  	| Mixes with Other Operators? 	| Can be used Alone? 	| Web 	| Images     	| Groups     	| News         	|
|------------	|--------------------------	|-----------------------------	|--------------------	|-----	|------------	|------------	|--------------	|
| ntitle     	| Search page Title        	| yes                         	| yes                	| yes 	| yes        	| yes        	| yes          	|
| allintitle 	| Search page title        	| no                          	| yes                	| yes 	| yes        	| yes        	| yes          	|
| inurl      	| Search URL               	| yes                         	| yes                	| yes 	| yes        	| not really 	| like intitle 	|
| filetype   	| specific files           	| yes|yes                     	| yes                	| no  	| not really 	|            	|              	|
| intext     	| Search text of page only 	| yes                         	| yes                	| yes 	| yes        	| yes        	| yes          	|

Here is a list of various dorks : download pdf

Be updated with dorks from exploit-db.com which is an awesome database for dorks and other exploits.

Try to find passwords.xls :P