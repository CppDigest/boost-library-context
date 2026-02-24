# #675 - Deferred Body Type Example inconsistencies [Closed]

> Username: eliottparis  
> Created at: 2017-07-25 10:49:06 UTC  
> Updated at: 2017-07-25 15:48:56 UTC  
> Closed at: 2017-07-25 15:48:56 UTC  
> Url: https://github.com/boostorg/beast/issues/675  

In the [example_http_defer_body](https://github.com/vinniefalco/beast/blob/develop/example/doc/http_examples.hpp#L776), it say:  
  
> Handle a form PUT request, choosing a body type depending on the Content-Type [...].  
  
Shouldn't it be replaced by : *Handle a form **POST** request, [...]*, as the Content-Type is only checked on `http::verb::post` case [here](https://github.com/vinniefalco/beast/blob/develop/example/doc/http_examples.hpp#L820) ?   
  
I also think that the `do_string_body` `goto` statement [here](https://github.com/vinniefalco/beast/blob/develop/example/doc/http_examples.hpp#L825) should be renamed as `do_dynamic_body`.

---

## Comment 1

> Username: vinniefalco  
> Created at: 2017-07-25 11:48:44 UTC  
> Url: https://github.com/boostorg/beast/issues/675#issuecomment-317713309  

Yep, I agree with all of that, thanks! Do you want to submit a pull request or should I just do it?
