# #516 - rfc7230 exists() [Closed]

> Username: vinniefalco  
> Created at: 2017-06-19 21:35:12 UTC  
> Updated at: 2022-10-11 17:42:28 UTC  
> Closed at: 2022-10-11 17:42:28 UTC  
> Url: https://github.com/boostorg/beast/issues/516  

We might want to rename these to something like `ifound`, `icount`, or `iexists` or otherwise use an identifier that communicates the case-insensitive nature of the comparison.

---

## Comment 1

> Username: klemens-morgenstern  
> Created at: 2022-10-11 16:32:46 UTC  
> Url: https://github.com/boostorg/beast/issues/516#issuecomment-1274970158  

This is a breaking change and the PR is 5 years old, is still open? Or should we just leave it as is?

---

## Comment 2

> Username: vinniefalco  
> Created at: 2022-10-11 16:42:31 UTC  
> Url: https://github.com/boostorg/beast/issues/516#issuecomment-1274983220  

sheeeit... close this stat

---

## Comment 3

> Username: vinniefalco  
> Created at: 2022-10-11 16:43:14 UTC  
> Updated at: 2022-10-11 16:43:47 UTC  
> Url: https://github.com/boostorg/beast/issues/516#issuecomment-1274984008  

I don't think its a good idea in the first place I mean `iexists()` wtf? That makes no sense. The javadoc just needs to state that field names are case insensitive, and that's the end of that
