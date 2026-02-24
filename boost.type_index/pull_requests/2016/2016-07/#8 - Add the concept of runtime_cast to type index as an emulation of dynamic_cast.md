# #8 Add the concept of runtime_cast to type index as an emulation of dynamic_cast.. [Merged]

> Username: cdglove  
> Created at: 2016-07-23 13:53:38 UTC  
> Updated at: 2016-09-28 20:45:39 UTC  
> Merged at: 2016-09-28 20:45:39 UTC  
> Closed at: 2016-09-28 20:45:39 UTC  
> Url: https://github.com/boostorg/type_index/pull/8  

I sent an email to the boost mailing list about this and received little response.  
  
http://thread.gmane.org/gmane.comp.lib.boost.devel/268139  
  
Is there an interest in adding this to typeindex, or maybe it should be a seperate library? For me its incredibly useful.

---

## Comment 1

> Username: apolukhin  
> Created_at: 2016-07-24 15:05:42 UTC  
> Updated_at: 2016-08-21 16:48:15 UTC  
> Url: https://github.com/boostorg/type_index/pull/8#discussion_r71990119  

Can we remove this macro and use only the BOOST_TYPE_INDEX_REGISTER_CLASS_RTTI_BASES macro?

---

## Comment 2

> Username: apolukhin  
> Created_at: 2016-07-24 15:07:01 UTC  
> Updated_at: 2016-08-21 16:48:15 UTC  
> Url: https://github.com/boostorg/type_index/pull/8#discussion_r71990138  

It's better to use boost::is_same, because it works even on antique compilers and c++98 compatible standard libraries

---

## Comment 3

> Username: apolukhin  
> Created_at: 2016-07-24 15:08:17 UTC  
> Updated_at: 2016-08-21 16:48:15 UTC  
> Url: https://github.com/boostorg/type_index/pull/8#discussion_r71990172  

Do we really need &lt;typeinfo&gt; header?

---

## Comment 4

> Username: apolukhin  
> Created_at: 2016-07-24 15:09:44 UTC  
> Updated_at: 2016-08-21 16:48:15 UTC  
> Url: https://github.com/boostorg/type_index/pull/8#discussion_r71990195  

`std::true_type` and `std::false_type` may not be available at all the compilers. It's better to use Boost's alternatives

---

## Comment 5

> Username: apolukhin  
> Created_at: 2016-07-24 15:12:22 UTC  
> Updated_at: 2016-08-21 16:48:15 UTC  
> Url: https://github.com/boostorg/type_index/pull/8#discussion_r71990239  

It may be profitable to use `is_base_and_derived` here too. In that case upcasts will cost nothing.

---

## Comment 6

> Username: apolukhin  
> Created_at: 2016-07-24 15:20:56 UTC  
> Url: https://github.com/boostorg/type_index/pull/8#issuecomment-234782983  

Looks interesting! Great idea.  
  
I've added a few comments. I'm short on time right now, so if you wish to fix those issues - you're welcome :) Otherwise, I'll fix them by myself somewhere around October.  
  
Also we'll need docs and reduce C++14 and C++11 usage (get rid of `decltype` and variadic templates if possible).

---

## Comment 7

> Username: cdglove  
> Created_at: 2016-07-24 18:25:07 UTC  
> Url: https://github.com/boostorg/type_index/pull/8#issuecomment-234793271  

I can make the changes and write the docs. This pull request was mostly to gauge interest and since you seem to be interested, I will take care of it.   
  
One thing I haven't included are overloads of runtime_pointer_cast for the smart pointer types. I think these should be included but packaged in separate headers. Thoughts?

---

## Comment 8

> Username: cdglove  
> Created_at: 2016-07-24 22:03:20 UTC  
> Updated_at: 2016-08-21 16:48:15 UTC  
> Url: https://github.com/boostorg/type_index/pull/8#discussion_r71996483  

It's for std::bad_cast which I need for the reference versions (since they can't return nullptr). An alternative is to use a different exception type or move the reference versions to a different header.

---

## Comment 9

> Username: cdglove  
> Created_at: 2016-07-24 22:03:29 UTC  
> Updated_at: 2016-08-21 16:48:15 UTC  
> Url: https://github.com/boostorg/type_index/pull/8#discussion_r71996484  

OK

---

## Comment 10

> Username: cdglove  
> Created_at: 2016-07-24 22:05:37 UTC  
> Updated_at: 2016-08-21 16:48:15 UTC  
> Url: https://github.com/boostorg/type_index/pull/8#discussion_r71996499  

