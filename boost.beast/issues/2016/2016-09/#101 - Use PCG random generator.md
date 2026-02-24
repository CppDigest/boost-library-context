# #101 - Use PCG random generator [Closed]

> Username: vinniefalco  
> Created at: 2016-09-28 23:12:08 UTC  
> Updated at: 2017-06-21 01:02:38 UTC  
> Closed at: 2017-06-21 01:02:38 UTC  
> Url: https://github.com/boostorg/beast/issues/101  

http://www.pcg-random.org/

---

## Comment 1

> Username: HowardHinnant  
> Created at: 2016-09-28 23:49:08 UTC  
> Url: https://github.com/boostorg/beast/issues/101#issuecomment-250334220  

One nit to keep in mind on this:  The license appears to have the "binary clause".  The copyright must accompany _compiled_ forms of this software.

---

## Comment 2

> Username: vinniefalco  
> Created at: 2016-09-29 00:32:06 UTC  
> Url: https://github.com/boostorg/beast/issues/101#issuecomment-250340085  

Says Apache license, where are you seeing the binary clause?

---

## Comment 3

> Username: HowardHinnant  
> Created at: 2016-09-29 01:13:28 UTC  
> Url: https://github.com/boostorg/beast/issues/101#issuecomment-250345499  

https://www.apache.org/licenses/LICENSE-2.0  
Search for "Object form".

---

## Comment 4

> Username: HowardHinnant  
> Created at: 2016-09-29 01:54:38 UTC  
> Url: https://github.com/boostorg/beast/issues/101#issuecomment-250350591  

It is possible that if this is an issue, the author might agree to an exemption on the binary clause.  This is what I did for libc++ as documented here: http://llvm.org/docs/DeveloperPolicy.html#license (search for libc++).  The binary clause was not important to me for libc++ (or any other open source software I have written).  I have no idea whether or not that is true for the pcg author.

---

## Comment 5

> Username: vinniefalco  
> Created at: 2017-06-21 01:02:38 UTC  
> Url: https://github.com/boostorg/beast/issues/101#issuecomment-309933070  

Not needed
