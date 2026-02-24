# #9 - variant does not compile in gcc 8.2 and higher in c++17 mode [Closed]

> Username: akrzemi1  
> Created at: 2019-04-06 21:03:59 UTC  
> Updated at: 2019-05-12 16:06:30 UTC  
> Closed at: 2019-05-12 16:06:30 UTC  
> Url: https://github.com/boostorg/variant2/issues/9  

Even including the header causes compiler error: https://wandbox.org/permlink/aD3JtrJLI6s0DpbU

---

## Comment 1

> Username: pdimov  
> Created at: 2019-04-06 22:44:45 UTC  
> Url: https://github.com/boostorg/variant2/issues/9#issuecomment-480543530  

This is caused by a bug in g++ 8/9, which affects mp11. mp11 has a workaround, but in 1.69, it only activates for gcc < 8.2. This is fixed in mp11 develop/master/1.70.

---

## Comment 2

> Username: pdimov  
> Created at: 2019-04-06 22:47:48 UTC  
> Url: https://github.com/boostorg/variant2/issues/9#issuecomment-480543678  

This is the bug: https://gcc.gnu.org/bugzilla/show_bug.cgi?id=86356

---

## Comment 3

> Username: akrzemi1  
> Created at: 2019-04-07 00:04:25 UTC  
> Url: https://github.com/boostorg/variant2/issues/9#issuecomment-480547228  

I see. I'll leave it up to you to close this issue or not.