Good call. Thanks!

---

## Comment 11

> Username: cdglove  
> Created_at: 2016-07-24 22:05:52 UTC  
> Updated_at: 2016-08-21 16:48:15 UTC  
> Url: https://github.com/boostorg/type_index/pull/8#discussion_r71996502  

OK

---

## Comment 12

> Username: cdglove  
> Created_at: 2016-07-24 22:09:32 UTC  
> Updated_at: 2016-08-21 16:48:15 UTC  
> Url: https://github.com/boostorg/type_index/pull/8#discussion_r71996576  

Yes, I think I can get this down to a single macro when I remove the variadic template. Do you recommend also not relying on variadic macros? If so, I'll need to provide two implementations (one with variadic macros and one with BOOST_TYPE_INDEX_REGISTER_CLASS_RTTI_BASES0, BOOST_TYPE_INDEX_REGISTER_CLASS_RTTI_BASES1, BOOST_TYPE_INDEX_REGISTER_CLASS_RTTI_BASES2, etc).

---

## Comment 13

> Username: cdglove  
> Created_at: 2016-08-08 16:21:24 UTC  
> Updated_at: 2016-08-21 16:48:15 UTC  
> Url: https://github.com/boostorg/type_index/pull/8#discussion_r73906080  

I can think of a few ways to get rid of this macro, and only have one, but to do so would, I think, require the user listing the name of the class something like;  
  
``` cpp  
struct derived : base1, base2 {  
  BOOST_TYPE_INDEX_REGISTER_CLASS_RTTI((derived)(base1)(base2))  
};  
```  
  
I'm not sure if that's preferable to having 2 macros or not. I'm also not super sold on the BOOST PP SEQUENCE syntax that's required now to list the classes involved. I would prefer a comma separated list, but well, preprocessor limitations. Thoughts?

---

## Comment 14

> Username: cdglove  
> Created_at: 2016-08-08 16:26:44 UTC  
> Updated_at: 2016-08-08 16:27:09 UTC  
> Url: https://github.com/boostorg/type_index/pull/8#issuecomment-238291204  

I've changed the code, removed the std::type_traits and the variadic template and replaced it with a version using BOOST_PP. I don't like dragging in the preprocessor lib, it seems a little heavy for this, but my macro skills are not really up to the task otherwise ;-). Any feedback is welcome.

---

## Comment 15

> Username: cdglove  
> Created_at: 2016-08-11 15:26:28 UTC  
> Url: https://github.com/boostorg/type_index/pull/8#issuecomment-239196025  

I've added an initial version of some docs. There are a few things that came out of writing the docs that I I don't like with the current implementation.  
  
i. I don't like having 2 macros, BOOST_TYPE_INDEX_REGISTER_CLASS and BOOST_TYPE_INDEX_REGISTER_RUNTIME_CLASS. I feel like there should only be one, or they can remain separate but there should be a master macro that does both by default. Or maybe BOOST_TYPE_INDEX_REGISTER_CLASS_EX that takes the base class list.  
  
ii. I don't like that to use BOOST_TYPE_INDEX_REGISTER_RUNTIME_CLASS requires an additional include. I did this because it drags in boost/preprocessor/seq/for_each.hpp and I want to keep that out of the primary header.  
  
iii. Actually, I'd like to find a solution that doesn't depend on BOOST_PP. I don't love the need for the ((base1)(base2)) syntax that PP sequences require.  
  
I'd appreciate any suggestions on how to move forward.  What are your thoughts on using variadic macros?

---

## Comment 16

> Username: apolukhin  
> Created_at: 2016-08-16 04:41:24 UTC  
> Updated_at: 2016-08-21 16:48:15 UTC  
> Url: https://github.com/boostorg/type_index/pull/8#discussion_r74875225  

use boost::addressof(u) instead of &u

---

## Comment 17

> Username: apolukhin  
> Created_at: 2016-08-16 04:42:34 UTC  
> Updated_at: 2016-08-21 16:48:15 UTC  
> Url: https://github.com/boostorg/type_index/pull/8#discussion_r74875289  

I'm not sure that <typeinfo> header is available on all platforms when RTTI is off and I'm sure that std::bad_cast() is not available when defined BOOST_NO_RTTI. How about making a boost::bad_runtime_cast exception that derived from std::exception?

---

## Comment 18

> Username: apolukhin  
> Created_at: 2016-08-16 04:43:07 UTC  
> Updated_at: 2016-08-21 16:48:15 UTC  
> Url: https://github.com/boostorg/type_index/pull/8#discussion_r74875316  

