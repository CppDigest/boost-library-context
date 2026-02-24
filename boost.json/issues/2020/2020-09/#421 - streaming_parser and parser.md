# #421 - streaming_parser and parser [Closed]

> Username: vinniefalco  
> Created at: 2020-09-26 23:12:42 UTC  
> Updated at: 2020-09-29 01:41:48 UTC  
> Closed at: 2020-09-29 01:41:47 UTC  
> Url: https://github.com/boostorg/json/issues/421  

It would help to split `parser` out into `streaming_parser`. The first handles inputs where the entire JSON is contained in one buffer, the second handles the streaming case. This allows the regular `parser` to avoid emitting code to handle suspending and resuming, which produces quite a lot of executable size.

---

## Comment 1

> Username: vinniefalco  
> Created at: 2020-09-29 01:41:47 UTC  
> Url: https://github.com/boostorg/json/issues/421#issuecomment-700373940  

done
