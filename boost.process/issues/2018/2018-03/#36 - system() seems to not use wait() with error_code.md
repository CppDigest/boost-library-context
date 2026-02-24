# #36 - system() seems to not use wait() with error_code [Closed]

> Username: nurelin  
> Created at: 2018-03-13 16:54:08 UTC  
> Updated at: 2018-03-28 11:45:30 UTC  
> Closed at: 2018-03-28 11:45:30 UTC  
> Url: https://github.com/boostorg/process/issues/36  

On my system, when a program launched via system() with an error_code segfaults, it raise an exception.  
  
To me, it seems to be abnormal.  
What do you think ?

---

## Comment 1

> Username: klemens-morgenstern  
> Created at: 2018-03-23 04:06:07 UTC  
> Url: https://github.com/boostorg/process/issues/36#issuecomment-375538453  

That does seem abnormal indeed. What is your system?

---

## Comment 2

> Username: nurelin  
> Created at: 2018-03-28 11:45:21 UTC  
> Url: https://github.com/boostorg/process/issues/36#issuecomment-376857604  

I cannot reproduce on my end, so I guess the problem not in boost. Sorry