nullptr is not available on pre C++11 compilers. better to use 0 or NULL

---

## Comment 19

> Username: apolukhin  
> Created_at: 2016-08-16 04:50:28 UTC  
> Updated_at: 2016-08-21 16:48:15 UTC  
> Url: https://github.com/boostorg/type_index/pull/8#discussion_r74875755  

boost/type_index.hpp must include boost/type_index/runtime_cast.hpp + there may be a problem with circular inclusions because most of the boost/type_index/runtime_cast/*.hpp headers include boost/type_index.hpp

---

## Comment 20

> Username: apolukhin  
> Created_at: 2016-08-16 04:51:04 UTC  
> Updated_at: 2016-08-21 16:48:15 UTC  
> Url: https://github.com/boostorg/type_index/pull/8#discussion_r74875782  

boost::addressof(u)

---

## Comment 21

> Username: apolukhin  
> Created_at: 2016-08-16 04:57:45 UTC  
> Url: https://github.com/boostorg/type_index/pull/8#issuecomment-240001222  

> it drags in boost/preprocessor/seq/for_each.hpp and I want to keep that out of the primary header.  
  
That would be good, but I'm not sure how to do it better. I've made a few experiments with `template <class B0 = na, B1 = na, B2 = na, B3 = na> class bases` but that does not seem good. So looks like preprocessor library is OK here.

---

## Comment 22

> Username: apolukhin  
> Created_at: 2016-08-16 04:59:21 UTC  
> Url: https://github.com/boostorg/type_index/pull/8#issuecomment-240001390  

I've added a few issues to fix. Fix those and I'll merge the pull request.

---

## Comment 23

> Username: cdglove  
> Created_at: 2016-08-16 13:15:38 UTC  
> Updated_at: 2016-08-21 16:48:15 UTC  
> Url: https://github.com/boostorg/type_index/pull/8#discussion_r74931518  

Currently, boost/type_index.hpp is not including runtime_cast.hpp. If a user is using type_index wants the runtime_cast functionality, I expect that user to include runtime_cast.hpp separately. I personally like finer grained headers like this, but I realize it can be a pain and some people don't like it so I'm happy to change it around so that type_index brings in the basic runtime_cast functionality as well. It's really not a lot of code, more on that below.

---

## Comment 24

> Username: cdglove  
> Created_at: 2016-08-16 13:17:12 UTC  
> Updated_at: 2016-08-21 16:48:15 UTC  
> Url: https://github.com/boostorg/type_index/pull/8#discussion_r74931802  

Yes, I think you are right -- I'll create a new exception class. Question: Should the throw be using the BOOST_THROW_EXCEPTION macro or the boost::throw_exception function?

---

## Comment 25

> Username: cdglove  
> Created_at: 2016-08-16 13:17:26 UTC  
> Updated_at: 2016-08-21 16:48:15 UTC  
> Url: https://github.com/boostorg/type_index/pull/8#discussion_r74931846  

Right. Thanks!

---

## Comment 26

> Username: cdglove  
> Created_at: 2016-08-16 13:17:33 UTC  
> Updated_at: 2016-08-21 16:48:15 UTC  
> Url: https://github.com/boostorg/type_index/pull/8#discussion_r74931860  

OK.

---

## Comment 27

> Username: cdglove  
> Created_at: 2016-08-16 13:18:35 UTC  
> Updated_at: 2016-08-21 16:48:15 UTC  
> Url: https://github.com/boostorg/type_index/pull/8#discussion_r74932004  

OK. For some reason I thought this would be OK in tests, but of course we want the test suite to also run on older compilers so I'll fix this up.

---

## Comment 28

> Username: cdglove  
> Created_at: 2016-08-16 13:27:05 UTC  
> Url: https://github.com/boostorg/type_index/pull/8#issuecomment-240100761  

> That would be good, but I'm not sure how to do it better.  
  
At some point I wrote a tool to analyze include file size. Though not a perfect indicator of compile time impacts, I find it to be a reasonably good precursor. When I run that tool on type_index with the preprocessor library here, it doesn't look too bad; only 140kb of additional code.  
  
![pasted2](https://cloud.githubusercontent.com/assets/1856042/17700273/1e5b2df2-6393-11e6-9ad6-0ef6eb988de6.png)  
  
So, I think this is an OK solution. It also means I think we can reorganize the header such that runtime_cast.hpp is unnecessary and type_index.hpp just includes the basic bits from the runtime_cast directory (essentially everything except the smart ptr overloads).

---
