# #473 - "Access-Control-Allow-Origin" header missing in field.hpp [Closed]

> Username: niklas88  
> Created at: 2017-06-12 16:50:49 UTC  
> Updated at: 2017-06-14 00:38:21 UTC  
> Closed at: 2017-06-14 00:38:21 UTC  
> Url: https://github.com/boostorg/beast/issues/473  

This header is needed for cross domain requests, a good reference can be found [here](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/Access-Control-Allow-Origin)

---

## Comment 1

> Username: vinniefalco  
> Created at: 2017-06-12 17:49:03 UTC  
> Url: https://github.com/boostorg/beast/issues/473#issuecomment-307865170  

Should we just add everything in the "Provisional Message Header Field Names" from https://www.iana.org/assignments/message-headers/message-headers.xhtml ?  
  
Would you like to try your hand at adding them? Just append the list from "https://www.iana.org/assignments/message-headers/message-headers.xhtml" to this file:  
https://github.com/vinniefalco/Beast/blob/master/scripts/field.txt  
  
Then run this nifty script:  
https://github.com/vinniefalco/Beast/blob/master/scripts/make_field.sh  
  
To produce the three chunks of source code (field.hpp, field.ipp, and field.cpp). You'll have to manually adjust the size of the array in the header file  
  
Great way to get your name in the commit log :) :)   
  
Or I can do it, either way works.

---

## Comment 2

> Username: vinniefalco  
> Created at: 2017-06-12 22:27:46 UTC  
> Url: https://github.com/boostorg/beast/issues/473#issuecomment-307951782  

I did it. This will go into version 56

---

## Comment 3

> Username: niklas88  
> Created at: 2017-06-13 07:23:29 UTC  
> Url: https://github.com/boostorg/beast/issues/473#issuecomment-308030306  

Thanks 👍 I'm in Europe and when I wrote the issue I had already been working for quite some time.
