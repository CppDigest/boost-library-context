# #2191 Fix invalid partial_deflate_block error code. [Closed]

> Username: dmorilha-twilio  
> Created at: 2021-03-15 22:18:12 UTC  
> Updated at: 2021-03-29 18:31:30 UTC  
> Closed at: 2021-03-29 17:40:26 UTC  
> Url: https://github.com/boostorg/beast/pull/2191  

fixes the assertion which wrongly raised a`partial_deflate_block` error code when the last received deflated chunk does not fit into a single `buffers_front(cb_)` and a remainder is still pending to be inflated. Example:  
  
```  
 -> partial deflate <-  
  sz.total_out: 54  
 21-03-12 14:07:14.560 TRACE: on_read(const beast::error_code &, const std::size_t)  
 21-03-12 14:07:14.560 DEBUG: WssTransport::onMessage (7680 bytes)  
 {"body" ... {"id":"b4b2bb07-586a-48e1-8064-a9c4b7423fe9","sid":"MT760583713619794ebf5adc89e42722e4"},{"id":"c6232956-79da-4726-835e-dfb37bbd35ed","sid":                            +> "MTea63d62fc35de55234fce1b8fd19a4f8"}]}  
 21-03-12 14:07:14.560 ERROR: JSON parsing failed with error "* Line 1, Column 7681  
   Missing ',' or '}' in object declaration  
 ```  
  
Wireshark had no problems to inflate it, the deflated payload was 69 bytes and after inflating, it became 7734 = 7680 + 54.

---

## Comment 1

> Username: madmongo1  
> Created_at: 2021-03-15 23:42:19 UTC  
> Url: https://github.com/boostorg/beast/pull/2191#issuecomment-799834238  

Thank you for the commit.   
I've cancelled the test run as we're getting some false positives due to a change in the ubuntu::xenial image/packages.  
I'll rebase and reshedule once we have a stable CI testbed.

---

## Comment 2

> Username: vinniefalco  
> Created_at: 2021-03-16 00:29:30 UTC  
> Url: https://github.com/boostorg/beast/pull/2191#issuecomment-799852063  

Any chance we could add a test which demonstrates the incorrect behavior (i.e. before this patch) ?

---

## Comment 3

> Username: dmorilha-twilio  
> Created_at: 2021-03-17 04:19:34 UTC  
> Url: https://github.com/boostorg/beast/pull/2191#issuecomment-800781734  

> Any chance we could add a test which demonstrates the incorrect behavior (i.e. before this patch) ?  
  
Added the test. Without the fix, the forth fragment generates the `partial_deflate_block` error code.

---

## Review 4 [Commented]

> Username: vinniefalco  
> Created_at: 2021-03-17 04:24:31 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/2191#pullrequestreview-613910415  

📁 test/beast/websocket/read3.cpp

```diff
  17 | #include <boost/asio/strand.hpp>
  18 | #include <boost/asio/write.hpp>
  19 |+ #include <boost/algorithm/hex.hpp>
```

> Username: vinniefalco  
> Created_at: 2021-03-17 04:24:30 UTC  
> Updated_at: 2021-03-18 02:17:02 UTC  
> Url: https://github.com/boostorg/beast/pull/2191#discussion_r595703253  

This introduces a dependency on Boost.Algorithm. We could probably get rid of that by reformatting the string constant:  
  
```  
char const lines[] ={  
    0xc1, 0x2d, 0xaa, 0x56, 0xca, ... };  
```

> Username: dmorilha-twilio  
> Created_at: 2021-03-17 17:29:02 UTC  
> Updated_at: 2021-03-18 02:17:02 UTC  
> Url: https://github.com/boostorg/beast/pull/2191#discussion_r596237969  

done


---

## Comment 5

> Username: vinniefalco  
> Created_at: 2021-03-17 04:26:31 UTC  
> Url: https://github.com/boostorg/beast/pull/2191#issuecomment-800783955  

Thanks for the patch, this is great work. I have to admit, that the permessage-deflate code is quite complex and difficult to analyze, and these changes are non-trivial. As we are just about to release a new version of Boost, I think it would be best if we merge this after the release, so it will have 4 months worth of testing.

---

## Comment 6

> Username: dmorilha-twilio  
> Created_at: 2021-03-17 17:37:58 UTC  
> Url: https://github.com/boostorg/beast/pull/2191#issuecomment-801276922  

