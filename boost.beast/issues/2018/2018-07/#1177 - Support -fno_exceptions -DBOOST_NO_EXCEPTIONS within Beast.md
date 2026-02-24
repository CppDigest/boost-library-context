# #1177 - Support -fno_exceptions -DBOOST_NO_EXCEPTIONS within Beast [Closed]

> Username: azuretheseeker  
> Created at: 2018-07-03 20:27:19 UTC  
> Updated at: 2024-06-06 05:34:19 UTC  
> Closed at: 2023-12-21 05:26:35 UTC  
> Url: https://github.com/boostorg/beast/issues/1177  

Beast currently does not support compilation with:  
-fno_exceptions  
even when the following are defined:  
-DBOOST_NO_EXCEPTIONS  
-DBOOST_EXCEPTION_DISABLE  
  
read.ipp has multiple try/catch clauses that do not respect this configuration.  
  
As per all boost libraries, one of the goals is to allow the user to define their own error handling mechanism, and enabling exceptions still degrades performance in real-time fault-tolerant applications. Suggestion is to identify all uses of try/catch in the library and make them obey configuration directives.

---

## Comment 1

> Username: vinniefalco  
> Created at: 2018-07-03 21:43:57 UTC  
> Updated at: 2018-07-03 21:44:07 UTC  
> Url: https://github.com/boostorg/beast/issues/1177#issuecomment-402300713  

This will help  
```  
namespace detail {  
template<class DynamicBuffer>  
bool dynamic_prepare(  
    boost::optional<typename DynamicBuffer::const_buffers_type>& buffer,  
    std::size_t amount);  
} // detail  
```

---

## Comment 2

> Username: djarek  
> Created at: 2018-07-03 22:03:36 UTC  
> Updated at: 2018-07-03 22:03:46 UTC  
> Url: https://github.com/boostorg/beast/issues/1177#issuecomment-402305261  

Note that according to https://www.boost.org/doc/libs/develop/libs/beast/doc/html/beast/concepts/DynamicBuffer.html  
  
DynamicBuffers are required to throw `length_error` if `length_error if size() + n` exceeds `max_size()`, so Beast could just check this condition before calling prepare, thus avoiding the need for the try catch block.

---

## Comment 3

> Username: vinniefalco  
> Created at: 2018-07-03 22:05:32 UTC  
> Url: https://github.com/boostorg/beast/issues/1177#issuecomment-402305673  

We still need the try/catch block because we want to convert `length_error` into an `error_code`. But this try/catch can be moved into a utility function (e.g. `dynamic_prepare` above) and then conditionally compiled based on `BOOST_NO_EXCEPTIONS` and `BOOST_EXCEPTIONS_DISABLE`. In addition, we can perform the length check you described, and return `false` if the check fails.

---

## Comment 4

> Username: azuretheseeker  
> Created at: 2018-07-13 01:25:27 UTC  
> Updated at: 2018-07-13 01:25:41 UTC  
> Url: https://github.com/boostorg/beast/issues/1177#issuecomment-404697932  

The combination of these ideas is very good and will move me forward in development right now. Thank you for your help and great work in this library. One question: will this change make it to the next official boost release? In my org environment, I can't include custom library code for release-grade build binaries very easily due to existing dev ops policies.

---

## Comment 5

> Username: vinniefalco  
> Created at: 2018-07-13 01:27:55 UTC  
> Url: https://github.com/boostorg/beast/issues/1177#issuecomment-404698284  

The next release is very soon, and this change might not be accepted by the release managers. However, I will ask - thanks!

---

## Comment 6

> Username: vinniefalco  
> Created at: 2018-10-09 21:46:11 UTC  
> Url: https://github.com/boostorg/beast/issues/1177#issuecomment-428364144  

Working on this now

---

## Comment 7

> Username: vinniefalco  
> Created at: 2018-10-10 02:48:01 UTC  
> Url: https://github.com/boostorg/beast/issues/1177#issuecomment-428419747  

