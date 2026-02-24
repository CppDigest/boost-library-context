# #1166 - BodyReader constructed before header is complete [Closed]

> Username: djarek  
> Created at: 2018-06-18 18:11:05 UTC  
> Updated at: 2022-06-16 16:34:24 UTC  
> Closed at: 2022-06-16 16:34:23 UTC  
> Url: https://github.com/boostorg/beast/issues/1166  

In order to enable BodyReaders that inspect header content, `parser` should only construct it once the header is complete.

---

## Comment 1

> Username: vinniefalco  
> Created at: 2022-06-16 16:34:23 UTC  
> Url: https://github.com/boostorg/beast/issues/1166#issuecomment-1157884715  

Body concept was a mistake
