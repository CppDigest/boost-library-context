# #1021 Add field enum entry for "Access-Control-Expose-Headers" [Closed]

> Username: ivilata  
> Created at: 2018-02-15 11:02:01 UTC  
> Updated at: 2018-03-09 12:58:01 UTC  
> Closed at: 2018-03-09 12:55:14 UTC  
> Url: https://github.com/boostorg/beast/pull/1021  

This response header is described both in [CORS](https://www.w3.org/TR/cors/) and [Fetch](https://fetch.spec.whatwg.org/) specifications.

---

## Comment 1

> Username: vinniefalco  
> Created_at: 2018-02-15 11:54:34 UTC  
> Updated_at: 2018-02-15 11:55:19 UTC  
> Url: https://github.com/boostorg/beast/pull/1021#issuecomment-365905550  

I'm always glad to add more fields but note that the source code is generated from this script:  
https://github.com/boostorg/beast/blob/develop/tools/make_field.sh  
  
Which uses this file as input:  
https://github.com/boostorg/beast/blob/develop/tools/field.txt  
  
If I run the script again, your change would be lost. I think you should also add it to field.txt and test it, if you wouldn't mind - thanks!  
  
And one more thing, as long as we're adding fields we should add any more that are missing (if there are any more) so that we do it all at once.

---

## Comment 2

> Username: ivilata  
> Created_at: 2018-03-09 12:55:14 UTC  
> Url: https://github.com/boostorg/beast/pull/1021#issuecomment-371804922  

Oh sorry, I wasn't aware about that generation step. I'll create a different PR following that approach.  
  
Thanks!

---
