# #353 - Bug in example code for Embedding page in Boost.Python tutorial [Open]

> Username: BongoVR  
> Created at: 2021-02-22 12:28:58 UTC  
> Updated at: 2021-02-22 14:36:54 UTC  
> Url: https://github.com/boostorg/python/issues/353  

Hi all,  
  
on page https://www.boost.org/doc/libs/1_75_0/libs/python/doc/html/tutorial/tutorial/embedding.html there is a bug in the example code.  
  
The line  
<code>object ignored = exec("hello = file('hello.txt', 'w')\n"</code>  
will cause an error_already_set exception (using Python 3.7 on Windows 10).  
  
The line should read   
<code>object ignored = exec("hello = open('hello.txt', 'w+')\n"</code>  
  
Best regards
