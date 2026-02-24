# #303 - Generate compile error when decorators aren't declared correctly [Closed]

> Username: vinniefalco  
> Created at: 2017-04-15 02:29:39 UTC  
> Updated at: 2017-05-08 00:03:59 UTC  
> Closed at: 2017-05-08 00:03:59 UTC  
> Url: https://github.com/boostorg/beast/issues/303  

When a decorator is provided to `set_option`, Beast should require that both versions of `operator()` (one for requests, one for responses) are invokable correctly, otherwise generate a compiler error. Otherwise Beast will silently ignore user-provided decorators if the user made a mistake in the declaration, causing confusion.

---

## Comment 1

> Username: vinniefalco  
> Created at: 2017-04-24 17:21:10 UTC  
> Url: https://github.com/boostorg/beast/issues/303#issuecomment-296755289  

See #317
