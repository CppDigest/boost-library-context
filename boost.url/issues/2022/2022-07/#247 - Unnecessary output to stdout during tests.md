# #247 - Unnecessary output to stdout during tests [Closed]

> Username: vinniefalco  
> Created at: 2022-07-26 02:15:46 UTC  
> Updated at: 2022-08-04 03:00:14 UTC  
> Closed at: 2022-08-04 03:00:14 UTC  
> Url: https://github.com/boostorg/url/issues/247  

There's a bunch of junk being emitted to standard out, which bypasses the unit test stream redirection:  
![image](https://user-images.githubusercontent.com/1503976/180908052-06e1e243-f20b-459f-b121-cd5b1cf3871e.png)

---

## Comment 1

> Username: alandefreitas  
> Created at: 2022-07-26 02:22:08 UTC  
> Url: https://github.com/boostorg/url/issues/247#issuecomment-1194900828  

How should we do these snippets then?

---

## Comment 2

> Username: vinniefalco  
> Created at: 2022-07-26 02:37:37 UTC  
> Url: https://github.com/boostorg/url/issues/247#issuecomment-1194924535  

Take the address of the function so it ensures that it compiles?