> Thanks for the patch, this is great work. I have to admit, that the permessage-deflate code is quite complex and difficult to analyze, and these changes are non-trivial. As we are just about to release a new version of Boost, I think it would be best if we merge this after the release, so it will have 4 months worth of testing.  
  
It is really a corner case, and I must say I really like how you leveraged other parts of boost to build a powerful API. I worked with other streaming libraries and beast is the most natural for a C++ developer. I am personally happy to have had time to and being able to contribute back to the project!  
  
The release schedule makes sense to me. We will probably retrofit it into 73 and test, do you foresee any problems porting it back?  
  
Thank you

---

## Review 7 [Commented]

> Username: vinniefalco  
> Created_at: 2021-03-17 17:50:52 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/2191#pullrequestreview-614622882  

📁 test/beast/websocket/read3.cpp

```diff
 983 |+                 "\xc6\xd4\x98\x1a\x53\x63\x6a\x4c\x8d\xa9\xf1"
 984 |+                 "\xa7\x1a\x0f"
 985 |+             ),
```

> Username: vinniefalco  
> Created_at: 2021-03-17 17:50:52 UTC  
> Updated_at: 2021-03-18 02:17:02 UTC  
> Url: https://github.com/boostorg/beast/pull/2191#discussion_r596255201  

beautiful


---

## Review 8 [Commented]

> Username: vinniefalco  
> Created_at: 2021-03-17 17:51:16 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/2191#pullrequestreview-614623305  

📁 test/beast/websocket/read3.cpp

```diff
 790 |-             boost::algorithm::unhex(line.begin(), line.end(), std::back_inserter(out));
 791 |-             net::write(wss.next_layer(), net::const_buffer(out.data(), out.size()));
 988 |+         for (const auto & packet : packets) {
```

> Username: vinniefalco  
> Created_at: 2021-03-17 17:51:16 UTC  
> Updated_at: 2021-03-18 02:17:02 UTC  
> Url: https://github.com/boostorg/beast/pull/2191#discussion_r596255500  

I would have used `const packet` instead of a reference but I suppose it doesn't matter

> Username: dmorilha-twilio  
> Created_at: 2021-03-18 21:38:50 UTC  
> Url: https://github.com/boostorg/beast/pull/2191#discussion_r597259896  

done


---

## Review 9 [Commented]

> Username: vinniefalco  
> Created_at: 2021-03-18 22:55:55 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/2191#pullrequestreview-615938345  

📁 include/boost/beast/websocket/impl/read.hpp

```diff
 565 |                         // append the empty block codes
 566 |-                         std::uint8_t constexpr
 566 |+                         static std::uint8_t constexpr
```

> Username: vinniefalco  
> Created_at: 2021-03-18 22:55:55 UTC  
> Url: https://github.com/boostorg/beast/pull/2191#discussion_r597295983  

I was wondering if `static` is needed. It _shouldn't_ be. But who knows. I am not very proficient with constexpr.

> Username: dmorilha-twilio  
> Created_at: 2021-03-19 00:34:20 UTC  
> Url: https://github.com/boostorg/beast/pull/2191#discussion_r597331121  

Same here, the only problem I was able to find later was since this is created inside the `else if` scope, the stack will unwind the the `sz.next_in` could potentially point to overwritten bytes, our release version with optimizations turned on was crashing with `zlib::invalid_block_length`.

> Username: madmongo1  
> Created_at: 2021-03-19 08:57:52 UTC  
> Updated_at: 2021-03-19 08:57:53 UTC  
> Url: https://github.com/boostorg/beast/pull/2191#discussion_r597504916  

Whether constexpr or const results in the same code:   
https://godbolt.org/z/1j9z18  
  
It definitely has to be static otherwise it's dangling pointer.  
In this case, gcc actually optimises out the initialisation of empty_block.  
https://godbolt.org/z/9xd3jv


---

## Comment 10

> Username: vinniefalco  
> Created_at: 2021-03-18 22:56:54 UTC  
> Url: https://github.com/boostorg/beast/pull/2191#issuecomment-802366857  

> do you foresee any problems porting it back?  
  
I don't think that code has changed since then, so it should be fairly straightforward.  
  
One thing on our agenda is to split zlib to its own Boost library and work on improvements to the tests and API.

---

## Comment 11

> Username: dmorilha-twilio  
> Created_at: 2021-03-29 18:31:28 UTC  
> Url: https://github.com/boostorg/beast/pull/2191#issuecomment-809613173  

thank you!

---
