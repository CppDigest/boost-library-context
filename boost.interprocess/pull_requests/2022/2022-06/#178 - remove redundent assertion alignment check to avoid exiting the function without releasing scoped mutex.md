# #178 remove redundent assertion alignment check to avoid exiting the function without releasing scoped mutex [Merged]

> Username: lioriz  
> Created at: 2022-06-13 12:59:25 UTC  
> Updated at: 2022-07-16 16:06:00 UTC  
> Merged at: 2022-07-16 13:29:37 UTC  
> Closed at: 2022-07-16 13:29:37 UTC  
> Url: https://github.com/boostorg/interprocess/pull/178  

The assertion in the ```check_sanity()``` function causes the function to crash without releasing the ```shared mutex```.  
The assertion is redundant due to ```alignment check``` in the following lines that returns ```false``` in case of failure and releases the ```shared mutex```.  
PR for [Issue 177](https://github.com/boostorg/interprocess/issues/177)

---
