# #2474 - Add json_body example [Closed]

> Username: sehe  
> Created at: 2022-06-26 14:08:21 UTC  
> Updated at: 2023-04-21 11:10:19 UTC  
> Closed at: 2023-04-21 11:10:18 UTC  
> Url: https://github.com/boostorg/beast/issues/2474  

We need a json_body sample implementation to elaborate the sketch in https://www.boost.org/doc/libs/develop/libs/beast/doc/html/beast/using_http/custom_body_types.html#beast.using_http.custom_body_types.value_type  
  
```c++  
#include <boost/json/stream_parser.hpp>  
  
struct Body  
{  
    using value_type = boost::json::value;  
  
    class reader;  
    class writer;  
};```

---

## Comment 1

> Username: sehe  
> Created at: 2022-06-26 14:08:58 UTC  
> Url: https://github.com/boostorg/beast/issues/2474#issuecomment-1166544468  

There's already work going on here https://github.com/boostorg/beast/pull/2381

---

## Comment 2

> Username: klemens-morgenstern  
> Created at: 2022-09-24 04:31:46 UTC  
> Url: https://github.com/boostorg/beast/issues/2474#issuecomment-1256853680  

See also #2511.   
  
Can we close this, so we don't have three places where json_body work happens?

---

## Comment 3

> Username: sehe  
> Created at: 2023-04-21 11:10:18 UTC  
> Url: https://github.com/boostorg/beast/issues/2474#issuecomment-1517665848  

@klemens-morgenstern  I guess :) I didn't start the third place, although I like that you named the example folder "body" to allow for the other examples to fit there
