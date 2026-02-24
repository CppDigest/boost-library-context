# #11 - BOOST_USE_VALGRIND? [Closed]

> Username: vinniefalco  
> Created at: 2017-07-31 19:27:04 UTC  
> Updated at: 2017-07-31 20:20:29 UTC  
> Closed at: 2017-07-31 19:36:51 UTC  
> Url: https://github.com/boostorg/coroutine2/issues/11  

The Coroutine2 docs state (doc/stack.qbk)  
  
>Running programs that switch stacks under valgrind causes problems. Property (b2 command-line) `valgrind=on` let valgrind treat the memory regions as stack space which suppresses the errors.  
  
However, I searched through the source code and I cannot find the text "valgrind" anywhere. Is this still a supported feature?  
  
Thanks

---

## Comment 1

> Username: olk  
> Created at: 2017-07-31 19:36:51 UTC  
> Url: https://github.com/boostorg/coroutine2/issues/11#issuecomment-319173311  

it is in boost.context (stack switching; valgrind has to keep track of stack switches)  
note that you have to apply BOOST_USE_VALGRIND too

---

## Comment 2

> Username: vinniefalco  
> Created at: 2017-07-31 19:56:28 UTC  
> Url: https://github.com/boostorg/coroutine2/issues/11#issuecomment-319178335  

Excellent, I found the code and I can see the feature rule in the jamfile - thanks!

---

## Comment 3

> Username: olk  
> Created at: 2017-07-31 19:58:24 UTC  
> Url: https://github.com/boostorg/coroutine2/issues/11#issuecomment-319178867  

hmm - please keep in mind that boost.coroutine is deprecated.  
boost.coroutine dublicates code from boost.context (see include/boost/coroutine/standard_stack_allocator.hpp).  
but it should work too :)

---

## Comment 4

> Username: vinniefalco  
> Created at: 2017-07-31 19:59:37 UTC  
> Url: https://github.com/boostorg/coroutine2/issues/11#issuecomment-319179175  

I don't use Boost.Coroutine directly, I use Boost.Asio. I hope that the deprecated Boost.Coroutine problem is resolved. To be honest, it was a bit of a mess, I think it is bad form to deprecate a Boost library that is currently in use by another Boost library, without coordinating with the author of the other library.

---

## Comment 5

> Username: olk  
> Created at: 2017-07-31 20:11:17 UTC  
> Url: https://github.com/boostorg/coroutine2/issues/11#issuecomment-319182348  

I've already posted a pull request (https://github.com/boostorg/asio/pull/52) that enables boost.coroutine2 in boost.asio - unfortunately not merged yet.

---

## Comment 6

> Username: vinniefalco  
> Created at: 2017-07-31 20:20:29 UTC  
> Url: https://github.com/boostorg/coroutine2/issues/11#issuecomment-319184701  

Ah, wonderful! The author informed me that there will be an update to Asio before 1.66.0, it was pending a freeze on Net-TS. I'm sure it will get merged then (I'll keep an eye on it).
