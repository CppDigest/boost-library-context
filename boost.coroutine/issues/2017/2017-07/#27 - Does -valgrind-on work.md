# #27 - Does <valgrind>on work? [Closed]

> Username: vinniefalco  
> Created at: 2017-07-31 19:33:17 UTC  
> Updated at: 2017-07-31 19:53:40 UTC  
> Closed at: 2017-07-31 19:52:35 UTC  
> Url: https://github.com/boostorg/coroutine/issues/27  

The documentation alludes to a bjam property `<valgrind>on` to enable `BOOST_USE_VALGRIND`. However, I have searched through tools/build and libs/coroutine and I cannot find any jam code or other code that implements this property. Is this a supported feature? If so, where is the code to support this feature? If not, could we perhaps update the documentation?

---

## Comment 1

> Username: olk  
> Created at: 2017-07-31 19:52:34 UTC  
> Updated at: 2017-07-31 19:53:40 UTC  
> Url: https://github.com/boostorg/coroutine/issues/27#issuecomment-319177302  

it is in boost.context, not in boost.coroutine/boost.coroutine2/boost.fiber  
for instance take a look at fixedsize_stack.hpp in boost.context
