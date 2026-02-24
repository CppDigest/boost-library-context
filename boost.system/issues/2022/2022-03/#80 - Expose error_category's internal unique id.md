# #80 - Expose error_category's internal unique id [Open]

> Username: ned14  
> Created at: 2022-03-28 16:55:48 UTC  
> Updated at: 2022-11-17 23:12:10 UTC  
> Url: https://github.com/boostorg/system/issues/80  

For https://github.com/ned14/status-code/blob/master/include/boost_error_code.hpp#L109 I'd like to be able to access `error_category`'s internal unique id.  
  
For https://github.com/ned14/status-code/blob/master/include/boost_error_code.hpp#L101 I'd like the ability to get some singleton `error_category&` for a specific `error_category` unique id. Then unique category id's can always be turned into an error category singleton, which makes exposing the unique id more widely useful.

---

## Comment 1

> Username: ned14  
> Created at: 2022-11-17 19:35:36 UTC  
> Url: https://github.com/boostorg/system/issues/80#issuecomment-1319109360  

We ran into a bug in the work codebase related to this today.  
  
Code was using a default constructed `std::error_code` to mean "success", but surrounding code was passing that to Boost.ASIO which converted it to `boost::system::error_code`. It would seem that `boost::system::error_code` does not map a default constructed `std::error_code` into a default constructed `boost::system::error_code`, but rather wraps it. Later on, ASIO's error code reached Outcome.Experimental whose `status_code` then proceeded to wrap the `boost::system::error_code`. So, now we have a status code wrapping a boost error code which wraps a std error code :)  
  
Unfortunately, status_code's wrapper of boost error code was written before you added a set of flags to denote if the boost error code is wrapping a std error code. The added payload is a big problem for status code, because we'd really like to be able to squirrel a boost error code into an `error`, which is two pointers sized, and so can't erase a boost error code if it has added state.  
  
It seems to me that the added state in boost error code is only used for wrapping std error codes, so if I could peek into boost error code sufficiently that I can see if boost is wrapping std, then status code can wrap the std error code directly, and then status code's wrap of boost error code can safely drop the added state and remain layout erase compatible with `error`.  
  
Any chance of addressing this soon?

---

## Comment 2

> Username: pdimov  
> Created at: 2022-11-17 19:45:59 UTC  
> Url: https://github.com/boostorg/system/issues/80#issuecomment-1319119623  

You can detect (at present) whether `boost::system::error_code ec` stores a `std::error_code` by `ec.category() == boost::system::detail::interop_category()` but the far simpler option is to just always convert `boost::system::error_code` to `std::error_code` and wrap that instead.  
  
> It seems to me that the added state in boost error code is only used for wrapping std error codes  
  
This used to be true, but it now stores a pointer to a source location associated with the error.

---

## Comment 3

> Username: ned14  
> Created at: 2022-11-17 20:10:13 UTC  
> Url: https://github.com/boostorg/system/issues/80#issuecomment-1319145156  

> the far simpler option is to just always convert boost::system::error_code to std::error_code and wrap that instead.  
  
Since which version of Boost?  
  
> This used to be true, but it now stores a pointer to a source location associated with the error.  
  
Surely safe to drop for status code's purposes?

---

## Comment 4

> Username: pdimov  
> Created at: 2022-11-17 20:20:33 UTC  
> Url: https://github.com/boostorg/system/issues/80#issuecomment-1319155505  

`boost::system::error_code` is convertible to `std::error_code` since Boost 1.65, but this conversion is only enabled when `<system_error>` exists and some other conditions are met that vary from version to version (e.g. the current release also needs `<atomic>` and `<mutex>`.)  
  
I think that the macro `BOOST_SYSTEM_HAS_SYSTEM_ERROR` is always defined if there's such conversion, but I'm not 100% sure whether this is true for all versions back to 1.65.

---

## Comment 5

> Username: ned14  
> Created at: 2022-11-17 21:34:22 UTC  
> Url: https://github.com/boostorg/system/issues/80#issuecomment-1319233908  

Thanks for the info. I think I have a solution for the immediate issue based on your help.  
  
I'd still like access to those internal unique id's and the ability to look up singleton error categories.

---

## Comment 6

> Username: pdimov  
> Created at: 2022-11-17 23:12:10 UTC  
> Url: https://github.com/boostorg/system/issues/80#issuecomment-1319329828  

I don't think that the ability to get a singleton by ID will ever be implemented. There's no central registry for these, and no way to e.g. link them up at construction time because the categories are `constexpr` so no code runs when they are constructed.  
  
For your first request, I remember that when I actually looked at what you want it for, I concluded that it won't solve your problem, but the link is dead now, so I can't check.
