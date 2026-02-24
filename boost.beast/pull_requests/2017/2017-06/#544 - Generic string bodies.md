# #544 Generic string bodies [Closed]

> Username: octopus-prime  
> Created at: 2017-06-26 15:10:51 UTC  
> Updated at: 2017-06-26 20:51:36 UTC  
> Closed at: 2017-06-26 20:51:36 UTC  
> Url: https://github.com/boostorg/beast/pull/544  

Pull request for #536

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2017-06-26 15:43:59 UTC  
> Updated_at: 2017-06-26 19:59:05 UTC  
> Url: https://github.com/boostorg/beast/pull/544#issuecomment-311098394  

# [Codecov](https://codecov.io/gh/vinniefalco/Beast/pull/544?src=pr&el=h1) Report  
> Merging [#544](https://codecov.io/gh/vinniefalco/Beast/pull/544?src=pr&el=desc) into [master](https://codecov.io/gh/vinniefalco/Beast/commit/8982e14aa65b9922ac5a00e5a5196a08dfa8f29e?src=pr&el=desc) will **increase** coverage by `0.02%`.  
> The diff coverage is `100%`.  
  
[![Impacted file tree graph](https://codecov.io/gh/vinniefalco/Beast/pull/544/graphs/tree.svg?src=pr&token=Gq6MFA9JRF&width=650&height=150)](https://codecov.io/gh/vinniefalco/Beast/pull/544?src=pr&el=tree)  
  
```diff  
@@            Coverage Diff            @@  
##           master    #544      +/-   ##  
=========================================  
+ Coverage   93.67%   93.7%   +0.02%       
=========================================  
  Files          93      94       +1       
  Lines        7213    7113     -100       
=========================================  
- Hits         6757    6665      -92       
+ Misses        456     448       -8  
```  
  
  
| [Impacted Files](https://codecov.io/gh/vinniefalco/Beast/pull/544?src=pr&el=tree) | Coverage Δ | |  
|---|---|---|  
| [include/beast/core/string.hpp](https://codecov.io/gh/vinniefalco/Beast/pull/544?src=pr&el=tree#diff-aW5jbHVkZS9iZWFzdC9jb3JlL3N0cmluZy5ocHA=) | `100% <ø> (ø)` | :arrow_up: |  
| [include/beast/http/dynamic\_body.hpp](https://codecov.io/gh/vinniefalco/Beast/pull/544?src=pr&el=tree#diff-aW5jbHVkZS9iZWFzdC9odHRwL2R5bmFtaWNfYm9keS5ocHA=) | `85.71% <ø> (ø)` | :arrow_up: |  
| [include/beast/http/empty\_body.hpp](https://codecov.io/gh/vinniefalco/Beast/pull/544?src=pr&el=tree#diff-aW5jbHVkZS9iZWFzdC9odHRwL2VtcHR5X2JvZHkuaHBw) | `57.14% <ø> (ø)` | :arrow_up: |  
| [include/beast/http/impl/basic\_parser.ipp](https://codecov.io/gh/vinniefalco/Beast/pull/544?src=pr&el=tree#diff-aW5jbHVkZS9iZWFzdC9odHRwL2ltcGwvYmFzaWNfcGFyc2VyLmlwcA==) | `95.4% <ø> (-0.06%)` | :arrow_down: |  
| [include/beast/http/string\_view\_body.hpp](https://codecov.io/gh/vinniefalco/Beast/pull/544?src=pr&el=tree#diff-aW5jbHVkZS9iZWFzdC9odHRwL3N0cmluZ192aWV3X2JvZHkuaHBw) | `100% <ø> (ø)` | :arrow_up: |  
| [include/beast/http/buffer\_body.hpp](https://codecov.io/gh/vinniefalco/Beast/pull/544?src=pr&el=tree#diff-aW5jbHVkZS9iZWFzdC9odHRwL2J1ZmZlcl9ib2R5LmhwcA==) | `96.29% <ø> (+3.43%)` | :arrow_up: |  
| [include/beast/http/impl/error.ipp](https://codecov.io/gh/vinniefalco/Beast/pull/544?src=pr&el=tree#diff-aW5jbHVkZS9iZWFzdC9odHRwL2ltcGwvZXJyb3IuaXBw) | `97.29% <100%> (ø)` | :arrow_up: |  
| [include/beast/http/serializer.hpp](https://codecov.io/gh/vinniefalco/Beast/pull/544?src=pr&el=tree#diff-aW5jbHVkZS9iZWFzdC9odHRwL3NlcmlhbGl6ZXIuaHBw) | `100% <100%> (ø)` | :arrow_up: |  
| [include/beast/http/fields.hpp](https://codecov.io/gh/vinniefalco/Beast/pull/544?src=pr&el=tree#diff-aW5jbHVkZS9iZWFzdC9odHRwL2ZpZWxkcy5ocHA=) | `100% <100%> (ø)` | :arrow_up: |  
| [include/beast/http/impl/serializer.ipp](https://codecov.io/gh/vinniefalco/Beast/pull/544?src=pr&el=tree#diff-aW5jbHVkZS9iZWFzdC9odHRwL2ltcGwvc2VyaWFsaXplci5pcHA=) | `94.63% <100%> (-0.11%)` | :arrow_down: |  
| ... and [15 more](https://codecov.io/gh/vinniefalco/Beast/pull/544?src=pr&el=tree-more) | |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/vinniefalco/Beast/pull/544?src=pr&el=continue).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/vinniefalco/Beast/pull/544?src=pr&el=footer). Last update [8982e14...9d42db1](https://codecov.io/gh/vinniefalco/Beast/pull/544?src=pr&el=lastupdated). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments).

---

## Review 2 [Commented]

> Username: vinniefalco  
> Created_at: 2017-06-26 16:05:14 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/544#pullrequestreview-46307654  

📁 test/http/doc_examples.cpp

```diff
 315 |+     doConstAndMutableBody()
 316 |+     {
 317 |+         test::pipe c{ios_};
```

> Username: vinniefalco  
> Created_at: 2017-06-26 16:05:14 UTC  
> Updated_at: 2017-06-26 19:12:04 UTC  
> Url: https://github.com/boostorg/beast/pull/544#discussion_r124050948  

Wow yeah, you understand that `test::pipe` mumbo-jumbo and you even wrote the tests - this is great!


---

## Review 3 [Commented]

> Username: vinniefalco  
> Created_at: 2017-06-26 16:06:12 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/544#pullrequestreview-46307918  

📁 test/http/doc_examples.cpp

```diff
 325 |+             req.body = body;
 326 |+             req.prepare_payload();
 327 |+             write(c.client, req);
```

> Username: vinniefalco  
> Created_at: 2017-06-26 16:06:12 UTC  
> Updated_at: 2017-06-26 19:12:04 UTC  
> Url: https://github.com/boostorg/beast/pull/544#discussion_r124051172  

This is interesting because note that you are writing the null terminator into the body. That's one of the problems with using `std::array` as the body type, its always the same size. `"Hello, world!\n"` is 14 characters not including the null.

> Username: octopus-prime  
> Created_at: 2017-06-26 16:28:43 UTC  
> Updated_at: 2017-06-26 19:12:04 UTC  
> Url: https://github.com/boostorg/beast/pull/544#discussion_r124056761  

Something to do here? Replace 15 by 14?

> Username: vinniefalco  
> Created_at: 2017-06-26 16:31:42 UTC  
> Updated_at: 2017-06-26 19:12:04 UTC  
> Url: https://github.com/boostorg/beast/pull/544#discussion_r124057438  

Nothing to do here except to note that people using `std::array` need to be careful because the entire array will be written out no matter how big the string is.


---

## Review 4 [Commented]

> Username: vinniefalco  
> Created_at: 2017-06-26 16:08:37 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/544#pullrequestreview-46308581  

📁 example/common/const_body.hpp

```diff
  26 |+ template< class T >
  27 |+ struct is_const_container<T, beast::detail::void_t<
  28 |+     decltype( std::declval<T&>().size() ),
```

> Username: vinniefalco  
> Created_at: 2017-06-26 16:08:37 UTC  
> Updated_at: 2017-06-26 19:12:04 UTC  
> Url: https://github.com/boostorg/beast/pull/544#discussion_r124051755  

Nice job on the traits! @pdimov showed me this trick for checking the return value:  
```  
    std::declval<std::size_t&>() = std::declval<T&>().size()  
```  
  
You can do something similar for checking the return value of `data`.   
```  
    std::declval<std::void const*&>() = std::declval<T&>().data()  
```

> Username: octopus-prime  
> Created_at: 2017-06-26 16:29:02 UTC  
> Updated_at: 2017-06-26 19:12:04 UTC  
> Url: https://github.com/boostorg/beast/pull/544#discussion_r124056829  

willfix


---

## Review 5 [Commented]

> Username: vinniefalco  
> Created_at: 2017-06-26 16:09:13 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/544#pullrequestreview-46308734  

📁 example/common/mutable_body.hpp

```diff
  17 |- #include <boost/tti/has_type.hpp>
  18 |- #include <boost/tti/has_member_function.hpp>
  19 |- #include <boost/mpl/identity.hpp>
```

> Username: vinniefalco  
> Created_at: 2017-06-26 16:09:13 UTC  
> Updated_at: 2017-06-26 19:12:04 UTC  
> Url: https://github.com/boostorg/beast/pull/544#discussion_r124051895  

Thank you for not dragging TTI and MPL into Beast.. lol


---

## Review 6 [Commented]

> Username: vinniefalco  
> Created_at: 2017-06-26 16:10:26 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/544#pullrequestreview-46309039  

📁 example/common/const_body.hpp

```diff
  41 |     static_assert(sizeof(typename Container::value_type) == 1, "Const character requirements not met");
  42 |-     static_assert(detail::is_const_container<Container>::value, "Const container requirements2 not met");
  42 |+     static_assert(detail::is_const_container<Container>::value, "Const container requirements not met");
```

> Username: vinniefalco  
> Created_at: 2017-06-26 16:10:25 UTC  
> Updated_at: 2017-06-26 19:12:04 UTC  
> Url: https://github.com/boostorg/beast/pull/544#discussion_r124052177  

I would use the names **ConstContainer** and **MutableContainer** since those are suggestive of the concept requirements.


---

## Review 7 [Commented]

> Username: vinniefalco  
> Created_at: 2017-06-26 16:11:08 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/544#pullrequestreview-46309245  

📁 test/http/doc_examples.cpp

```diff
 361 |+     {
 362 |+     	BEAST_EXPECTS(::detail::is_const_container<std::string>::value == true,
 363 |+     	    "is_const_container<std::string>::value");
```

> Username: vinniefalco  
> Created_at: 2017-06-26 16:11:08 UTC  
> Updated_at: 2017-06-26 19:12:04 UTC  
> Url: https://github.com/boostorg/beast/pull/544#discussion_r124052353  

Consider this instead:  
```  
BOOST_STATIC_ASSERT(::detail::is_const_container<std::string>::value);  
```  
  
It can go at file scope instead of in the body of a function

> Username: octopus-prime  
> Created_at: 2017-06-26 16:29:49 UTC  
> Updated_at: 2017-06-26 19:12:04 UTC  
> Url: https://github.com/boostorg/beast/pull/544#discussion_r124057007  

yeah! willfix


---

## Review 8 [Commented]

> Username: vinniefalco  
> Created_at: 2017-06-26 16:13:13 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/544#pullrequestreview-46309823  

📁 example/common/const_body.hpp

```diff
  12 |+ #include <beast/http/error.hpp>
  13 |+ #include <beast/http/message.hpp>
  14 |+ #include <beast/core/detail/type_traits.hpp>
```

> Username: vinniefalco  
> Created_at: 2017-06-26 16:13:13 UTC  
> Updated_at: 2017-06-26 19:12:04 UTC  
> Url: https://github.com/boostorg/beast/pull/544#discussion_r124052875  

Is this needed?

> Username: octopus-prime  
> Created_at: 2017-06-26 16:30:35 UTC  
> Updated_at: 2017-06-26 19:12:04 UTC  
> Url: https://github.com/boostorg/beast/pull/544#discussion_r124057169  

not sure. will check it


---

## Comment 9

> Username: vinniefalco  
> Created_at: 2017-06-26 16:16:46 UTC  
> Url: https://github.com/boostorg/beast/pull/544#issuecomment-311107994  

This should be rebased against **v68**, and you  should add the files `const_body.cpp` and `mutable_body.cpp` to this project:  
https://github.com/vinniefalco/Beast/tree/v68/test/common  
  
The .cpp file just includes the .hpp, to make sure it compiles by itself. Here's an example  
https://github.com/vinniefalco/Beast/blob/v68/test/common/detect_ssl.cpp

---

## Comment 10

> Username: octopus-prime  
> Created_at: 2017-06-26 17:22:09 UTC  
> Url: https://github.com/boostorg/beast/pull/544#issuecomment-311125244  

After rebasing my branch i have to close this pull request and create a new one?!  
Is my branch rebased? github says while comparing against v68 "These branches can be automatically merged."

---

## Comment 11

> Username: vinniefalco  
> Created_at: 2017-06-26 17:42:16 UTC  
> Url: https://github.com/boostorg/beast/pull/544#issuecomment-311130412  

Github often gets confused when projects attempt to keep a linear history, I wouldn't worry about it. But you included a merge commit which I typically try to avoid. Don't worry I will l fix it up.

---

## Comment 12

> Username: octopus-prime  
> Created_at: 2017-06-26 17:51:31 UTC  
> Updated_at: 2017-06-26 18:06:47 UTC  
> Url: https://github.com/boostorg/beast/pull/544#issuecomment-311132817  

After rebasing i get bad errors:  
  
```  
In file included from /home/mike/workspace/Beast/include/beast/http.hpp:21:0,  
                 from /home/mike/workspace/Beast/include/beast.hpp:14,  
                 from /home/mike/workspace/Beast/./example/doc/http_examples.hpp:8,  
                 from /home/mike/workspace/Beast/test/http/doc_examples.cpp:8:  
/home/mike/workspace/Beast/include/beast/http/parser.hpp: In instantiation of ‘class beast::http::parser<true, mutable_body<std::vector<char> >, std::allocator<char> >’:  
/home/mike/workspace/Beast/test/http/doc_examples.cpp:336:65:   required from here  
/home/mike/workspace/Beast/include/beast/http/parser.hpp:52:5: error: static assertion failed: BodyWriter requirements not met  
     static_assert(is_body_writer<Body>::value,  
     ^~~~~~~~~~~~~  
```  
  
Any idea why body concept check stopped working?  
  
Got it. You change the constructor of Reader/Writer.

---

## Comment 13

> Username: octopus-prime  
> Created_at: 2017-06-26 19:43:26 UTC  
> Url: https://github.com/boostorg/beast/pull/544#issuecomment-311161421  

I followed your suggestions. So you can try another review.

---

## Comment 14

> Username: vinniefalco  
> Created_at: 2017-06-26 20:51:36 UTC  
> Url: https://github.com/boostorg/beast/pull/544#issuecomment-311178406  

Code looks great, I squashed it down and now its part of version 68 (see https://github.com/vinniefalco/Beast/pull/541) Thanks!

---
