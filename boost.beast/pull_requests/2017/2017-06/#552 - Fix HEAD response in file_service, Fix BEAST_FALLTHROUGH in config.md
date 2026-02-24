# #552 Fix HEAD response in file_service, Fix BEAST_FALLTHROUGH in config [Closed]

> Username: octopus-prime  
> Created at: 2017-06-28 19:13:26 UTC  
> Updated at: 2017-06-28 20:13:57 UTC  
> Closed at: 2017-06-28 20:13:05 UTC  
> Url: https://github.com/boostorg/beast/pull/552  

```  
            // Make sure the file is there  
            if(! boost::filesystem::exists(full_path))  
            {  
                // Send a HEAD response  
                send(head(req, full_path));  
            }  
            else  
            {  
                // Send a Not Found result  
                send(not_found(req, rel_path));  
            }  
```  
  
==>  
  
```  
            // Make sure the file is there  
            if(boost::filesystem::exists(full_path))  
            {  
                // Send a HEAD response  
                send(head(req, full_path));  
            }  
            else  
            {  
                // Send a Not Found result  
                send(not_found(req, rel_path));  
            }  
```

---

## Comment 1

> Username: vinniefalco  
> Created_at: 2017-06-28 19:17:24 UTC  
> Url: https://github.com/boostorg/beast/pull/552#issuecomment-311760154  

That which is not tested...contains bugs!  
  
Please change the commit message to "Fix HEAD response in file_service" and add "* Fix HEAD response in file_service" to the CHANGELOG.md  
  
Thanks!

---

## Comment 2

> Username: octopus-prime  
> Created_at: 2017-06-28 19:47:13 UTC  
> Url: https://github.com/boostorg/beast/pull/552#issuecomment-311767348  

Added 2nd fix for BEAST_FALLTHROUGH in config.  
You remember `__attribute__` instead of `_attribute__`?  
  
;-)

---

## Comment 3

> Username: vinniefalco  
> Created_at: 2017-06-28 20:09:44 UTC  
> Url: https://github.com/boostorg/beast/pull/552#issuecomment-311774015  

AGAIN?!!!

---

## Comment 4

> Username: octopus-prime  
> Created_at: 2017-06-28 20:10:54 UTC  
> Url: https://github.com/boostorg/beast/pull/552#issuecomment-311774382  

The fix was not in v70...

---

## Comment 5

> Username: vinniefalco  
> Created_at: 2017-06-28 20:13:05 UTC  
> Url: https://github.com/boostorg/beast/pull/552#issuecomment-311775095  

I merged your changes to **v70**

---

## Comment 6

> Username: vinniefalco  
> Created_at: 2017-06-28 20:13:57 UTC  
> Url: https://github.com/boostorg/beast/pull/552#issuecomment-311775394  

>The fix was not in v70...  
  
What happened is I assumed that the bug was only on account of 7.0.1 being pre-release and I forgot about the _attribute__ misspelling, I threw out the commit with the fix.

---
