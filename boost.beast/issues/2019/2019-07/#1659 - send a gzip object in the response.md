# #1659 - send a gzip object in the response [Closed]

> Username: oAly  
> Created at: 2019-07-17 04:22:36 UTC  
> Updated at: 2019-08-23 09:31:36 UTC  
> Closed at: 2019-08-23 09:31:36 UTC  
> Url: https://github.com/boostorg/beast/issues/1659  

Hey there,  
  
Is it possible to send a gzipped object in the response using boost beast?  
  
I am using Beast 1.69  
Thank you

---

## Comment 1

> Username: vinniefalco  
> Created at: 2019-07-17 04:32:52 UTC  
> Url: https://github.com/boostorg/beast/issues/1659#issuecomment-512097939  

Yes you can send whatever you want.

---

## Comment 2

> Username: oAly  
> Created at: 2019-07-17 04:37:50 UTC  
> Url: https://github.com/boostorg/beast/issues/1659#issuecomment-512098740  

what body_type would I use for the response in that case?

---

## Comment 3

> Username: vinniefalco  
> Created at: 2019-07-17 07:03:46 UTC  
> Url: https://github.com/boostorg/beast/issues/1659#issuecomment-512130757  

`string_body` will work

---

## Comment 4

> Username: stale[bot]  
> Created at: 2019-08-16 07:47:50 UTC  
> Url: https://github.com/boostorg/beast/issues/1659#issuecomment-521920211  

This issue has been open for a while with no activity, has it been resolved?

---

## Comment 5

> Username: stale[bot]  
> Created at: 2019-08-23 09:31:35 UTC  
> Url: https://github.com/boostorg/beast/issues/1659#issuecomment-524245752  

It looks like this issue has either been abandoned or resolved so I will go ahead and close it. Feel free to re-open this issue if you feel it needs attention, or open new issues as needed. Thanks!
