# #2170 - How to ignore authentication key when calling with https url? [Closed]

> Username: leejaenggu  
> Created at: 2021-02-24 09:04:17 UTC  
> Updated at: 2022-01-09 05:17:17 UTC  
> Closed at: 2022-01-09 05:17:17 UTC  
> Url: https://github.com/boostorg/beast/issues/2170  

I am trying to communicate via https URL Post.  
However, if you use https, an error appears saying that the host cannot be found.  
I think it's a certificate issue.  
I want to do something like the -k or --insecure option, which is the certificate override in curl.  
What should I do?

---

## Comment 1

> Username: madmongo1  
> Created at: 2021-02-24 09:07:50 UTC  
> Url: https://github.com/boostorg/beast/issues/2170#issuecomment-784924750  

Not a lot of info here, but a common mistake is trying to resolve the entire url.  
You can’t do that as beast is a low level library.  
You have to connect to the host name/ port section of the url and then issue the request, setting the target field to be the query part of the url

---

## Comment 2

> Username: stale[bot]  
> Created at: 2021-05-29 16:11:00 UTC  
> Url: https://github.com/boostorg/beast/issues/2170#issuecomment-850857870  

This issue has been open for a while with no activity, has it been resolved?

---

## Comment 3

> Username: stale[bot]  
> Created at: 2022-01-09 05:16:52 UTC  
> Url: https://github.com/boostorg/beast/issues/2170#issuecomment-1008232388  

It looks like this issue has either been abandoned or resolved so I will go ahead and close it. Feel free to re-open this issue if you feel it needs attention, or open new issues as needed. Thanks!
