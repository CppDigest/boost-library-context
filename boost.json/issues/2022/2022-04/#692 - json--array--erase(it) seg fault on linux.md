# #692 - json::array::erase(it) seg fault on linux [Closed]

> Username: jeremycepres  
> Created at: 2022-04-13 12:01:10 UTC  
> Updated at: 2022-05-14 15:44:59 UTC  
> Closed at: 2022-05-14 15:44:59 UTC  
> Url: https://github.com/boostorg/json/issues/692  

array::erase(it) seg faults when called on a thread other than the main thread  
  
environment:  
  
$ uname -a  
Linux localhost.localdomain 5.16.18-200.fc35.x86_64 #1 SMP PREEMPT Mon Mar 28 14:10:07 UTC 2022 x86_64 x86_64 x86_64 GNU/Linux  
  
$ gcc --version  
gcc (GCC) 11.2.1 20220127 (Red Hat 11.2.1-9)  
  
tested on boost 1.76.0 and 1.78.0  
  
example code attached:  
  
[test.zip](https://github.com/boostorg/json/files/8481188/test.zip)  
  
regards,  
jeremy godfrey

---

## Comment 1

> Username: vinniefalco  
> Created at: 2022-04-13 12:44:18 UTC  
> Url: https://github.com/boostorg/json/issues/692#issuecomment-1098006527  

This fails for me without a separate thread  
```  
    void  
    testIssue692()  
    {  
	    array a;  
	    object obj;  
	    obj["test1"] = "hello";  
	    a.push_back(obj);  
	    a.push_back(obj);  
	    a.push_back(obj);  
	    a.push_back(obj);  
	    a.push_back(obj);  
	    while(a.size())  
		    a.erase(a.begin());  
    }  
```  
  
Congrats Jeremy :)

---

## Comment 2

> Username: jeremycepres  
> Created at: 2022-04-13 17:49:11 UTC  
> Url: https://github.com/boostorg/json/issues/692#issuecomment-1098324069  

great, thanks Vinnie
