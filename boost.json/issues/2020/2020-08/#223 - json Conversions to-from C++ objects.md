# #223 - json Conversions to/from C++ objects [Closed]

> Username: JoeStroupe  
> Created at: 2020-08-28 16:33:39 UTC  
> Updated at: 2020-09-09 02:43:46 UTC  
> Closed at: 2020-09-01 14:56:28 UTC  
> Url: https://github.com/boostorg/json/issues/223  

Does Boost.json support conversions from C++ objects to json, and from json to C++ objects? Use case is in the interface between C++ application and calls to the ArangoDB HTTP REST API. I'm using libcurl to generate REST calls (with json bodies) into ArangoDB, and I need to convert C++ object members and their values to json for those calls. Then, on the return route, I need to populate C++ object members with the json data returned from ArangoDB calls. I also need to know if such conversions can be done recursively, or only singly.  
  
What of the case where my C++ objects exist in a vector or other std container? I'm looking for functionality akin to that noted here: https://stackoverflow.com/questions/17549906/c-json-serialization  
  
**Scroll down to the solution with 85 votes, posted by Guillaume Racicot.**

---

## Comment 1

> Username: sdkrystian  
> Created at: 2020-08-28 16:44:49 UTC  
> Url: https://github.com/boostorg/json/issues/223#issuecomment-682866531  

The library supports conversions to and from user-defined types via `value_to<T>(expr)` and `value_from(expr)`. Generic conversions for container types can be done by the library out of the box so long as they fit a certain "container-like" criteria, and such conversions are performed recursively. You can find some more details of what is supported here: http://vinniefalco.github.io/doc/json/json/usage/conversion.html  
  
If the generic conversions that we provide aren't enough, you can implement the conversion yourself by providing overloads for `tag_invoke`. These will be found by ADL and `value_to/from` will use them for the conversion.

---

## Comment 2

> Username: vinniefalco  
> Created at: 2020-08-28 16:45:12 UTC  
> Updated at: 2020-08-28 16:47:24 UTC  
> Url: https://github.com/boostorg/json/issues/223#issuecomment-682867163  

