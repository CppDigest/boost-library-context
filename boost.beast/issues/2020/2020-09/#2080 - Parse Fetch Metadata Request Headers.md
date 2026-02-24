# #2080 - Parse Fetch Metadata Request Headers [Closed]

> Username: bigvzhang  
> Created at: 2020-09-05 11:22:02 UTC  
> Updated at: 2020-09-14 06:58:31 UTC  
> Closed at: 2020-09-14 06:58:31 UTC  
> Url: https://github.com/boostorg/beast/issues/2080  

### Version of 189  
  
How do I parse Fetch Metadata Request Headers?  
  
I added  the following  lines  to the `beast/http/field.hpp`, but I still cannot get its value through `req[http::field::sec_fetch_mode]`  
 ```  
    sec_fetch_dest,  
    sec_fetch_mode,  
    sec_fetch_site,  
    sec_fetch_user,  
```  
How should I do?

---

## Comment 1

> Username: vinniefalco  
> Created at: 2020-09-05 11:46:11 UTC  
> Url: https://github.com/boostorg/beast/issues/2080#issuecomment-687598080  

you can't be adding lines to field.hpp. Just use strings as keys, e.g. `req["Sec-Fetch-Dest"]`.

---

## Comment 2

> Username: bigvzhang  
> Created at: 2020-09-05 14:44:02 UTC  
> Url: https://github.com/boostorg/beast/issues/2080#issuecomment-687620626  

Thanks a lot. The problem is fixed. By the way, is it possible to add such fields in the future?

---

## Comment 3

> Username: vinniefalco  
> Created at: 2020-09-05 14:46:58 UTC  
> Url: https://github.com/boostorg/beast/issues/2080#issuecomment-687620954  

> is it possible to add such fields in the future?  
  
Yes, it is. They are added here:  
https://github.com/boostorg/beast/blob/4eb7d06d5d5b2b73c681f1a161cc50be697e8197/tools/field.txt  
  
And then this script is used to generate the source code snippets:  
https://github.com/boostorg/beast/blob/4eb7d06d5d5b2b73c681f1a161cc50be697e8197/tools/make_field.sh

---

## Comment 4

> Username: bigvzhang  
> Created at: 2020-09-06 06:21:49 UTC  
> Url: https://github.com/boostorg/beast/issues/2080#issuecomment-687708541  

It's fine. Thanks a lot!
