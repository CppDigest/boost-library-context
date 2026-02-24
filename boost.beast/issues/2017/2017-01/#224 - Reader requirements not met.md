# #224 - Reader requirements not met [Closed]

> Username: WeissGotsman  
> Created at: 2017-01-15 11:53:18 UTC  
> Updated at: 2017-02-18 12:13:35 UTC  
> Closed at: 2017-02-18 12:13:35 UTC  
> Url: https://github.com/boostorg/beast/issues/224  

I cannot compile with visual studio 2015 with boost 1.61.0, do I miss anything? google does not help me either  
  
Severity	Code	Description	Project	File	Line	Suppression State  
Error C2338 Reader requirements not met http-crawl X:\_public\beast\Beast\include\beast\http\impl\read.ipp	326	  
  
![2017-01-15_19-55-24](https://cloud.githubusercontent.com/assets/22855561/21962273/be20ee62-db5c-11e6-94fa-375b98941daa.jpg)

---

## Comment 1

> Username: vinniefalco  
> Created at: 2017-01-15 12:15:06 UTC  
> Updated at: 2017-01-15 12:15:17 UTC  
> Url: https://github.com/boostorg/beast/issues/224#issuecomment-272691372  

What version of Visual Studio? Is it Update 3?

---

## Comment 2

> Username: WeissGotsman  
> Created at: 2017-01-15 12:22:19 UTC  
> Url: https://github.com/boostorg/beast/issues/224#issuecomment-272691763  

![2017-01-15_20-21-53](https://cloud.githubusercontent.com/assets/22855561/21962412/4d206950-db60-11e6-8add-04be751f51ba.jpg)

---

## Comment 3

> Username: vinniefalco  
> Created at: 2017-01-15 12:23:21 UTC  
> Url: https://github.com/boostorg/beast/issues/224#issuecomment-272691826  

You need at least 2015 Update 3 to compile - earlier versions of Visual Studio have bugs in their C++11 compliance. I will add a static assert or pragma to detect these incompatible versions and generate a better compiler error.

---

## Comment 4

> Username: vinniefalco  
> Created at: 2017-02-18 12:13:35 UTC  
> Url: https://github.com/boostorg/beast/issues/224#issuecomment-280841817  

I have not been able to figure out the correct value for `_MSC_FULL_VER` so I will close this. If you can find out what the correct value should be, to detect version older than Visual Studio 2015 Update 3 please re-open the issue, thanks!
