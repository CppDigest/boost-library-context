# #179 Fix build failure with gcc 4.9.1 [Closed]

> Username: tntljc  
> Created at: 2019-08-02 11:18:37 UTC  
> Updated at: 2023-05-08 00:30:24 UTC  
> Closed at: 2023-05-08 00:30:23 UTC  
> Url: https://github.com/boostorg/graph/pull/179  

- This is a gcc bug existing in 4.9.1 but it was fixed in 4.9.3. See comipler bug link https://gcc.gnu.org/bugzilla/show_bug.cgi?id=62255#c2 for details.

---

## Comment 1

> Username: anadon  
> Created_at: 2019-08-07 03:51:58 UTC  
> Url: https://github.com/boostorg/graph/pull/179#issuecomment-518928841  

I'm overhauling CI right now, so I'd like to put this merge on hold.

---

## Comment 2

> Username: jeremy-murphy  
> Created_at: 2023-05-08 00:30:23 UTC  
> Url: https://github.com/boostorg/graph/pull/179#issuecomment-1537585211  

If it was fixed in 4.9.3 then I would say don't use anything less than that.

---
