# #38 - Macro vanished, but still documented [Closed]

> Username: ChristofKaufmann  
> Created at: 2021-02-13 22:45:56 UTC  
> Updated at: 2021-10-06 01:35:59 UTC  
> Closed at: 2021-10-06 01:35:59 UTC  
> Url: https://github.com/boostorg/exception/issues/38  

I got a compile error with an updated boost version about an undefined macro, namely `BOOST_THROW_EXCEPTION_CURRENT_FUNCTION`. I could resolve it by replacing it by `BOOST_CURRENT_FUNCTION`. However, it is still documented in [Configuration Macros](https://www.boost.org/doc/libs/1_75_0/libs/exception/doc/configuration_macros.html). So is this a documentation error or has it been removed accidentally?

---

## Comment 1

> Username: zajo  
> Created at: 2021-02-15 02:26:05 UTC  
> Url: https://github.com/boostorg/exception/issues/38#issuecomment-778898937  

Yeah, the macro does not exist now. I'll remove it from the documentation, but it really wasn't something you'd use, it was something you'd define for BOOST_THROW_EXCEPTION to use.  
  
When Boost was modularized, `boost::throw_exception` was separated in its own git submodule, thus making it a separate library from Boost Exception. It now has its own [documentation](https://www.boost.org/doc/libs/release/libs/throw_exception/doc/html/throw_exception.html), which I recommend you read. I should put a link in Boost Exception so there is no confusion.

---

## Comment 2

> Username: ChristofKaufmann  
> Created at: 2021-02-15 06:51:58 UTC  
> Url: https://github.com/boostorg/exception/issues/38#issuecomment-778993028  

Thanks for clarification! I'll leave this open for you as a reminder to remove it the documentation and put the link up.
