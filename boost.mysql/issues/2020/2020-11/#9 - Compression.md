# #9 - Compression [Open]

> Username: anarthal  
> Created at: 2020-11-29 17:03:48 UTC  
> Updated at: 2025-08-31 16:32:33 UTC  
> Url: https://github.com/boostorg/mysql/issues/9  

Support MySQL protocol's compression feature.

---

## Comment 1

> Username: bruno-viva  
> Created at: 2025-08-30 15:28:52 UTC  
> Url: https://github.com/boostorg/mysql/issues/9#issuecomment-3239344409  

+1! Enabling compression has saved me 30%+ bandwidth on average in some applications (of course at a CPU increase trade-off). Do you have a sense of how extensive the work to implement this would be? Thank you

---

## Comment 2

> Username: anarthal  
> Created at: 2025-08-31 16:32:33 UTC  
> Url: https://github.com/boostorg/mysql/issues/9#issuecomment-3240259707  

It's far from trivial, because the compression layer is not transparent to the application (it's a bit of a mess at the protocol level). I need to have a deeper look to see what's the interaction with pipelines. I'll put some work to this during these months.
