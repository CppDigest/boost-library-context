# #25 - feature request: throw_exception customization point [Closed]

> Username: zlojvavan  
> Created at: 2019-09-11 15:04:05 UTC  
> Updated at: 2020-02-18 07:04:53 UTC  
> Closed at: 2020-02-18 02:54:20 UTC  
> Url: https://github.com/boostorg/exception/issues/25  

what about introducing end user customization point facility? at the moment one can specialize throw_exception template using any specific type but providing specialization for the entire class hierarchy looks somewhat problematic

---

## Comment 1

> Username: zajo  
> Created at: 2019-10-15 20:39:10 UTC  
> Url: https://github.com/boostorg/exception/issues/25#issuecomment-542394528  

What customization do you need? The only reason you'd call `throw_exception` rather than just use `throw` is to support `BOOST_NO_EXCEPTIONS` configurations. It is always illegal to return from `throw_exception`.

---

## Comment 2

> Username: zlojvavan  
> Created at: 2019-10-16 06:42:53 UTC  
> Url: https://github.com/boostorg/exception/issues/25#issuecomment-542546855  

I'm not talking about returning from throw_exception  
for example one may want to replace exception instance with another class or just "enrich" it with say callstack info, etc.

---

## Comment 3

> Username: zajo  
> Created at: 2020-02-18 02:54:20 UTC  
> Url: https://github.com/boostorg/exception/issues/25#issuecomment-587249331  

Like I said, `throw_exception` is not a customization point. When a library author wants to throw an exception, it shouldn't be up to the user to change the exception type. It exists solely to allow Boost to be used under `-fno-exceptions`.

---

## Comment 4

> Username: zlojvavan  
> Created at: 2020-02-18 07:04:53 UTC  
> Url: https://github.com/boostorg/exception/issues/25#issuecomment-587312449  

it's not and that's why I filed request as it's better be that customization point. changing exception type is somewhat extreme example (though if the type is derived from the thrown type it won't really hurt) but enriching it by simple replacing text or adding extra info such as say call stack as demonstrated in BOOST_THROW_EXCEPTION macro and/or in https://www.boost.org/doc/libs/1_72_0/doc/html/stacktrace/getting_started.html#stacktrace.getting_started.exceptions_with_stacktrace is very useful feature
