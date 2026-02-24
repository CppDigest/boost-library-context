# #2 - Number of parameters for BOOST_DESCRIBE_CLASS and BOOST_DESCRIBE_STRUCT [Closed]

> Username: eldiener  
> Created at: 2021-02-24 17:48:21 UTC  
> Updated at: 2021-03-05 02:31:04 UTC  
> Closed at: 2021-03-05 02:31:04 UTC  
> Url: https://github.com/boostorg/describe/issues/2  

You should be able to leave out the empty parentheses parameter completely for no base classes in the BOOST_DESCRIBE_CLASS and BOOST_DESCRIBE_STRUCT macros. OK, you do not want to rely on Boost PP and BOOST_PP_OVERLOAD, but you can steal the code for that from Boost PP and adjust your macro depending on the number of arguments passed to it. I think it would be more elegant. I assume that all compilers support variadic macros by now and all you need for your own internal version of BOOST_PP_OVERLOAD is variadic macro support.

---

## Comment 1

> Username: eldiener  
> Created at: 2021-03-05 02:31:04 UTC  
> Url: https://github.com/boostorg/describe/issues/2#issuecomment-791100835  

I will close this because if you leave out empty parentheses completely you would not know if this means empty base classes or empty members.
