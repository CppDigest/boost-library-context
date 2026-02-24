# #126 - sort comparison operators together? [Closed]

> Username: vinniefalco  
> Created at: 2022-08-05 05:04:11 UTC  
> Updated at: 2022-08-05 15:00:49 UTC  
> Closed at: 2022-08-05 15:00:49 UTC  
> Url: https://github.com/boostorg/docca/issues/126  

Is it possible to change how operators are sorted, so that the equality/inequality are sorted together, while the stream operators (`operator<<` and `operator>>`) or the arithmetic operators like plus and minus are kept separate? See:  
  
![image](https://user-images.githubusercontent.com/1503976/183005253-4943c4ca-0566-4b2a-951f-7908fec331fc.png)

---

## Comment 1

> Username: vinniefalco  
> Created at: 2022-08-05 05:04:22 UTC  
> Url: https://github.com/boostorg/docca/issues/126#issuecomment-1206045922  

Do you see the odd man out? lol

---

## Comment 2

> Username: evanlenz  
> Created at: 2022-08-05 05:53:51 UTC  
> Updated at: 2022-08-05 05:54:12 UTC  
> Url: https://github.com/boostorg/docca/issues/126#issuecomment-1206071292  

Hmm, this might be an argument for turning off sorting altogether, so the results show up in code (and thus Doxygen) order (which I verified). But that would impact a lot of other pages, and I'm assuming we still in the general case want to sort member names alphabetically, yes?

---

## Comment 3

> Username: evanlenz  
> Created at: 2022-08-05 05:55:20 UTC  
> Url: https://github.com/boostorg/docca/issues/126#issuecomment-1206072070  

You can see the relevant code for sorting is quite generic: https://github.com/boostorg/docca/blob/develop/include/docca/base-stage1.xsl#L349
