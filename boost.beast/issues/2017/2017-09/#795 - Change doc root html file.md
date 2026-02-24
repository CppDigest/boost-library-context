# #795 - Change doc root html file [Closed]

> Username: vinniefalco  
> Created at: 2017-09-24 15:55:33 UTC  
> Updated at: 2017-09-29 13:22:46 UTC  
> Closed at: 2017-09-29 13:22:46 UTC  
> Url: https://github.com/boostorg/beast/issues/795  

Remove 'root.filename=beast' from the doc Jamfile and modify the redirect to point to the new location (minus the index.html)

---

## Comment 1

> Username: danieljames  
> Created at: 2017-09-24 16:41:55 UTC  
> Url: https://github.com/boostorg/beast/issues/795#issuecomment-331722447  

You need to keep the index.html so that a local copy will work.
