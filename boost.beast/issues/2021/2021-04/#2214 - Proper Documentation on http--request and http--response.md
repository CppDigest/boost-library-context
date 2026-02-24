# #2214 - Proper Documentation on http::request and http::response [Open]

> Username: xhimanshuz  
> Created at: 2021-04-15 03:58:01 UTC  
> Updated at: 2024-10-16 06:17:26 UTC  
> Url: https://github.com/boostorg/beast/issues/2214  

In boost Beast documentation nowhere mentioning how to properly create the Request and Response object.   
Boost Beast Reference page is missing some basic things, So, I have to watch all your great talk to make me motivated to use Beast even if these are too great and knowledgeable, can't cover all the Beast features. I really like how fast you try to cover the content in a limited time.  
  
As for reference, these are the reference page for the Response, Request class, and file_body class.   
https://www.boost.org/doc/libs/1_68_0/libs/beast/doc/html/beast/ref/boost__beast__http__request.html  
https://www.boost.org/doc/libs/1_68_0/libs/beast/doc/html/beast/ref/boost__beast__http__response.html   
https://www.boost.org/doc/libs/1_68_0/libs/beast/doc/html/beast/ref/boost__beast__http__file_body.html   
  
**There should be some example at the end of all references, So People get to know how to use them.**  
I know it's time-consuming but please cover at least basic concepts So, New people like me at least know how to use basic functionality. I have read example Its great still haven't mentioned how to create request and response object.

---

## Comment 1

> Username: stale[bot]  
> Created at: 2021-05-29 16:10:53 UTC  
> Url: https://github.com/boostorg/beast/issues/2214#issuecomment-850857848  

This issue has been open for a while with no activity, has it been resolved?

---

## Comment 2

> Username: xhimanshuz  
> Created at: 2021-05-29 23:58:51 UTC  
> Url: https://github.com/boostorg/beast/issues/2214#issuecomment-850916187  

No

---

## Comment 3

> Username: vinniefalco  
> Created at: 2021-07-19 17:47:24 UTC  
> Url: https://github.com/boostorg/beast/issues/2214#issuecomment-882738843  

What's going on with this? @madmongo1

---

## Comment 4

> Username: stale[bot]  
> Created at: 2022-01-09 03:16:57 UTC  
> Url: https://github.com/boostorg/beast/issues/2214#issuecomment-1008220763  

This issue has been open for a while with no activity, has it been resolved?

---

## Comment 5

> Username: xhimanshuz  
> Created at: 2022-01-11 10:32:50 UTC  
> Url: https://github.com/boostorg/beast/issues/2214#issuecomment-1009827339  

No

---

## Comment 6

> Username: madmongo1  
> Created at: 2022-01-11 18:08:46 UTC  
> Url: https://github.com/boostorg/beast/issues/2214#issuecomment-1010227688  

I am trying to think how I can best cover the needed information.  
Do you think one example that covers the use of each method would be the right thing, or do you think a little example for each method would be better?

---

## Comment 7

> Username: xhimanshuz  
> Created at: 2022-01-12 13:15:34 UTC  
> Url: https://github.com/boostorg/beast/issues/2214#issuecomment-1011034031  

> So People get to know how to use them  
  
@madmongo1 So, I mentioned, If you add an example at the end of the page of reference, It will be easy to learn and use.

---

## Comment 8

> Username: klemens-morgenstern  
> Created at: 2022-09-24 05:25:46 UTC  
> Url: https://github.com/boostorg/beast/issues/2214#issuecomment-1256865954  

A reference is not the place to learn, it's to be used by developers already familiar with the library. You shouldn't use the cppreference as an intro to C++ either. While I get that more examples are always better, the response & request objects are seldolmy used alone, and the doc has multiple examples of how to use them right here under `Quick look` : https://www.boost.org/doc/libs/1_80_0/libs/beast/doc/html/index.html  
  
People might be thrown off by the reference being on the top of the ToC.

---

## Comment 9

> Username: NateSeymour  
> Created at: 2024-10-13 21:11:52 UTC  
> Url: https://github.com/boostorg/beast/issues/2214#issuecomment-2409132869  

@madmongo1 @ashtum Hey! I see that this issue has been stale for a while. Is there still interest in it? I'd be happy to write documentation on `request` and `response`.

---

## Comment 10

> Username: ashtum  
> Created at: 2024-10-14 11:22:42 UTC  
> Url: https://github.com/boostorg/beast/issues/2214#issuecomment-2410918335  

> @madmongo1 @ashtum Hey! I see that this issue has been stale for a while. Is there still interest in it? I'd be happy to write documentation on `request` and `response`.  
  
Yes, that sounds great. It might be helpful to share a draft of the documentation for initial feedback before investing too much time.

---

## Comment 11

> Username: NateSeymour  
> Created at: 2024-10-15 13:06:28 UTC  
> Url: https://github.com/boostorg/beast/issues/2214#issuecomment-2413866403  

@ashtum Sounds great, will do! Just for my reference, do you have any examples of `boostorg` documentation that you consider to be particularly well-written?

---

## Comment 12

> Username: ashtum  
> Created at: 2024-10-16 06:17:24 UTC  
> Url: https://github.com/boostorg/beast/issues/2214#issuecomment-2415825986  

The documentation needs to be consistent with the rest of the documentation in Beast.  
  
There are already a few examples for `request` and `response` in [Message Containers](https://www.boost.org/doc/libs/develop/libs/beast/doc/html/beast/using_http/message_containers.html). You may want to expand on this by including examples with other body types. To do so, you will need to edit [02_message.qbk](https://github.com/boostorg/beast/blob/develop/doc/qbk/04_http/02_message.qbk) and add the necessary code snippets to [http_snippets.cpp](https://github.com/boostorg/beast/blob/develop/test/doc/http_snippets.cpp).  
  
Alternatively, you can edit the Javadoc for each body type and add a concise example. You can find an example here: https://github.com/boostorg/beast/blob/fee9be0be10c9c9a22ac1505a710d1d8ed5a3dfb/include/boost/beast/websocket/stream.hpp#L649-L655  
  
The key is to keep it concise and clear; otherwise, most users will skip it without reading.  
  
By the way, to build the documentation locally, you can follow the guides available here: https://github.com/boostorg/release-tools/tree/develop/build_docs