@djarek how do we handle this:  
https://github.com/boostorg/beast/blob/ec1c8ada7a22b274cf6e58c019c2bbe6965f6232/include/boost/beast/http/string_body.hpp#L100

---

## Comment 8

> Username: djarek  
> Created at: 2018-10-10 06:42:08 UTC  
> Url: https://github.com/boostorg/beast/issues/1177#issuecomment-428456206  

```  
BOOST_TRY  
{  
  // ...  
}  
BOOST_CATCH(std::exception const&)  
{  
  // ...  
} BOOST_CATCH_END  
```  
  
Catching all exceptions may be a bit too overzealous here, I think the only one that should be handled is `std::bad_alloc`.

---

## Comment 9

> Username: altf4  
> Created at: 2021-03-02 15:47:05 UTC  
> Url: https://github.com/boostorg/beast/issues/1177#issuecomment-789005232  

Hi, I wanted to check in on the status of this. Can beast (specifically the websockets) be used with -fno-exceptions today? Sorry if this is in documentation somewhere, I couldn't find it. Thanks!

---

## Comment 10

> Username: madmongo1  
> Created at: 2021-03-02 17:55:21 UTC  
> Url: https://github.com/boostorg/beast/issues/1177#issuecomment-789095291  

Not supported at present, but thanks for commenting on this issue. It's from before my time but I'll resurrect it.

---

## Comment 11

> Username: vinniefalco  
> Created at: 2021-03-02 18:09:17 UTC  
> Url: https://github.com/boostorg/beast/issues/1177#issuecomment-789104778  

I don't see this ever happening in Beast

---

## Comment 12

> Username: klemens-morgenstern  
> Created at: 2022-09-24 06:14:30 UTC  
> Url: https://github.com/boostorg/beast/issues/1177#issuecomment-1256877379  

Vinnie, does that mean the issue is rejected?

---

## Comment 13

> Username: vinniefalco  
> Created at: 2022-09-25 00:25:22 UTC  
> Url: https://github.com/boostorg/beast/issues/1177#issuecomment-1257089834  

Does asio work without exceptions?

---

## Comment 14

> Username: Kyle-Thompson  
> Created at: 2023-12-20 05:51:45 UTC  
> Url: https://github.com/boostorg/beast/issues/1177#issuecomment-1863895358  

This issue is still marked as open despite #2611 being merged with the description saying it closes this issue. Is this still considered open?

---

## Comment 15

> Username: ashtum  
> Created at: 2023-12-20 06:35:37 UTC  
> Updated at: 2023-12-20 06:36:02 UTC  
> Url: https://github.com/boostorg/beast/issues/1177#issuecomment-1863929535  

> This issue is still marked as open despite #2611 being merged with the description saying it closes this issue. Is this still considered open?  
  
I believe the issue has been resolved, and we can now use Beast with `-fno-exceptions`. Are you experiencing any issues?

---

## Comment 16

> Username: Kyle-Thompson  
> Created at: 2023-12-20 17:53:53 UTC  
> Url: https://github.com/boostorg/beast/issues/1177#issuecomment-1864896325  

I have not yet used the library, I'm looking at a possible replacement to what I'm currently using and `-fno-exceptions` is a requirement so I wanted to confirm that this issue is resolved despite being still listed as open. Thanks for the quick response.

---

## Comment 17

> Username: ashtum  
> Created at: 2023-12-20 17:57:30 UTC  
> Url: https://github.com/boostorg/beast/issues/1177#issuecomment-1864900712  

I believe so (and did some tests). Could you confirm, @klemens-morgenstern?

---

## Comment 18

> Username: klemens-morgenstern  
> Created at: 2023-12-20 21:30:54 UTC  
> Url: https://github.com/boostorg/beast/issues/1177#issuecomment-1865168512  

I think so.
