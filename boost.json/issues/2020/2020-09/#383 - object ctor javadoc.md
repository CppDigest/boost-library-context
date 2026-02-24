# #383 - object ctor javadoc [Closed]

> Username: vinniefalco  
> Created at: 2020-09-22 17:06:38 UTC  
> Updated at: 2020-09-26 01:50:46 UTC  
> Closed at: 2020-09-26 01:45:22 UTC  
> Url: https://github.com/boostorg/json/issues/383  

The object initializer constructor is listed under "Copy constructor".

---

## Comment 1

> Username: vinniefalco  
> Created at: 2020-09-26 01:49:06 UTC  
> Url: https://github.com/boostorg/json/issues/383#issuecomment-699268763  

![image](https://user-images.githubusercontent.com/1503976/94327418-c88ed000-ff5f-11ea-87b9-a832dcc217ff.png)

---

## Comment 2

> Username: vinniefalco  
> Created at: 2020-09-26 01:50:42 UTC  
> Url: https://github.com/boostorg/json/issues/383#issuecomment-699269133  

This can be observed by changing the brief to just `/** Constructor` on the initializer-list overloads. It is like the 2nd overload that accepts initializer-list clones the brief of the first overload.
