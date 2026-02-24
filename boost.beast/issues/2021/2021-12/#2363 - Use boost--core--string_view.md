# #2363 - Use boost::core::string_view [Closed]

> Username: vinniefalco  
> Created at: 2021-12-16 20:07:18 UTC  
> Updated at: 2023-09-06 17:04:16 UTC  
> Closed at: 2022-10-03 05:29:22 UTC  
> Url: https://github.com/boostorg/beast/issues/2363  

We need to deprecate `BOOST_BEAST_USE_STD_STRING_VIEW` and change the type to use `boost::core::string_view`

---

## Comment 1

> Username: vinniefalco  
> Created at: 2022-06-10 17:08:47 UTC  
> Url: https://github.com/boostorg/beast/issues/2363#issuecomment-1152568402  

isn't this done?

---

## Comment 2

> Username: sehe  
> Created at: 2022-06-10 21:28:04 UTC  
> Updated at: 2022-06-15 13:33:02 UTC  
> Url: https://github.com/boostorg/beast/issues/2363#issuecomment-1152750362  

@vinniefalco   
  
Only partially. We [replaced `boost::string_view` with `boost::core::string_view`](https://github.com/boostorg/beast/issues/2417).  
  
I wasn't aware of a desire to also get rid of `BEAST_USE_STD_STRING_VIEW` - for which I never did impact analysis.  
  
Checking back in history suggests that is no longer a realistic desire to support standalone ASIO. Therefore, I so no immediate reason to avoid tie ourselves to Boost Core string_view. In terms of compatibility it is already the Rosetta Stone.  
  
It seems a little impact analysis is all that's needed here.

---

## Comment 3

> Username: eiderdaus  
> Created at: 2023-09-06 17:04:16 UTC  
> Url: https://github.com/boostorg/beast/issues/2363#issuecomment-1708777679  

The message `"BOOST_BEAST_USE_STD_STRING_VIEW is deprecated, use BOOST_NO_CXX17_HDR_STRING_VIEW instead"` does not make much sense. Defining or undefining `BOOST_NO_CXX17_HDR_STRING_VIEW` will not bring back `BOOST_BEAST_USE_STD_STRING_VIEW`'s behavior which has been removed obviously. Also it is my understanding whether `BOOST_NO_CXX17_HDR_STRING_VIEW` is defined should be determined by Boost's build system and nothing a user should tamper with. A better message would be `"BOOST_BEAST_USE_STD_STRING_VIEW does not work anymore, stop using it"`.