Short answer: Yes (http://vinniefalco.github.io/doc/json/json/usage/conversion.html)  
  
Long answer:  
  
Given a user defined type `T`:  
```  
    T t;  
    ...  
    // Convert from T to json::value  
    json::value jv = json::value_from( t );  
  
    // Convert json::value to T  
    T t2 = json::value_to( jv );  
```  
  
This is accomplished by providing overloads of `tag_invoke` for `T` in the namespace of `T`:  
  
```  
    // Called to convert json::value to T  
    T tag_invoke( json::value_to_tag<T>, json::value const& jv );  
  
    // Called to convert T to json::value  
    void tag_invoke( json::value_from_tag, json::value& jv, T const& );  
```  
  
Furthermore as @sdkrystian noted, the library provides generic conversions for ranges, which handles `std::vector<T>` (and maps with key/value pairs).

---

## Comment 3

> Username: JoeStroupe  
> Created at: 2020-08-28 18:45:28 UTC  
> Url: https://github.com/boostorg/json/issues/223#issuecomment-683072096  

Thanks so much for the quick and affirmative reply!  
  
From: Krystian Stasiowski <notifications@github.com>  
Sent: Friday, August 28, 2020 10:45 AM  
To: CPPAlliance/json <json@noreply.github.com>  
Cc: Joe Stroupe <joes@altia.com>; Author <author@noreply.github.com>  
Subject: Re: [CPPAlliance/json] json Conversions to/from C++ objects (#223)  
  
  
The library supports conversions to and from user-defined types via value_to<T>(expr) and value_from(expr). Generic conversions for container types can be done by the library out of the box so long as they fit a certain "container-like" criteria, and such conversions are performed recursively. You can find some more details of what is supported here: http://vinniefalco.github.io/doc/json/json/usage/conversion.html<https://urldefense.proofpoint.com/v2/url?u=http-3A__vinniefalco.github.io_doc_json_json_usage_conversion.html&d=DwMCaQ&c=euGZstcaTDllvimEN8b7jXrwqOf-v5A_CdpgnVfiiMM&r=xesY1ER9OAQvsJFpLd_0fw&m=5A_10QhbcVY9c2fmrueuRCkSqZbYmLXytgPNdq_aUZw&s=Q6fNKtLX4EuiPiudNm2uK5trJVV3KWZ1drJH6quNWyM&e=>  
  
If the generic conversions that we provide aren't enough, you can implement the conversion yourself by providing overloads for tag_invoke. These will be found by ADL and value_to/from will use them for the conversion.  
  
—  
You are receiving this because you authored the thread.  
Reply to this email directly, view it on GitHub<https://urldefense.proofpoint.com/v2/url?u=https-3A__github.com_CPPAlliance_json_issues_223-23issuecomment-2D682866531&d=DwMCaQ&c=euGZstcaTDllvimEN8b7jXrwqOf-v5A_CdpgnVfiiMM&r=xesY1ER9OAQvsJFpLd_0fw&m=5A_10QhbcVY9c2fmrueuRCkSqZbYmLXytgPNdq_aUZw&s=IlfZ91Ykt5ca7jKFTsxb-dtNnaljs7mKopyb1UKb41c&e=>, or unsubscribe<https://urldefense.proofpoint.com/v2/url?u=https-3A__github.com_notifications_unsubscribe-2Dauth_ACAKC23EUBUKEFFDWNHZWUDSC7NI7ANCNFSM4QOJ33JA&d=DwMCaQ&c=euGZstcaTDllvimEN8b7jXrwqOf-v5A_CdpgnVfiiMM&r=xesY1ER9OAQvsJFpLd_0fw&m=5A_10QhbcVY9c2fmrueuRCkSqZbYmLXytgPNdq_aUZw&s=ERVnBo57gj6NnQNXyKeBtk4uLtapmbMq3Q3TC34ELLs&e=>.

---

## Comment 4

> Username: JoeStroupe  
> Created at: 2020-08-28 18:46:01 UTC  
> Url: https://github.com/boostorg/json/issues/223#issuecomment-683072464  

Thanks so much for the quick and affirmative reply!  
  
From: Vinnie Falco <notifications@github.com>  
Sent: Friday, August 28, 2020 10:45 AM  
To: CPPAlliance/json <json@noreply.github.com>  
Cc: Joe Stroupe <joes@altia.com>; Author <author@noreply.github.com>  
Subject: Re: [CPPAlliance/json] json Conversions to/from C++ objects (#223)  
  
  
Short answer: Yes (http://vinniefalco.github.io/doc/json/json/usage/conversion.html<https://urldefense.proofpoint.com/v2/url?u=http-3A__vinniefalco.github.io_doc_json_json_usage_conversion.html&d=DwMCaQ&c=euGZstcaTDllvimEN8b7jXrwqOf-v5A_CdpgnVfiiMM&r=xesY1ER9OAQvsJFpLd_0fw&m=jfMbSzBKILQ2c7DL8HBgAHYp1jEtMut2yrwIfEx2kHY&s=NP2iSgkjfpWlfEXlQxZv9b7Cov18w19YvhhpmNoL3Zw&e=>)  
  
Long answer:  
  
Given a user defined type T:  
  
    T t;  
  
    ...  
  
    // Convert from T to json::value  
  
    json::value jv = json::value_from( t );  
  
  
  
    // Convert json::value to T  
  
    T t2 = json::value_to( jv );  
  
This is accomplished by providing overloads of tag_invoke for T in the namespace of T:  
  
    // Called to convert json::value to T  
  
    T tag_invoke( json::value_to_tag<T>, json::value const& jv );  
  
  
  
    // Called to convert T to json::value  
  
    void tag_invoke( json::value_from_tag, json::value& jv, T const& );  
  
—  
You are receiving this because you authored the thread.  
Reply to this email directly, view it on GitHub<https://urldefense.proofpoint.com/v2/url?u=https-3A__github.com_CPPAlliance_json_issues_223-23issuecomment-2D682867163&d=DwMCaQ&c=euGZstcaTDllvimEN8b7jXrwqOf-v5A_CdpgnVfiiMM&r=xesY1ER9OAQvsJFpLd_0fw&m=jfMbSzBKILQ2c7DL8HBgAHYp1jEtMut2yrwIfEx2kHY&s=wcwATpnR6_tCLm8ALqLOqFXW4NgejVGXjJxRgEzoQJg&e=>, or unsubscribe<https://urldefense.proofpoint.com/v2/url?u=https-3A__github.com_notifications_unsubscribe-2Dauth_ACAKC27LEUUC5D3YV75YXRTSC7NKNANCNFSM4QOJ33JA&d=DwMCaQ&c=euGZstcaTDllvimEN8b7jXrwqOf-v5A_CdpgnVfiiMM&r=xesY1ER9OAQvsJFpLd_0fw&m=jfMbSzBKILQ2c7DL8HBgAHYp1jEtMut2yrwIfEx2kHY&s=S2oDtKenr80hHQVyFPVaiUWlUwXLulEIMLNqzUSZAMw&e=>.

---

## Comment 5

> Username: JoeStroupe  
> Created at: 2020-08-28 21:13:50 UTC  
> Url: https://github.com/boostorg/json/issues/223#issuecomment-683149410  

Hi Vinnie,  
  
I want to build the json lib as MD/d, since that would match my C++ project I want to integrate your json lib into. In order to do this kind of build, do I need to actually edit the common.cmake file to turn off static build, or is there a higher-level define or flag I should set to achieve MD/d (Multi-user Dynamic Debug) build?  
  
  
In common.cmake:  
  
# Static library linkage.  
set(BUILD_SHARED_LIBS OFF CACHE STRING "")  
add_definitions(-DBOOST_ALL_STATIC_LINK=1)  
  
  
  
Thanks,  
  
William  
  
  
From: Vinnie Falco <notifications@github.com>  
Sent: Friday, August 28, 2020 10:45 AM  
To: CPPAlliance/json <json@noreply.github.com>  
Cc: Joe Stroupe <joes@altia.com>; Author <author@noreply.github.com>  
Subject: Re: [CPPAlliance/json] json Conversions to/from C++ objects (#223)  
  
  
Short answer: Yes (http://vinniefalco.github.io/doc/json/json/usage/conversion.html<https://urldefense.proofpoint.com/v2/url?u=http-3A__vinniefalco.github.io_doc_json_json_usage_conversion.html&d=DwMCaQ&c=euGZstcaTDllvimEN8b7jXrwqOf-v5A_CdpgnVfiiMM&r=xesY1ER9OAQvsJFpLd_0fw&m=jfMbSzBKILQ2c7DL8HBgAHYp1jEtMut2yrwIfEx2kHY&s=NP2iSgkjfpWlfEXlQxZv9b7Cov18w19YvhhpmNoL3Zw&e=>)  
  
Long answer:  
  
Given a user defined type T:  
  
    T t;  
  
    ...  
  
    // Convert from T to json::value  
  
    json::value jv = json::value_from( t );  
  
  
  
    // Convert json::value to T  
  
    T t2 = json::value_to( jv );  
  
This is accomplished by providing overloads of tag_invoke for T in the namespace of T:  
  
    // Called to convert json::value to T  
  
    T tag_invoke( json::value_to_tag<T>, json::value const& jv );  
  
  
  
    // Called to convert T to json::value  
  
    void tag_invoke( json::value_from_tag, json::value& jv, T const& );  
  
—  
You are receiving this because you authored the thread.  
Reply to this email directly, view it on GitHub<https://urldefense.proofpoint.com/v2/url?u=https-3A__github.com_CPPAlliance_json_issues_223-23issuecomment-2D682867163&d=DwMCaQ&c=euGZstcaTDllvimEN8b7jXrwqOf-v5A_CdpgnVfiiMM&r=xesY1ER9OAQvsJFpLd_0fw&m=jfMbSzBKILQ2c7DL8HBgAHYp1jEtMut2yrwIfEx2kHY&s=wcwATpnR6_tCLm8ALqLOqFXW4NgejVGXjJxRgEzoQJg&e=>, or unsubscribe<https://urldefense.proofpoint.com/v2/url?u=https-3A__github.com_notifications_unsubscribe-2Dauth_ACAKC27LEUUC5D3YV75YXRTSC7NKNANCNFSM4QOJ33JA&d=DwMCaQ&c=euGZstcaTDllvimEN8b7jXrwqOf-v5A_CdpgnVfiiMM&r=xesY1ER9OAQvsJFpLd_0fw&m=jfMbSzBKILQ2c7DL8HBgAHYp1jEtMut2yrwIfEx2kHY&s=S2oDtKenr80hHQVyFPVaiUWlUwXLulEIMLNqzUSZAMw&e=>.

---

## Comment 6

> Username: vinniefalco  
> Created at: 2020-08-28 21:16:09 UTC  
> Url: https://github.com/boostorg/json/issues/223#issuecomment-683150217  

> I want to build the json lib as MD/d  
  
The easiest thing to do is ignore my CMakeLists.txt and just add src.cpp to your project:  
https://github.com/CPPAlliance/json/blob/397a31f1667150a94c1b079de83bec85065729f6/src/src.cpp  
  
Alternatively, just add the following line to any one of your translation units:  
```  
#include <boost/json/src.hpp>  
```

---

## Comment 7

> Username: JoeStroupe  
> Created at: 2020-08-28 21:20:05 UTC  
> Url: https://github.com/boostorg/json/issues/223#issuecomment-683151606  

Thanks Vinnie!  
  
From: Vinnie Falco <notifications@github.com>  
Sent: Friday, August 28, 2020 3:16 PM  
To: CPPAlliance/json <json@noreply.github.com>  
Cc: Joe Stroupe <joes@altia.com>; Author <author@noreply.github.com>  
Subject: Re: [CPPAlliance/json] json Conversions to/from C++ objects (#223)  
  
  
I want to build the json lib as MD/d  
  
The easiest thing to do is ignore my CMakeLists.txt and just add src.cpp to your project:  
https://github.com/CPPAlliance/json/blob/397a31f1667150a94c1b079de83bec85065729f6/src/src.cpp<https://urldefense.proofpoint.com/v2/url?u=https-3A__github.com_CPPAlliance_json_blob_397a31f1667150a94c1b079de83bec85065729f6_src_src.cpp&d=DwMCaQ&c=euGZstcaTDllvimEN8b7jXrwqOf-v5A_CdpgnVfiiMM&r=xesY1ER9OAQvsJFpLd_0fw&m=IpPM2rzRFh2zYymEpeNok-bzqm4k2ntILN5mu-qO9HE&s=4_dUFOR_av33R6Or8cO3kbV1WAopy9yh3CRKFKfxWCM&e=>  
  
Alternatively, just add the following line to any one of your translation units:  
  
#include <boost/json/src.hpp>  
  
—  
You are receiving this because you authored the thread.  
Reply to this email directly, view it on GitHub<https://urldefense.proofpoint.com/v2/url?u=https-3A__github.com_CPPAlliance_json_issues_223-23issuecomment-2D683150217&d=DwMCaQ&c=euGZstcaTDllvimEN8b7jXrwqOf-v5A_CdpgnVfiiMM&r=xesY1ER9OAQvsJFpLd_0fw&m=IpPM2rzRFh2zYymEpeNok-bzqm4k2ntILN5mu-qO9HE&s=7ic8ebtWR1goSjEsdBhQvlwNoyYOqH8iF0OEiMTnZl4&e=>, or unsubscribe<https://urldefense.proofpoint.com/v2/url?u=https-3A__github.com_notifications_unsubscribe-2Dauth_ACAKC232WX4BWG7FBMKMIA3SDANCNANCNFSM4QOJ33JA&d=DwMCaQ&c=euGZstcaTDllvimEN8b7jXrwqOf-v5A_CdpgnVfiiMM&r=xesY1ER9OAQvsJFpLd_0fw&m=IpPM2rzRFh2zYymEpeNok-bzqm4k2ntILN5mu-qO9HE&s=hujlsnsJsRIJqoVxMzu-yl_A4_Vo2ODerIBlZdH2ZqM&e=>.

---

## Comment 8

> Username: vinniefalco  
> Created at: 2020-09-01 14:56:28 UTC  
> Url: https://github.com/boostorg/json/issues/223#issuecomment-684915967  

Looks like this is resolved, thanks!

---

## Comment 9

> Username: JoeStroupe  
> Created at: 2020-09-01 18:50:19 UTC  
> Url: https://github.com/boostorg/json/issues/223#issuecomment-685065379  

Hi Vinnie,  
  
In a few days I’ll have proof-of-concept code I’ve written as to how I’m implementing your Boost.json into my C++ code which uses libcurl to perform HTTP REST requests against the ArangoDB database.  
  
Would it be possible for you to critique how I’m implementing your library? I suspect I’m going to hear some real criticism on my approach – that’s fine, I want to get this right.  
  
If you would be willing to critique it, what is your preferred way for me to get you the code and explanation of what I’m trying to do?  
  
Thanks so much for your consideration, and this very nice library!  
  
William Stroupe  
  
  
  
From: Vinnie Falco <notifications@github.com>  
Sent: Friday, August 28, 2020 10:45 AM  
To: CPPAlliance/json <json@noreply.github.com>  
Cc: Joe Stroupe <joes@altia.com>; Author <author@noreply.github.com>  
Subject: Re: [CPPAlliance/json] json Conversions to/from C++ objects (#223)  
  
  
Short answer: Yes (http://vinniefalco.github.io/doc/json/json/usage/conversion.html<https://urldefense.proofpoint.com/v2/url?u=http-3A__vinniefalco.github.io_doc_json_json_usage_conversion.html&d=DwMCaQ&c=euGZstcaTDllvimEN8b7jXrwqOf-v5A_CdpgnVfiiMM&r=xesY1ER9OAQvsJFpLd_0fw&m=jfMbSzBKILQ2c7DL8HBgAHYp1jEtMut2yrwIfEx2kHY&s=NP2iSgkjfpWlfEXlQxZv9b7Cov18w19YvhhpmNoL3Zw&e=>)  
  
Long answer:  
  
Given a user defined type T:  
  
    T t;  
  
    ...  
  
    // Convert from T to json::value  
  
    json::value jv = json::value_from( t );  
  
  
  
    // Convert json::value to T  
  
    T t2 = json::value_to( jv );  
  
This is accomplished by providing overloads of tag_invoke for T in the namespace of T:  
  
    // Called to convert json::value to T  
  
    T tag_invoke( json::value_to_tag<T>, json::value const& jv );  
  
  
  
    // Called to convert T to json::value  
  
    void tag_invoke( json::value_from_tag, json::value& jv, T const& );  
  
—  
You are receiving this because you authored the thread.  
Reply to this email directly, view it on GitHub<https://urldefense.proofpoint.com/v2/url?u=https-3A__github.com_CPPAlliance_json_issues_223-23issuecomment-2D682867163&d=DwMCaQ&c=euGZstcaTDllvimEN8b7jXrwqOf-v5A_CdpgnVfiiMM&r=xesY1ER9OAQvsJFpLd_0fw&m=jfMbSzBKILQ2c7DL8HBgAHYp1jEtMut2yrwIfEx2kHY&s=wcwATpnR6_tCLm8ALqLOqFXW4NgejVGXjJxRgEzoQJg&e=>, or unsubscribe<https://urldefense.proofpoint.com/v2/url?u=https-3A__github.com_notifications_unsubscribe-2Dauth_ACAKC27LEUUC5D3YV75YXRTSC7NKNANCNFSM4QOJ33JA&d=DwMCaQ&c=euGZstcaTDllvimEN8b7jXrwqOf-v5A_CdpgnVfiiMM&r=xesY1ER9OAQvsJFpLd_0fw&m=jfMbSzBKILQ2c7DL8HBgAHYp1jEtMut2yrwIfEx2kHY&s=S2oDtKenr80hHQVyFPVaiUWlUwXLulEIMLNqzUSZAMw&e=>.

---

## Comment 10

> Username: vinniefalco  
> Created at: 2020-09-01 20:15:32 UTC  
> Url: https://github.com/boostorg/json/issues/223#issuecomment-685107086  

> If you would be willing to critique it, what is your preferred way for me to get you the code and explanation of what I’m trying to do?  
  
I am more than happy to comment on the usage of my libraries in other programs. The best way to do that is to open a pull request against your own GitHub repository, and add me as a reviewer so I can leave comments.

---

## Comment 11

> Username: JoeStroupe  
> Created at: 2020-09-01 21:02:34 UTC  
> Url: https://github.com/boostorg/json/issues/223#issuecomment-685130686  

Hey Vinnie,  
  
Thanks so much for your interest and help! At this point I don’t have the code in a repo since it’s really just my initial take on how to get familiar with your library, and the interactions between the libcurl Requirements for json bodies and the Boost.json tools.  
  
But when I’m ready I could send you a zip file with the code and a good explanation of what I’m trying/needing to accomplish.  
  
Thanks,  
  
William  
  
  
From: Vinnie Falco <notifications@github.com>  
Sent: Tuesday, September 01, 2020 2:16 PM  
To: CPPAlliance/json <json@noreply.github.com>  
Cc: Joe Stroupe <joes@altia.com>; Author <author@noreply.github.com>  
Subject: Re: [CPPAlliance/json] json Conversions to/from C++ objects (#223)  
  
  
If you would be willing to critique it, what is your preferred way for me to get you the code and explanation of what I’m trying to do?  
  
I am more than happy to comment on the usage of my libraries in other programs. The best way to do that is to open a pull request against your own GitHub repository, and add me as a reviewer so I can leave comments.  
  
—  
You are receiving this because you authored the thread.  
Reply to this email directly, view it on GitHub<https://urldefense.proofpoint.com/v2/url?u=https-3A__github.com_CPPAlliance_json_issues_223-23issuecomment-2D685107086&d=DwMFaQ&c=euGZstcaTDllvimEN8b7jXrwqOf-v5A_CdpgnVfiiMM&r=xesY1ER9OAQvsJFpLd_0fw&m=m-cmCDi4dsGfJJqAIqp2W1kOsuO-_NKJLMfA5JXHPh8&s=Nl79Mg3lmoUZIJFItJE4CDM6Spg6AE92xV-OLll_12A&e=>, or unsubscribe<https://urldefense.proofpoint.com/v2/url?u=https-3A__github.com_notifications_unsubscribe-2Dauth_ACAKC27HOEQNAODWTCK6IILSDVI7HANCNFSM4QOJ33JA&d=DwMFaQ&c=euGZstcaTDllvimEN8b7jXrwqOf-v5A_CdpgnVfiiMM&r=xesY1ER9OAQvsJFpLd_0fw&m=m-cmCDi4dsGfJJqAIqp2W1kOsuO-_NKJLMfA5JXHPh8&s=k4CsxOCDiKcFEZN8QTpQoq5H7SRv6j8i0Ex16Hq_xTQ&e=>.

---

## Comment 12

> Username: vinniefalco  
> Created at: 2020-09-01 21:04:41 UTC  
> Url: https://github.com/boostorg/json/issues/223#issuecomment-685131663  

> But when I’m ready I could send you a zip file with the code and a good explanation of what I’m trying/needing to accomplish.  
  
I encourage you to create a repository. It is easy, and free. I can't really comment on a zip file. GitHub has wonderful tools for collaboration - we should use it!

---

## Comment 13

> Username: JoeStroupe  
> Created at: 2020-09-01 21:13:04 UTC  
> Url: https://github.com/boostorg/json/issues/223#issuecomment-685135496  

Ok – will do – thx!  
  
From: Vinnie Falco <notifications@github.com>  
Sent: Tuesday, September 01, 2020 3:05 PM  
To: CPPAlliance/json <json@noreply.github.com>  
Cc: Joe Stroupe <joes@altia.com>; Author <author@noreply.github.com>  
Subject: Re: [CPPAlliance/json] json Conversions to/from C++ objects (#223)  
  
  
But when I’m ready I could send you a zip file with the code and a good explanation of what I’m trying/needing to accomplish.  
  
I encourage you to create a repository. It is easy, and free. I can't really comment on a zip file. GitHub has wonderful tools for collaboration - we should use it!  
  
—  
You are receiving this because you authored the thread.  
Reply to this email directly, view it on GitHub<https://urldefense.proofpoint.com/v2/url?u=https-3A__github.com_CPPAlliance_json_issues_223-23issuecomment-2D685131663&d=DwMFaQ&c=euGZstcaTDllvimEN8b7jXrwqOf-v5A_CdpgnVfiiMM&r=xesY1ER9OAQvsJFpLd_0fw&m=TafC5rD9P3U1Eq_PRL6OcpJCnLZND5lVfPl_JObVF1w&s=ESLqEdE5P60EmnSYeWFN71U9SuGyNLszhy1g_OKW8U4&e=>, or unsubscribe<https://urldefense.proofpoint.com/v2/url?u=https-3A__github.com_notifications_unsubscribe-2Dauth_ACAKC2ZDQ7THPZXQIAUULETSDVOXPANCNFSM4QOJ33JA&d=DwMFaQ&c=euGZstcaTDllvimEN8b7jXrwqOf-v5A_CdpgnVfiiMM&r=xesY1ER9OAQvsJFpLd_0fw&m=TafC5rD9P3U1Eq_PRL6OcpJCnLZND5lVfPl_JObVF1w&s=a5EC9EQ5mdPtEFDP6u9tT9xhwha87Jp3ueB-VU8MmsY&e=>.

---

## Comment 14

> Username: JoeStroupe  
> Created at: 2020-09-08 15:44:19 UTC  
> Url: https://github.com/boostorg/json/issues/223#issuecomment-688965299  

Hi Vinnie,  
  
The docs have examples of calling the serialize() function to output a std::string from a json value, and the examples give a nicely formatted json string as output, exactly as required for an HTTP request, but the only function I see is json::to_string(). There appears to be no such function or object named “serialize”.  
  
When I use to_string() I get a string with ‘[‘ ‘]’ brkts and it’s not suitable for a json body in an HTTP request. What am I missing here?  
  
Thanks,  
  
William  
  
  
From: Vinnie Falco <notifications@github.com>  
Sent: Tuesday, September 01, 2020 3:05 PM  
To: CPPAlliance/json <json@noreply.github.com>  
Cc: Joe Stroupe <joes@altia.com>; Author <author@noreply.github.com>  
Subject: Re: [CPPAlliance/json] json Conversions to/from C++ objects (#223)  
  
  
But when I’m ready I could send you a zip file with the code and a good explanation of what I’m trying/needing to accomplish.  
  
I encourage you to create a repository. It is easy, and free. I can't really comment on a zip file. GitHub has wonderful tools for collaboration - we should use it!  
  
—  
You are receiving this because you authored the thread.  
Reply to this email directly, view it on GitHub<https://urldefense.proofpoint.com/v2/url?u=https-3A__github.com_CPPAlliance_json_issues_223-23issuecomment-2D685131663&d=DwMFaQ&c=euGZstcaTDllvimEN8b7jXrwqOf-v5A_CdpgnVfiiMM&r=xesY1ER9OAQvsJFpLd_0fw&m=TafC5rD9P3U1Eq_PRL6OcpJCnLZND5lVfPl_JObVF1w&s=ESLqEdE5P60EmnSYeWFN71U9SuGyNLszhy1g_OKW8U4&e=>, or unsubscribe<https://urldefense.proofpoint.com/v2/url?u=https-3A__github.com_notifications_unsubscribe-2Dauth_ACAKC2ZDQ7THPZXQIAUULETSDVOXPANCNFSM4QOJ33JA&d=DwMFaQ&c=euGZstcaTDllvimEN8b7jXrwqOf-v5A_CdpgnVfiiMM&r=xesY1ER9OAQvsJFpLd_0fw&m=TafC5rD9P3U1Eq_PRL6OcpJCnLZND5lVfPl_JObVF1w&s=a5EC9EQ5mdPtEFDP6u9tT9xhwha87Jp3ueB-VU8MmsY&e=>.

---

## Comment 15

> Username: vinniefalco  
> Created at: 2020-09-08 15:57:44 UTC  
> Updated at: 2020-09-08 15:58:01 UTC  
> Url: https://github.com/boostorg/json/issues/223#issuecomment-688974043  

I just recently changed `to_string` to `serialize`. Have you picked up the latest _develop_ branch?  
  
> When I use to_string() I get a string with ‘[‘ ‘]’ brkts and it’s not suitable for a json body in an HTTP request.   
  
Yeah...that's the problem. The standard library and Boost already provide many overloads of `to_string` so even though I took out the function, another overload is still being found! So it is being converted incorrectly...

---

## Comment 16

> Username: JoeStroupe  
> Created at: 2020-09-08 16:41:02 UTC  
> Url: https://github.com/boostorg/json/issues/223#issuecomment-689000964  

Hi Vinnie,  
  
I just picked up the latest dev branch (says updated 2 days ago), changed my code to use boost::json::serialize(), and made sure all the new code is indeed being used in my VS 2019 project. I know it is now because the serialize() function is now seen by VS 2019.  
  
However, after a clean and rebuild it did not change the output from what it was before, when I was using to_string().  
  
My project does NOT use Boost anywhere, and I have no environment variables set for Boost.  
  
I am using namespace boost::json after including <boost/json/src.hpp>  
  
I have a pre-processor define BOOST_JSON_STANDALONE in my project.  
  
Any suggestions on how to get the new serialize() to work correctly?  
  
Thanks!  
  
William  
  
  
From: Vinnie Falco <notifications@github.com>  
Sent: Tuesday, September 08, 2020 9:58 AM  
To: CPPAlliance/json <json@noreply.github.com>  
Cc: Joe Stroupe <joes@altia.com>; Author <author@noreply.github.com>  
Subject: Re: [CPPAlliance/json] json Conversions to/from C++ objects (#223)  
  
  
I just changed to_string to serialize. Have you picked up the latest develop branch?  
  
When I use to_string() I get a string with ‘[‘ ‘]’ brkts and it’s not suitable for a json body in an HTTP request.  
  
Yeah...that's the problem. The standard library and Boost already provide many overloads of to_string so even though I took out the function, another overload is still being found! So it is being converted incorrectly...  
  
—  
You are receiving this because you authored the thread.  
Reply to this email directly, view it on GitHub<https://urldefense.proofpoint.com/v2/url?u=https-3A__github.com_CPPAlliance_json_issues_223-23issuecomment-2D688974043&d=DwMFaQ&c=euGZstcaTDllvimEN8b7jXrwqOf-v5A_CdpgnVfiiMM&r=xesY1ER9OAQvsJFpLd_0fw&m=UJnVWwSk9SUV8CCeV5E74GsbgNieT2REnNJlT0l15hM&s=C0CP6AurYkS0fwm_URPQxgoVVgA58jc_QPj3LebSNWY&e=>, or unsubscribe<https://urldefense.proofpoint.com/v2/url?u=https-3A__github.com_notifications_unsubscribe-2Dauth_ACAKC2ZILJKIZH455PTQXYLSEZIARANCNFSM4QOJ33JA&d=DwMFaQ&c=euGZstcaTDllvimEN8b7jXrwqOf-v5A_CdpgnVfiiMM&r=xesY1ER9OAQvsJFpLd_0fw&m=UJnVWwSk9SUV8CCeV5E74GsbgNieT2REnNJlT0l15hM&s=VmaO0jbbZc16GBJvQOpA9ZUikwmZX59V8cB94kkboq8&e=>.

---

## Comment 17

> Username: vinniefalco  
> Created at: 2020-09-08 16:43:34 UTC  
> Url: https://github.com/boostorg/json/issues/223#issuecomment-689002477  

> Any suggestions on how to get the new serialize() to work correctly?  
  
Can you please provide a small sample program that demonstrates the problem?

---

## Comment 18

> Username: JoeStroupe  
> Created at: 2020-09-08 16:52:42 UTC  
> Url: https://github.com/boostorg/json/issues/223#issuecomment-689007587  

Sure – see attached zip file.  
  
In the jsonBodyBuilder class you’ll see code I wrote to do the conversion the correct way – that code is #defined out, and I’m calling serialize() instead.  
  
There’s other test code in the .cpp file that is also #defined out. The operative code is just a few lines, as you’ll see.  
  
Thanks, William  
  
  
From: Vinnie Falco <notifications@github.com>  
Sent: Tuesday, September 08, 2020 10:44 AM  
To: CPPAlliance/json <json@noreply.github.com>  
Cc: Joe Stroupe <joes@altia.com>; Author <author@noreply.github.com>  
Subject: Re: [CPPAlliance/json] json Conversions to/from C++ objects (#223)  
  
  
Any suggestions on how to get the new serialize() to work correctly?  
  
Can you please provide a small sample program that demonstrates the problem?  
  
—  
You are receiving this because you authored the thread.  
Reply to this email directly, view it on GitHub<https://urldefense.proofpoint.com/v2/url?u=https-3A__github.com_CPPAlliance_json_issues_223-23issuecomment-2D689002477&d=DwMCaQ&c=euGZstcaTDllvimEN8b7jXrwqOf-v5A_CdpgnVfiiMM&r=xesY1ER9OAQvsJFpLd_0fw&m=HvI1wZFc9L1_gtr1-8asiiadQO827o-hGU34uOAk9YA&s=TiJtCpPNKKcmUAQqSDTfNVkPqCgDrm6z7NfzV2Vz2Jc&e=>, or unsubscribe<https://urldefense.proofpoint.com/v2/url?u=https-3A__github.com_notifications_unsubscribe-2Dauth_ACAKC24BR3DPBOL2XOZQLELSEZNMLANCNFSM4QOJ33JA&d=DwMCaQ&c=euGZstcaTDllvimEN8b7jXrwqOf-v5A_CdpgnVfiiMM&r=xesY1ER9OAQvsJFpLd_0fw&m=HvI1wZFc9L1_gtr1-8asiiadQO827o-hGU34uOAk9YA&s=51gdk3VFXNELidim9URYkC2Mdi0klrc5dPbosivkSAs&e=>.

---

## Comment 19

> Username: vinniefalco  
> Created at: 2020-09-08 16:55:57 UTC  
> Url: https://github.com/boostorg/json/issues/223#issuecomment-689009305  

I don't see any zip file, can you paste the minimal sample program here?

---

## Comment 20

> Username: JoeStroupe  
> Created at: 2020-09-08 17:05:24 UTC  
> Url: https://github.com/boostorg/json/issues/223#issuecomment-689014518  

ArangodbBackend.h :  
  
  
#pragma once  
  
  
#include <typeinfo>  
#include <type_traits>  
#include <iterator>  
#include <vector>  
#include <list>  
#include <algorithm>  
#include <string>  
#include <stdio.h>  
#include <stdlib.h>  
#include <tchar.h>  
#include <cstddef> // NULL  
#include <iomanip>  
#include <iostream>  
#include <fstream>  
  
  
  
  
#if 1  
  
#undef DOMDocument  
  
#ifndef __MSXML_LIBRARY_DEFINED__  
#define __MSXML_LIBRARY_DEFINED__  
#endif  
  
  
// includes for curl  
#include <curl/curl.h>  
  
// includes for json  
#include <boost/json/src.hpp>  
  
using namespace boost::json;  
  
#define CURL_PAIR_SEPARATOR               "\":\""  
  
  
enum BUILD_STS  
{  
                STARTED = 0,  
                FINISHED  
};  
  
  
// Pseudo-pair object  
struct reqPair  
{  
                std::string name;  
                std::string value;  
};  
  
  
// json body builder class  
//  
// singleton class.  
//  
// The user-facing API. Abstracts all the details away from the user.  
//  
  
  
class jsonBodyBuilder  
{  
public:  
  
                jsonBodyBuilder(bool curl = true) : use_curl(curl), pairsAppended(0), Sts(FINISHED) {}                                      // Init options list here  
  
                virtual ~jsonBodyBuilder() {}  
  
                static jsonBodyBuilder& getInstance() { static jsonBodyBuilder theInstance; return theInstance; }  
  
                // Supply a name-value pair to construct a reqPair object  
                template <typename Left, typename Right>  
                bool startBodyBuild(Left const l, Right const r)  
                {  
                                // Is this call valid at this time?  
                                if (FINISHED != Sts)  
                                {  
                                                // Can't start when already started, and not yet finished  
                                                return false;  
                                }  
  
                                // Remember to update our status  
                                Sts = STARTED;  
  
                                // Append it  
                                bool result = appendBody(l, r);  
                                return result;  
                }  
  
  
                template <typename Left, typename Right>  
                bool appendBody(Left const l, Right const r)  
                {  
                                if (STARTED != Sts)  
                                {  
                                                // Can't APPEND when not yet started  
                                                return false;  
                                }  
  
                                // Construct a pseudo pair object  
                                reqPair *newPair = new reqPair;  
                                                if (nullptr == newPair)  
                                                                return false;  
  
                                // Set up the first and second members of the Pair  
  
                                // Is the left parameter a string-type?  
                                if constexpr (!std::is_same_v<Left, const char*> // A string literal  
                                                && !std::is_same_v<Left, std::string>)                                    // A std::string  
                                // Nope - it's not a string-type - do std::to_string()  
                                {  
                                                newPair->name = std::to_string(l);  
                                }  
                                // It is a string-type - just copy it into the Pair.first  
                                else  
                                {  
                                                newPair->name = l;  
                                }  
  
                                // Is the right parameter a string-type?  
                                if constexpr (!std::is_same_v<Right, const char*>              // A string literal  
                                                && !std::is_same_v<Right, std::string>)                                 // A std::string  
                                // Nope - it's not a string-type - do std::to_string()  
                                {  
                                                newPair->value = std::to_string(r);  
                                }  
                                // It is a string-type - just copy it into the Pair.first  
                                else  
                                {  
                                                newPair->value = r;  
                                }  
  
                                // Got a valid object - push it onto the vector  
                                ReqV.push_back(newPair);  
  
                                // Incr the # of pairs we've pushed onto the vector  
                                pairsAppended++;  
                                return true;  
                }  
  
  
                bool finishBody(std::string &s)  
                {  
                                if (STARTED != Sts)  
                                {  
                                                // Can't finish when not yet started  
                                                return false;  
                                }  
  
                                // Do the convert of each object in the vector  
                                value requestBodyjs = boost::json::value_from(ReqV);  
                                s = boost::json::serialize(requestBodyjs);  
  
#if 0  
                                // If curl is being used, re-format the json body  
                                if (use_curl)  
                                {  
//                                            boost::json::string str = to_string(requestBodyjs);  
                                                s = serialize(requestBodyjs);  
  
                                                // Get rid of annoying '[' ']' sets  
                                                s.erase(std::remove(s.begin(), s.end(), '['), s.end());  
                                                s.erase(std::remove(s.begin(), s.end(), ']'), s.end());  
  
                                                // Add missing \ in front of " where needed  
                                                for (int it = 0; it != s.size(); it++)  
                                                {  
                                                                // The " char is the first char and needs a preceding '\'  
                                                                if (s[it] == '"' && it == 0)  
                                                                {  
                                                                                s.insert(it, "\\");  
                                                                                continue;  
                                                                }  
                                                                // The " char needs a preceding '\'  
                                                                if (s[it] == '"' && s[it - 1] != '\\')  
                                                                {  
                                                                                s.insert(it, "\\");  
                                                                }  
                                                }  
  
                                                // Insert "{ at start and append }" at end  
                                                s.insert(0, "\"{");  
                                                s.append("}\"");  
                                }  
  
#endif  
  
                                // Reset members for next conversion  
                                for (auto p : ReqV)  
                                                delete p;  
                                ReqV.clear();  
                                pairsAppended = 0;  
                                Sts = FINISHED;  
                                return true;  
                }  
  
protected:  
                bool                                                                       use_curl;  
                BUILD_STS                                                          Sts;  
                unsigned int                                       pairsAppended;  
                std::vector<reqPair *>   ReqV;  
};  
  
  
  
template <class T>  
void  
tag_invoke(const value_from_tag&, value& jval, T const& t)  
{  
                jval = { t->name + CURL_PAIR_SEPARATOR + t->value };  
}  
  
  
  
// Write callback function for HTTP response content  
static size_t WriteCallback(void* contents, size_t size, size_t nmemb, void* userp)  
{  
                ((std::string*)userp)->append((char*)contents, size * nmemb);  
                return size * nmemb;  
}  
  
  
#endif  
  
  
  
  
  
ArangoDB_Backend.cpp  :  
  
  
  
  
// ArangoDB_Backend.cpp : This file contains the 'main' function. Program execution begins and ends there.  
//  
  
#include "ArangodbBackend.h"  
  
using namespace boost::json;  
  
  
#if 0  
  
class poo  
{  
public:  
                poo() {}  
                ~poo() {}  
                int i;  
};  
  
  
template <typename Left, typename Right>  
void test(Left const l, Right const r)  
{  
                if constexpr (std::is_same_v<Right, const char*>)              // A string literal  
                {  
                                std::cout << "Right param is string literal" << std::endl;  
                }  
                if constexpr (std::is_same_v<Right, int>)  
                {  
                                std::cout << "Right param is int" << std::endl;  
                }  
                if constexpr (std::is_same_v<Right, std::string>)  
                {  
                                std::cout << "Right param is std::string" << std::endl;  
                }  
                if constexpr (std::is_same_v<Right, long int>)  
                {  
                                std::cout << "Right param is long int" << std::endl;  
                }  
                if constexpr (std::is_same_v<Right, long long int>)  
                {  
                                std::cout << "Right param is long long int" << std::endl;  
                }  
                if constexpr (std::is_same_v<Right, float>)  
                {  
                                std::cout << "Right param is float" << std::endl;  
                }  
                if constexpr (std::is_same_v<Right, double>)  
                {  
                                std::cout << "Right param is double" << std::endl;  
                }  
                if constexpr (std::is_same_v<Right, unsigned int>)  
                {  
                                std::cout << "Right param is unsigned int" << std::endl;  
                }  
                if constexpr (std::is_same_v<Right, unsigned long int>)  
                {  
                                std::cout << "Right param is unsigned long int" << std::endl;  
                }  
                if constexpr (std::is_same_v<Right, unsigned long long int>)  
                {  
                                std::cout << "Right param is unsigned long long int" << std::endl;  
                }  
                if constexpr (std::is_same_v<Right, poo>)  
                {  
                                std::cout << "Right param is poo" << std::endl;  
                }  
}  
  
#endif  
  
int main()  
{  
  
#if 0  
                poo p;  
                std::string s = "world";  
                test("const char*", "world");  
                test("int", 8);  
                test("std::string", s);  
                test("long int", 8L);  
                test("long long int", 9LL);  
                test("float", 3.0F);  
                test("double", 3.14151223456667);  
                test("unsigned int", 8u);  
                test("unsigned long int", 8ul);  
                test("unsigned long long int", 8ull);  
                test("poo", p);  
#endif  
  
  
#if 0  
  
                CURL* curl;  
                CURLcode res;  
                std::string readBuffer;  
                std::string requestURL;  
  
                std::string requestBody;  
                struct curl_slist* headers = NULL;  
                long http_code;  
  
                curl_global_init(CURL_GLOBAL_ALL);  
                curl = curl_easy_init();  
                if (!curl)  
                {  
                                std::cout << "curl init failed! << std::endl";  
                                return 0;  
                }  
  
                // A PUT operation to change the name of a Collection  
  
                curl_easy_setopt(curl, CURLOPT_VERBOSE, 1L);  
                curl_easy_setopt(curl, CURLOPT_HTTPAUTH, CURLAUTH_BASIC);  
                curl_easy_setopt(curl, CURLOPT_USERPWD, "Joe:Siberian21");  
                curl_easy_setopt(curl, CURLOPT_WRITEFUNCTION, WriteCallback);  
                curl_easy_setopt(curl, CURLOPT_WRITEDATA, &readBuffer);  
                readBuffer.clear();  
                curl_easy_setopt(curl, CURLOPT_TCP_KEEPALIVE, 1L);  
                headers = curl_slist_append(headers, "Accept: application/json");  
                headers = curl_slist_append(headers, "Content-Type: application/json");  
                headers = curl_slist_append(headers, "charset: utf-8");  
  
                requestURL = "http://localhost:8529/_db/testdrive/_api/collection/testCollection11/rename";  
                curl_easy_setopt(curl, CURLOPT_URL, requestURL.c_str());  
  
                curl_easy_setopt(curl, CURLOPT_CUSTOMREQUEST, "PUT"); /* !!! */  
  
  
                requestBody = "{\"name\":\"testCollection12\"}";  
                curl_easy_setopt(curl, CURLOPT_POSTFIELDS, requestBody.c_str());  
                curl_easy_setopt(curl, CURLOPT_POSTFIELDSIZE, requestBody.size());  
  
  
                res = curl_easy_perform(curl);  
                if (CURLE_OK != res)  
                {  
                                std::cout << "Request failed with error code = " << curl_easy_strerror(res) << std::endl << std::endl;  
                }  
  
                curl_easy_getinfo(curl, CURLINFO_RESPONSE_CODE, &http_code);  
                std::cout << "http code = " << http_code << std::endl << std::endl;  
  
                std::cout << readBuffer << std::endl << std::endl;  
  
  
  
  
                // Another PUT operation to change the name of a Collection again  
                requestURL = "http://localhost:8529/_db/testdrive/_api/collection/testCollection12/rename";  
                curl_easy_setopt(curl, CURLOPT_URL, requestURL.c_str());  
  
                requestBody = "{\"name\":\"testCollection13\"}";  
                curl_easy_setopt(curl, CURLOPT_POSTFIELDS, requestBody.c_str());  
                curl_easy_setopt(curl, CURLOPT_POSTFIELDSIZE, requestBody.size());  
                readBuffer.clear();  
  
                res = curl_easy_perform(curl);  
                if (CURLE_OK != res)  
                {  
                                std::cout << "Request failed with error code = " << curl_easy_strerror(res) << std::endl << std::endl;  
                }  
  
                curl_easy_getinfo(curl, CURLINFO_RESPONSE_CODE, &http_code);  
                std::cout << "http code = " << http_code << std::endl << std::endl;  
  
                std::cout << readBuffer << std::endl << std::endl;  
  
  
  
  
                // Create document (POST) operation without returnNew=true  
                curl_easy_setopt(curl, CURLOPT_CUSTOMREQUEST, NULL);         // Clear out the CUSTOMREQUEST stuff so Arangod won't think it's a PUT  
                curl_easy_setopt(curl, CURLOPT_POST, 1L); /* !!! */  
                curl_easy_setopt(curl, CURLOPT_TCP_KEEPALIVE, 1L);                    // Keep the connection alive  
                requestURL = "http://localhost:8529/_db/testdrive/_api/document/testCollection13";  
                curl_easy_setopt(curl, CURLOPT_URL, requestURL.c_str());  
                requestBody.clear();  
                requestBody = "{\"Hello\":\"World\"}";  
                curl_easy_setopt(curl, CURLOPT_POSTFIELDS, requestBody.c_str());  
                curl_easy_setopt(curl, CURLOPT_POSTFIELDSIZE, requestBody.size());  
                readBuffer.clear();  
                res = curl_easy_perform(curl);  
                if (CURLE_OK != res)  
                {  
                                std::cout << "Request failed with error code = " << curl_easy_strerror(res) << std::endl << std::endl;  
                }  
  
                curl_easy_getinfo(curl, CURLINFO_RESPONSE_CODE, &http_code);  
                std::cout << "http code = " << http_code << std::endl << std::endl;  
  
                std::cout << readBuffer << std::endl << std::endl;  
  
  
  
                // Create document (POST) operation with returnNew=true  
                curl_easy_setopt(curl, CURLOPT_CUSTOMREQUEST, NULL);         // Clear out the CUSTOMREQUEST stuff so Arangod won't think it's a PUT  
                curl_easy_setopt(curl, CURLOPT_POST, 1L); /* !!! */  
                curl_easy_setopt(curl, CURLOPT_TCP_KEEPALIVE, 1L);                    // Keep the connection alive  
                requestURL = "http://localhost:8529/_db/testdrive/_api/document/testCollection13?returnNew=true";  
                curl_easy_setopt(curl, CURLOPT_URL, requestURL.c_str());  
                requestBody.clear();  
                requestBody = "{\"Hello\":\"World\"}";  
                curl_easy_setopt(curl, CURLOPT_POSTFIELDS, requestBody.c_str());  
                curl_easy_setopt(curl, CURLOPT_POSTFIELDSIZE, requestBody.size());  
                readBuffer.clear();  
                res = curl_easy_perform(curl);  
                if (CURLE_OK != res)  
                {  
                                std::cout << "Request failed with error code = " << curl_easy_strerror(res) << std::endl << std::endl;  
                }  
  
                curl_easy_getinfo(curl, CURLINFO_RESPONSE_CODE, &http_code);  
                std::cout << "http code = " << http_code << std::endl << std::endl;  
  
                std::cout << readBuffer << std::endl << std::endl;  
  
  
  
  
                // Create document (POST) operation which we will subsequently update with a new revision  
  
                // Create document (POST) operation without returnNew=true, _key = lock  
                curl_easy_setopt(curl, CURLOPT_CUSTOMREQUEST, NULL);         // Clear out the CUSTOMREQUEST stuff so Arangod won't think it's a PUT  
                curl_easy_setopt(curl, CURLOPT_POST, 1L); /* !!! */  
                curl_easy_setopt(curl, CURLOPT_TCP_KEEPALIVE, 1L);                    // Keep the connection alive  
                requestURL = "http://localhost:8529/_db/testdrive/_api/document/testCollection13";  
                curl_easy_setopt(curl, CURLOPT_URL, requestURL.c_str());  
                requestBody.clear();  
                requestBody = "{\"Hello\":\"World\", \"_key\":\"lock\"}";  
  
                std::cout << std::endl << "Transmitting: " << requestBody << std::endl;  
  
                curl_easy_setopt(curl, CURLOPT_POSTFIELDS, requestBody.c_str());  
                curl_easy_setopt(curl, CURLOPT_POSTFIELDSIZE, requestBody.size());  
                readBuffer.clear();  
                res = curl_easy_perform(curl);  
                if (CURLE_OK != res)  
                {  
                                std::cout << "Request failed with error code = " << curl_easy_strerror(res) << std::endl << std::endl;  
                }  
  
                curl_easy_getinfo(curl, CURLINFO_RESPONSE_CODE, &http_code);  
                std::cout << "http code = " << http_code << std::endl << std::endl;  
  
                std::cout << readBuffer << std::endl << std::endl;  
  
  
  
                // Update previously created document _key = lock (POST) operation  
                curl_easy_setopt(curl, CURLOPT_CUSTOMREQUEST, NULL);         // Clear out the CUSTOMREQUEST stuff so Arangod won't think it's a PUT  
                curl_easy_setopt(curl, CURLOPT_POST, 1L); /* !!! */  
                curl_easy_setopt(curl, CURLOPT_TCP_KEEPALIVE, 1L);                    // Keep the connection alive  
                requestURL = "http://localhost:8529/_db/testdrive/_api/document/testCollection13?overwrite=true";  
                curl_easy_setopt(curl, CURLOPT_URL, requestURL.c_str());  
                requestBody.clear();  
                requestBody = "{\"Hello\":\"Universe\", \"_key\":\"lock\"}";  
                curl_easy_setopt(curl, CURLOPT_POSTFIELDS, requestBody.c_str());  
                curl_easy_setopt(curl, CURLOPT_POSTFIELDSIZE, requestBody.size());  
                readBuffer.clear();  
                res = curl_easy_perform(curl);  
                if (CURLE_OK != res)  
                {  
                                std::cout << "Request failed with error code = " << curl_easy_strerror(res) << std::endl << std::endl;  
                }  
  
                curl_easy_getinfo(curl, CURLINFO_RESPONSE_CODE, &http_code);  
                std::cout << "http code = " << http_code << std::endl << std::endl;  
  
                std::cout << readBuffer << std::endl << std::endl;  
  
  
  
                // Create a document which will subsequently be replaced by another document  
  
                // Create the first document  
                // Create document (POST) operation with returnNew=true  
                curl_easy_setopt(curl, CURLOPT_CUSTOMREQUEST, NULL);         // Clear out the CUSTOMREQUEST stuff so Arangod won't think it's a PUT  
                curl_easy_setopt(curl, CURLOPT_POST, 1L); /* !!! */  
                curl_easy_setopt(curl, CURLOPT_TCP_KEEPALIVE, 1L);                    // Keep the connection alive  
                requestURL = "http://localhost:8529/_db/testdrive/_api/document/testCollection13?returnNew=true";  
                curl_easy_setopt(curl, CURLOPT_URL, requestURL.c_str());  
                requestBody.clear();  
                requestBody = "{\"Hello\":\"World\"}";  
                curl_easy_setopt(curl, CURLOPT_POSTFIELDS, requestBody.c_str());  
                curl_easy_setopt(curl, CURLOPT_POSTFIELDSIZE, requestBody.size());  
                readBuffer.clear();  
                res = curl_easy_perform(curl);  
                if (CURLE_OK != res)  
                {  
                                std::cout << "Request failed with error code = " << curl_easy_strerror(res) << std::endl << std::endl;  
                }  
  
                curl_easy_getinfo(curl, CURLINFO_RESPONSE_CODE, &http_code);  
                std::cout << "http code = " << http_code << std::endl << std::endl;  
  
                std::cout << readBuffer << std::endl << std::endl;  
  
  
                // Replace an existing document with this new one  
                curl_easy_setopt(curl, CURLOPT_CUSTOMREQUEST, "PUT"); /* !!! */  
                curl_easy_setopt(curl, CURLOPT_TCP_KEEPALIVE, 1L);                    // Keep the connection alive  
                requestURL = "http://localhost:8529/_db/testdrive/_api/document/testCollection13/379923?returnNew=true";  
                curl_easy_setopt(curl, CURLOPT_URL, requestURL.c_str());  
                requestBody.clear();  
                requestBody = "{\"Hello\":\"Dummy\"}";  
                curl_easy_setopt(curl, CURLOPT_POSTFIELDS, requestBody.c_str());  
                curl_easy_setopt(curl, CURLOPT_POSTFIELDSIZE, requestBody.size());  
                readBuffer.clear();  
                res = curl_easy_perform(curl);  
                if (CURLE_OK != res)  
                {  
                                std::cout << "Request failed with error code = " << curl_easy_strerror(res) << std::endl << std::endl;  
                }  
  
                curl_easy_getinfo(curl, CURLINFO_RESPONSE_CODE, &http_code);  
                std::cout << "http code = " << http_code << std::endl << std::endl;  
  
                std::cout << readBuffer << std::endl << std::endl;  
  
  
  
                // Update an existing document using PATCH and the document _id and _key  
  
  
  
  
  
  
  
  
  
                // Merge attributes of an object using document _id and _key and ?mergeObjects  
  
  
  
  
  
  
  
  
  
  
                // Delete a document  
  
  
  
  
  
  
  
  
  
  
  
                // Read multiple documents  
  
  
  
  
  
  
  
  
  
  
  
  
                // Create multiple documents  
  
  
  
  
  
  
  
  
  
  
  
  
                // Delete multiple documents  
  
  
  
  
  
  
  
  
  
  
  
  
                // PATCH and PUT for respectively updating and replacing multiple documents works the same way  
  
  
                curl_slist_free_all(headers);  
                curl_easy_cleanup(curl);  
                curl_global_cleanup();  
  
  
#endif  
  
#if 1  
                std::string s;  
                std::string &str = s;  
  
                jsonBodyBuilder *jb = new jsonBodyBuilder();  
                jb->startBodyBuild("Hello", "world");  
                jb->appendBody("key", 7);  
                jb->appendBody("stat", 10L);  
                jb->appendBody("source", 4.667);  
                jb->appendBody(9L, 6.221);  
                jb->finishBody(s);  
  
                std::cout << s << std::endl;  
                delete jb;  
  
#endif  
  
  
                return 0;  
  
}  
  
  
  
  
  
  
  
From: Vinnie Falco <notifications@github.com>  
Sent: Tuesday, September 08, 2020 10:56 AM  
To: CPPAlliance/json <json@noreply.github.com>  
Cc: Joe Stroupe <joes@altia.com>; Author <author@noreply.github.com>  
Subject: Re: [CPPAlliance/json] json Conversions to/from C++ objects (#223)  
  
  
I don't see any zip file, can you paste the minimal sample program here?  
  
—  
You are receiving this because you authored the thread.  
Reply to this email directly, view it on GitHub<https://urldefense.proofpoint.com/v2/url?u=https-3A__github.com_CPPAlliance_json_issues_223-23issuecomment-2D689009305&d=DwMCaQ&c=euGZstcaTDllvimEN8b7jXrwqOf-v5A_CdpgnVfiiMM&r=xesY1ER9OAQvsJFpLd_0fw&m=0JlOknucRpLSuzDPgwxVGH9X7wGBhNMqMSDMKWH-dsk&s=jlrINfgD-KKiQHiYOEYLp4NQpQRFRkopeMc36bvKDvU&e=>, or unsubscribe<https://urldefense.proofpoint.com/v2/url?u=https-3A__github.com_notifications_unsubscribe-2Dauth_ACAKC22ORWMSNBP2XG46NCDSEZO2ZANCNFSM4QOJ33JA&d=DwMCaQ&c=euGZstcaTDllvimEN8b7jXrwqOf-v5A_CdpgnVfiiMM&r=xesY1ER9OAQvsJFpLd_0fw&m=0JlOknucRpLSuzDPgwxVGH9X7wGBhNMqMSDMKWH-dsk&s=c-RO40rUkA_iRGSz25P4AhdCSF8caMXutf-A5M3rXms&e=>.

---

## Comment 21

> Username: vinniefalco  
> Created at: 2020-09-08 17:07:33 UTC  
> Url: https://github.com/boostorg/json/issues/223#issuecomment-689015707  

can you please reduce that down to a small program, with `main`, of just a few lines, that reproduces the problem? In other words a minimal reproducible example.

---

## Comment 22

> Username: JoeStroupe  
> Created at: 2020-09-08 17:08:44 UTC  
> Url: https://github.com/boostorg/json/issues/223#issuecomment-689016432  

Hi,  
  
Sure – give me a few minutes.  
  
  
From: Vinnie Falco <notifications@github.com>  
Sent: Tuesday, September 08, 2020 11:08 AM  
To: CPPAlliance/json <json@noreply.github.com>  
Cc: Joe Stroupe <joes@altia.com>; Author <author@noreply.github.com>  
Subject: Re: [CPPAlliance/json] json Conversions to/from C++ objects (#223)  
  
  
can you please reduce that down to a small program, with main, of just a few lines, that reproduces the problem? In other words a minimal reproducible example.  
  
—  
You are receiving this because you authored the thread.  
Reply to this email directly, view it on GitHub<https://urldefense.proofpoint.com/v2/url?u=https-3A__github.com_CPPAlliance_json_issues_223-23issuecomment-2D689015707&d=DwMCaQ&c=euGZstcaTDllvimEN8b7jXrwqOf-v5A_CdpgnVfiiMM&r=xesY1ER9OAQvsJFpLd_0fw&m=bOBTc4SN3MHRELe0esKjI0HZVw3H4ha3MPxNG2p82iA&s=dNyJfeKV8y6NYzWfMhUynlvQNuwEj7cZ629kPsRLetU&e=>, or unsubscribe<https://urldefense.proofpoint.com/v2/url?u=https-3A__github.com_notifications_unsubscribe-2Dauth_ACAKC26QXU62JDART5JUJ2TSEZQGJANCNFSM4QOJ33JA&d=DwMCaQ&c=euGZstcaTDllvimEN8b7jXrwqOf-v5A_CdpgnVfiiMM&r=xesY1ER9OAQvsJFpLd_0fw&m=bOBTc4SN3MHRELe0esKjI0HZVw3H4ha3MPxNG2p82iA&s=PORZTu3jxiefsAyXxlP_DPNhcf55VeB7nXHaDJpCs5c&e=>.

---

## Comment 23

> Username: JoeStroupe  
> Created at: 2020-09-08 17:30:06 UTC  
> Url: https://github.com/boostorg/json/issues/223#issuecomment-689027641  

#include <vector>  
#include <string>  
#include <stdio.h>  
#include <stdlib.h>  
#include <iomanip>  
#include <iostream>  
  
#include <boost/json/src.hpp>  
  
// Pseudo-pair object  
struct reqPair  
{  
                std::string name;  
                std::string value;  
};  
  
  
using namespace boost::json;  
  
#define CURL_PAIR_SEPARATOR               "\":\""  
  
template <class T>  
void  
tag_invoke(const value_from_tag&, value& jval, T const& t)  
{  
                jval = { t->name + CURL_PAIR_SEPARATOR + t->value };  
}  
  
  
int main()  
{  
                // vector to hold reqPair structure pointers  
                std::vector<reqPair *>   ReqV;  
  
                std::string str;  
  
                // populate a reqPair structure, then push a pointer to it into the vector  
                reqPair *newPair_1 = new reqPair;  
                newPair_1->name = "Hello";  
                newPair_1->value = "World";  
                ReqV.push_back(newPair_1);  
  
                // populate another reqPair structure, then push a pointer to it into the vector  
                reqPair *newPair_2 = new reqPair;  
                newPair_2->name = "Goodbye";  
                newPair_2->value = "World";  
                ReqV.push_back(newPair_2);  
  
                // Do the recursive convert from reqPair structure to json  
                value requestBodyjs = boost::json::value_from(ReqV);  
                // Serialize it  
                str = boost::json::serialize(requestBodyjs);  
  
                // Let's see what the serialized string looks like  
                std::cout << str << std::endl;  
  
                // Clean up things  
                for (auto p : ReqV)  
                                                delete p;  
                                ReqV.clear();  
  
                return 0;  
  
}  
  
  
From: Vinnie Falco <notifications@github.com>  
Sent: Tuesday, September 08, 2020 11:08 AM  
To: CPPAlliance/json <json@noreply.github.com>  
Cc: Joe Stroupe <joes@altia.com>; Author <author@noreply.github.com>  
Subject: Re: [CPPAlliance/json] json Conversions to/from C++ objects (#223)  
  
  
can you please reduce that down to a small program, with main, of just a few lines, that reproduces the problem? In other words a minimal reproducible example.  
  
—  
You are receiving this because you authored the thread.  
Reply to this email directly, view it on GitHub<https://urldefense.proofpoint.com/v2/url?u=https-3A__github.com_CPPAlliance_json_issues_223-23issuecomment-2D689015707&d=DwMCaQ&c=euGZstcaTDllvimEN8b7jXrwqOf-v5A_CdpgnVfiiMM&r=xesY1ER9OAQvsJFpLd_0fw&m=bOBTc4SN3MHRELe0esKjI0HZVw3H4ha3MPxNG2p82iA&s=dNyJfeKV8y6NYzWfMhUynlvQNuwEj7cZ629kPsRLetU&e=>, or unsubscribe<https://urldefense.proofpoint.com/v2/url?u=https-3A__github.com_notifications_unsubscribe-2Dauth_ACAKC26QXU62JDART5JUJ2TSEZQGJANCNFSM4QOJ33JA&d=DwMCaQ&c=euGZstcaTDllvimEN8b7jXrwqOf-v5A_CdpgnVfiiMM&r=xesY1ER9OAQvsJFpLd_0fw&m=bOBTc4SN3MHRELe0esKjI0HZVw3H4ha3MPxNG2p82iA&s=PORZTu3jxiefsAyXxlP_DPNhcf55VeB7nXHaDJpCs5c&e=>.

---

## Comment 24

> Username: vinniefalco  
> Created at: 2020-09-08 17:56:33 UTC  
> Url: https://github.com/boostorg/json/issues/223#issuecomment-689041072  

Your program works fine for me, I get this output   
![image](https://user-images.githubusercontent.com/1503976/92511288-f3d49980-f1c1-11ea-8faa-144f396d776d.png)

---

## Comment 25

> Username: JoeStroupe  
> Created at: 2020-09-08 18:15:08 UTC  
> Url: https://github.com/boostorg/json/issues/223#issuecomment-689050456  

Here’s the output I’m looking for, and that matches what an HTTP json body looks like:  
  
"{\"Hello\":\"World\", \"Goodbye\":\"World\"}"  
  
This format matches what’s on your conversion page in the docs.  
  
I can get this format with my own custom code, but not with your json code. Those ‘[‘ ‘]’ brkts and the lack of ‘\’ C-style escape in some places preceding double quotes, and no enclosing ‘{‘  ‘}’ curly braces will make the format unusable in libcurl unless I do my custom reformat after the json serialize, as it now stands.  
  
I’m wondering why this is so because your docs on the serialize page have the correct format I’m looking for – no square brkts and no missing ‘\’ escape chars preceding any of the double-quote “ chars, and entire body enclosed in ‘{‘  ‘}’ curly braces.  
  
William  
  
  
From: Vinnie Falco <notifications@github.com>  
Sent: Tuesday, September 08, 2020 11:57 AM  
To: CPPAlliance/json <json@noreply.github.com>  
Cc: Joe Stroupe <joes@altia.com>; Author <author@noreply.github.com>  
Subject: Re: [CPPAlliance/json] json Conversions to/from C++ objects (#223)  
  
  
Your program works fine for me, I get this output  
[image]<https://urldefense.proofpoint.com/v2/url?u=https-3A__user-2Dimages.githubusercontent.com_1503976_92511288-2Df3d49980-2Df1c1-2D11ea-2D8faa-2D144f396d776d.png&d=DwMCaQ&c=euGZstcaTDllvimEN8b7jXrwqOf-v5A_CdpgnVfiiMM&r=xesY1ER9OAQvsJFpLd_0fw&m=OW-6JEMkjMdvzu_XWB8XxFcGTwVQa89uFKmPJLiuHd4&s=w3xxmsujPTaXHyiIk0LiTl-OzH-iMaOecr3IwEbFay0&e=>  
  
—  
You are receiving this because you authored the thread.  
Reply to this email directly, view it on GitHub<https://urldefense.proofpoint.com/v2/url?u=https-3A__github.com_CPPAlliance_json_issues_223-23issuecomment-2D689041072&d=DwMCaQ&c=euGZstcaTDllvimEN8b7jXrwqOf-v5A_CdpgnVfiiMM&r=xesY1ER9OAQvsJFpLd_0fw&m=OW-6JEMkjMdvzu_XWB8XxFcGTwVQa89uFKmPJLiuHd4&s=2GUiL1DiVxgLBn6FzNqaE3mLNsA9ppa3cx-V9wZWD2E&e=>, or unsubscribe<https://urldefense.proofpoint.com/v2/url?u=https-3A__github.com_notifications_unsubscribe-2Dauth_ACAKC2YQA2HVOJBFNI5KWDDSEZV6BANCNFSM4QOJ33JA&d=DwMCaQ&c=euGZstcaTDllvimEN8b7jXrwqOf-v5A_CdpgnVfiiMM&r=xesY1ER9OAQvsJFpLd_0fw&m=OW-6JEMkjMdvzu_XWB8XxFcGTwVQa89uFKmPJLiuHd4&s=KWM1w0haw-63LxByPOvwUE4-42ZKeqijV37GJj1j6N0&e=>.

---

## Comment 26

> Username: vinniefalco  
> Created at: 2020-09-08 18:32:02 UTC  
> Url: https://github.com/boostorg/json/issues/223#issuecomment-689058761  

> jval = { t->name + CURL_PAIR_SEPARATOR + t->value };  
  
Well, what you are doing here is creating an array of one-element arrays each containing a string. If you want an object with key/value pairs then you need something resembling a map. This is one way to do it:  
```  
int main()  
{  
    std::map<std::string, std::string> ReqV;  
    ReqV.emplace( "Hello", "World" );  
    ReqV.emplace( "Goodbye", "World" );  
    value requestBodyjs = boost::json::value_from(ReqV);  
    std::string str = boost::json::serialize(requestBodyjs);  
    std::cout << str << std::endl;  
    return 0;  
}  
```

---

## Comment 27

> Username: JoeStroupe  
> Created at: 2020-09-08 20:17:44 UTC  
> Url: https://github.com/boostorg/json/issues/223#issuecomment-689112788  

He Vinnie,  
  
A map isn’t going to work for me – there’s a stinking bug in VS 2015 still until now where a std::map<std::string, std::string> doesn’t compile, because the 2nd parameter as a string isn’t working – it wants an int or other numeric field. I’ve Googled it and it’s a mess that shows up in json stuff. So I’m not going there!  
  
What I have is working fine for my needs.  
  
Next I need to move on to conversions from a json body that’s embedded in an HTTP response to a user object, like a simple struct such as I’m using in the user type to json conversion now. So that’s the convert_to() side of things.  
  
Let me delve into this and see how far I can get w/o getting stuck.  
  
William  
  
  
  
From: Vinnie Falco <notifications@github.com>  
Sent: Tuesday, September 08, 2020 12:32 PM  
To: CPPAlliance/json <json@noreply.github.com>  
Cc: Joe Stroupe <joes@altia.com>; Author <author@noreply.github.com>  
Subject: Re: [CPPAlliance/json] json Conversions to/from C++ objects (#223)  
  
  
jval = { t->name + CURL_PAIR_SEPARATOR + t->value };  
  
Well, what you are doing here is creating an array of one-element arrays each containing a string. If you want an object with key/value pairs then you need something resembling a map. This is one way to do it:  
  
int main()  
  
{  
  
    std::map<std::string, std::string> ReqV;  
  
    ReqV.emplace( "Hello", "World" );  
  
    ReqV.emplace( "Goodbye", "World" );  
  
    value requestBodyjs = boost::json::value_from(ReqV);  
  
    std::string str = boost::json::serialize(requestBodyjs);  
  
    std::cout << str << std::endl;  
  
    return 0;  
  
}  
  
—  
You are receiving this because you authored the thread.  
Reply to this email directly, view it on GitHub<https://urldefense.proofpoint.com/v2/url?u=https-3A__github.com_CPPAlliance_json_issues_223-23issuecomment-2D689058761&d=DwMCaQ&c=euGZstcaTDllvimEN8b7jXrwqOf-v5A_CdpgnVfiiMM&r=xesY1ER9OAQvsJFpLd_0fw&m=y-izojFN4fKdeA5A92UwE166OfWQU0-x_q5RgngES8U&s=FOyzPnKl2t_N_nJDmY9DdICHWvZH2Kn26hJToTW4iac&e=>, or unsubscribe<https://urldefense.proofpoint.com/v2/url?u=https-3A__github.com_notifications_unsubscribe-2Dauth_ACAKC22SFKOHAP5UTPUAAHLSEZ2DDANCNFSM4QOJ33JA&d=DwMCaQ&c=euGZstcaTDllvimEN8b7jXrwqOf-v5A_CdpgnVfiiMM&r=xesY1ER9OAQvsJFpLd_0fw&m=y-izojFN4fKdeA5A92UwE166OfWQU0-x_q5RgngES8U&s=iSTX8YFYdvSgEIkMdjg2Aiu4OOVL0LZiWOVm7zpZlbs&e=>.

---

## Comment 28

> Username: vinniefalco  
> Created at: 2020-09-08 20:19:25 UTC  
> Url: https://github.com/boostorg/json/issues/223#issuecomment-689113593  

>  there’s a stinking bug in VS 2015  
  
Try `boost::container::map`

---

## Comment 29

> Username: JoeStroupe  
> Created at: 2020-09-09 00:58:10 UTC  
> Url: https://github.com/boostorg/json/issues/223#issuecomment-689233128  

I’m having trouble getting any code to recognize the value_to() function – the VS project thinks that value_to is not a member of boost::json. Then also, value_to_tag in tag_invoke() is not recognized either – unknown identifier.  
  
What am I missing here? The .h files do define value_to.  
  
William  
  
  
From: Vinnie Falco <notifications@github.com>  
Sent: Tuesday, September 08, 2020 2:20 PM  
To: CPPAlliance/json <json@noreply.github.com>  
Cc: Joe Stroupe <joes@altia.com>; Author <author@noreply.github.com>  
Subject: Re: [CPPAlliance/json] json Conversions to/from C++ objects (#223)  
  
  
there’s a stinking bug in VS 2015  
  
Try boost::container::map  
  
—  
You are receiving this because you authored the thread.  
Reply to this email directly, view it on GitHub<https://urldefense.proofpoint.com/v2/url?u=https-3A__github.com_CPPAlliance_json_issues_223-23issuecomment-2D689113593&d=DwMFaQ&c=euGZstcaTDllvimEN8b7jXrwqOf-v5A_CdpgnVfiiMM&r=xesY1ER9OAQvsJFpLd_0fw&m=jLNcfv3cBxRRb3l3DHaCXG0NUqxvJuu5TyKQxDLJIJ0&s=wMLiIP7EgnbzuYrZ29WLUll2c_0nVWR4lCuiTRAayLI&e=>, or unsubscribe<https://urldefense.proofpoint.com/v2/url?u=https-3A__github.com_notifications_unsubscribe-2Dauth_ACAKC27OYZ6XWCLZWPB3BTTSE2GVXANCNFSM4QOJ33JA&d=DwMFaQ&c=euGZstcaTDllvimEN8b7jXrwqOf-v5A_CdpgnVfiiMM&r=xesY1ER9OAQvsJFpLd_0fw&m=jLNcfv3cBxRRb3l3DHaCXG0NUqxvJuu5TyKQxDLJIJ0&s=f76MS8H3ZaUJVdy8RFjbbA_zzakS7mc5tf5gLIEJQ7E&e=>.

---

## Comment 30

> Username: vinniefalco  
> Created at: 2020-09-09 01:05:21 UTC  
> Url: https://github.com/boostorg/json/issues/223#issuecomment-689235222  

See: https://stackoverflow.com/help/minimal-reproducible-example

---

## Comment 31

> Username: JoeStroupe  
> Created at: 2020-09-09 02:08:39 UTC  
> Url: https://github.com/boostorg/json/issues/223#issuecomment-689253635  

Here’s my attempt at trying to figure out how to use the value_to() function. This won’t compile because value_to and value_to_tag are not found in boost::json.  
  
  
#include <vector>  
#include <string>  
#include <stdio.h>  
#include <stdlib.h>  
#include <boost/json/src.hpp>  
  
using namespace boost::json;  
  
  
struct reqPair  
{  
                std::string name;  
                std::string value;  
};  
  
  
  
template< class T >  
T tag_invoke(value_to_tag<T>&, value const& jv)  
{  
                T vec = { jv.as_array().at(0), jv.as_array().at(1) };  
                return vec;  
}  
  
  
int main()  
{  
                value js = { "Hello World", "Goodbye World" };  
                std::vector<reqPair> ReqV;  
  
                value requestBodyjs = { "Hello World", "Goodbye World" };  
  
                ReqV = boost::json::value_to<std::vector<reqPair>>(requestBodyjs);  
                ;  
                ;  
}  
  
  
From: Vinnie Falco <notifications@github.com>  
Sent: Tuesday, September 08, 2020 7:06 PM  
To: CPPAlliance/json <json@noreply.github.com>  
Cc: Joe Stroupe <joes@altia.com>; Author <author@noreply.github.com>  
Subject: Re: [CPPAlliance/json] json Conversions to/from C++ objects (#223)  
  
  
See: https://stackoverflow.com/help/minimal-reproducible-example<https://urldefense.proofpoint.com/v2/url?u=https-3A__stackoverflow.com_help_minimal-2Dreproducible-2Dexample&d=DwMCaQ&c=euGZstcaTDllvimEN8b7jXrwqOf-v5A_CdpgnVfiiMM&r=xesY1ER9OAQvsJFpLd_0fw&m=pfvFUQfTuJ0m5v0DsUM7YmFHm3btBXsafrb9LjS28ew&s=Qj7uvn4bXhbqUEw0tKjYONYKXPJZ6XA5BmSipJVbALs&e=>  
  
—  
You are receiving this because you authored the thread.  
Reply to this email directly, view it on GitHub<https://urldefense.proofpoint.com/v2/url?u=https-3A__github.com_CPPAlliance_json_issues_223-23issuecomment-2D689235222&d=DwMCaQ&c=euGZstcaTDllvimEN8b7jXrwqOf-v5A_CdpgnVfiiMM&r=xesY1ER9OAQvsJFpLd_0fw&m=pfvFUQfTuJ0m5v0DsUM7YmFHm3btBXsafrb9LjS28ew&s=gX94eJ39nkZitFt86QSkKyXQ4kPBvqI4YR8E6SIJs5I&e=>, or unsubscribe<https://urldefense.proofpoint.com/v2/url?u=https-3A__github.com_notifications_unsubscribe-2Dauth_ACAKC23474XVTPNAV5XQJ7TSE3IF5ANCNFSM4QOJ33JA&d=DwMCaQ&c=euGZstcaTDllvimEN8b7jXrwqOf-v5A_CdpgnVfiiMM&r=xesY1ER9OAQvsJFpLd_0fw&m=pfvFUQfTuJ0m5v0DsUM7YmFHm3btBXsafrb9LjS28ew&s=KBORQF2aKXrib3R4QnsoXlxZM-JZUtfV_Rptjp_tfCE&e=>.

---

## Comment 32

> Username: vinniefalco  
> Created at: 2020-09-09 02:39:02 UTC  
> Url: https://github.com/boostorg/json/issues/223#issuecomment-689262470  

You are including src.hpp which is required for linking, but you are not including the headers which give you all the declarations:  
```  
#Include <boost/json.hpp>  
```  
  
Alternatively you can include just what you need:  
```  
#Include <boost/json/value_to.hpp>  
#Include <boost/json/value_from.hpp>  
```

---

## Comment 33

> Username: JoeStroupe  
> Created at: 2020-09-09 02:43:46 UTC  
> Url: https://github.com/boostorg/json/issues/223#issuecomment-689264149  

I see – made the assumption that src.hpp included all I needed – obviously a dumb assumption!  
  
Thx for your help!  
  
From: Vinnie Falco <notifications@github.com>  
Sent: Tuesday, September 08, 2020 8:39 PM  
To: CPPAlliance/json <json@noreply.github.com>  
Cc: Joe Stroupe <joes@altia.com>; Author <author@noreply.github.com>  
Subject: Re: [CPPAlliance/json] json Conversions to/from C++ objects (#223)  
  
  
You are including src.hpp which is required for linking, but you are not including the headers which give you all the declarations:  
  
#Include <boost/json.hpp>  
  
Alternatively you can include just what you need:  
  
#Include <boost/json/value_to.hpp>  
  
#Include <boost/json/value_from.hpp>  
  
—  
You are receiving this because you authored the thread.  
Reply to this email directly, view it on GitHub<https://urldefense.proofpoint.com/v2/url?u=https-3A__github.com_CPPAlliance_json_issues_223-23issuecomment-2D689262470&d=DwMCaQ&c=euGZstcaTDllvimEN8b7jXrwqOf-v5A_CdpgnVfiiMM&r=xesY1ER9OAQvsJFpLd_0fw&m=hBHOyZCpRCZKmkjGlYPWnLzmDMhwaKLjvuk-uY3Szak&s=JNEqKlxfeo9A8x4wd8s0GEgeYJnBDwf1e_yxS-1Pbes&e=>, or unsubscribe<https://urldefense.proofpoint.com/v2/url?u=https-3A__github.com_notifications_unsubscribe-2Dauth_ACAKC253QDJMUIHB224WELDSE3TFHANCNFSM4QOJ33JA&d=DwMCaQ&c=euGZstcaTDllvimEN8b7jXrwqOf-v5A_CdpgnVfiiMM&r=xesY1ER9OAQvsJFpLd_0fw&m=hBHOyZCpRCZKmkjGlYPWnLzmDMhwaKLjvuk-uY3Szak&s=Ol6iK7RSlT4yGQZLjCPOLW7wi6QBqJjFUIqMOi2GUj8&e=>.
