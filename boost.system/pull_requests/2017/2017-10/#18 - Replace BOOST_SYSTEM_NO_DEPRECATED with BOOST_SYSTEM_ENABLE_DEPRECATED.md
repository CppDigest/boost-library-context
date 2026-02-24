# #18 Replace BOOST_SYSTEM_NO_DEPRECATED with BOOST_SYSTEM_ENABLE_DEPRECATED [Merged]

> Username: pdimov  
> Created at: 2017-10-21 16:21:30 UTC  
> Updated at: 2017-10-24 16:31:46 UTC  
> Merged at: 2017-10-22 21:12:20 UTC  
> Closed at: 2017-10-22 21:12:20 UTC  
> Url: https://github.com/boostorg/system/pull/18  



---

## Comment 1

> Username: pdimov  
> Created_at: 2017-10-22 18:05:31 UTC  
> Url: https://github.com/boostorg/system/pull/18#issuecomment-338496927  

Beman, can you please take a look at this and decide whether we want it in 1.66, as there are just a few days left.

---

## Comment 2

> Username: Beman  
> Created_at: 2017-10-22 20:48:08 UTC  
> Url: https://github.com/boostorg/system/pull/18#issuecomment-338507892  

On Sun, Oct 22, 2017 at 2:05 PM, Peter Dimov <notifications@github.com>  
wrote:  
  
> Beman, can you please take a look at this and decide whether we want it in  
> 1.66, as there are just a few days left.  
>  
I've been thinking about it on and off all day. Not because there is  
anything wrong with your P/R; go ahead and apply it.  
  
But for the longer term, C++ software developers need better tools for  
dealing with library changes over time. We need to be able to tell at  
compile time if a translation unit does certain things and have the compile  
time ability to issue warnings if the translation unit does something like  
calls deprecated functions. More about this as soon as the committee's  
premeeting mailing ships in a few days.  
  
--Beman

---

## Comment 3

> Username: pdimov  
> Created_at: 2017-10-22 21:12:09 UTC  
> Url: https://github.com/boostorg/system/pull/18#issuecomment-338509478  

OK, let's see how it goes.

---

## Comment 4

> Username: pdimov  
> Created_at: 2017-10-22 21:16:28 UTC  
> Url: https://github.com/boostorg/system/pull/18#issuecomment-338509770  

As a next step, I think that we need to consider routing `boost::error_code` and friends to `std::error_code` et al, if the compiler supports the latter. This can also potentially break code, of course. But "use `std::error_code` if it exists, otherwise `boost::error_code`" is something many libraries already do, and due to `error_code` being an interface type, this creates incompatibilities between libraries that choose one over the other.

---

## Comment 5

> Username: ned14  
> Created_at: 2017-10-22 21:52:10 UTC  
> Url: https://github.com/boostorg/system/pull/18#issuecomment-338512086  

The patchset looked good.  
  
I agree with the idea that `error_code`, `error_category` etc become mapped to the `std` editions when on C++ 11 or better. It allows all the header only libraries currently incorrectly using header only Boost.System to become correct if compiled on C++ 11 or better.  
  
One might consider announcing in the release notes that the next Boost will be the last to use the local implementation in C++ 11 or better.  
  
Also, one might consider also rerouting `boost::current_exception()` to `std::current_exception()`. The latter is surely always going to be equal or better to the Boost implementation.

---

## Comment 6

> Username: pdimov  
> Created_at: 2017-10-22 21:59:41 UTC  
> Url: https://github.com/boostorg/system/pull/18#issuecomment-338512548  

This last suggestion would be better placed in https://github.com/boostorg/exception. It didn't have issues enabled, but it does now. :-)

---
