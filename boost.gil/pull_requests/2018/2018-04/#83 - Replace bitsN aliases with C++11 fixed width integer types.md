# #83 Replace bitsN[s] aliases with C++11 fixed width integer types [Merged]

> Username: mloskot  
> Created at: 2018-04-12 18:51:48 UTC  
> Updated at: 2018-04-12 22:57:11 UTC  
> Merged at: 2018-04-12 22:48:25 UTC  
> Closed at: 2018-04-12 22:48:25 UTC  
> Url: https://github.com/boostorg/gil/pull/83  

### Description  
  
- Replace bitsN[s] aliases with C++11 fixed width integer types  
- Import the selection of integer types into `boost::gil` namespace, and move from `channel.hpp` to `typedefs.hpp` for easier access.  
- Replace `bits32f` with `float32_t` and `bits64f` with `float64_t` - kept as alias of scoped_channel_value.  
- Move `float64_t` (`bits64f`) to typedefs.hpp.  
- Replace the four `{float|double}_{zero|one}` min/max channel values  
  with `float_point_zero` and `float_point_one` templates.  
- Replace `<boost/cstdint,hpp>` with C++11 `<cstdint>`.  
  
- Introduce preference of `using` declaration instead of `typedef`.  
- Reformat typedefs.hpp to take advantage of the `using` declaration - works much better for left-to-right reading, alias name as  most important detail comes first.  
- Add some of missing #include typedefs.hpp, sort some headers.  
  
### Tasklist  
  
- [x] Review  
- [x] Adjust for comments  
- [x] All CI builds and checks have passed  
  
### References (eg. other issues, pull requests)  
  
This PR implements code modernisation suggested in the [GIL 3 Ideas](https://github.com/boostorg/gil/wiki/Boost.GIL-3-Ideas) wiki:  
> ditch gil's simple data types, like bits32 or bits32f

---

## Review 1 [Commented]

> Username: stefanseefeld  
> Created_at: 2018-04-12 19:01:00 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/gil/pull/83#pullrequestreview-111752102  

📁 include/boost/gil/extension/io/targa/detail/supported_types.hpp

```diff
 109 | 
 110 |- #endif
 110 |+ #endif // BOOST_GIL_EXTENSION_IO_TARGA_DETAIL_SUPPORTED_TYPES_HPP
```

> Username: stefanseefeld  
> Created_at: 2018-04-12 19:01:00 UTC  
> Updated_at: 2018-04-12 21:23:44 UTC  
> Url: https://github.com/boostorg/gil/pull/83#discussion_r181188930  

Is this really necessary ? One more thing to track should we ever rename or move the file again...  
(I was just thinking we might just replace include guards by `#pragma once` statements, to avoid the debate entirely :-)

> Username: mloskot  
> Created_at: 2018-04-12 19:14:21 UTC  
> Updated_at: 2018-04-12 21:23:44 UTC  
> Url: https://github.com/boostorg/gil/pull/83#discussion_r181192471  

I think I got warned by an editor (VSCode?), some kind of linting annoyance. Unnecessary indeed. I'll remove it.  
  
  
I'm all for `#pragma once`!  
  
Question: is it allowed/forbidden by Boost?  
  
However, we should be aware and agree on caveats:  
* [It was considered for standardization, but rejected because it cannot be implemented reliably](https://stackoverflow.com/a/23699893/151641)  
* It may be troublesome in some [rare convoluted cases](https://en.wikipedia.org/wiki/Pragma_once#Caveats).  
* We agree we don't have to support any compilers not included [in this table](https://stackoverflow.com/q/23696115/151641)   
  
/cc @chhenning


---

## Review 2 [Commented]

> Username: stefanseefeld  
> Created_at: 2018-04-12 19:03:07 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/gil/pull/83#pullrequestreview-111752771  

📁 include/boost/gil/extension/toolbox/color_spaces/lab.hpp

```diff
  21 | 
  22 |- #include <boost/gil/gil_all.hpp>
  22 |+ #include <boost/gil/gil_all.hpp> // FIXME: Include what you use, not everything, even in extensions!
```

> Username: stefanseefeld  
> Created_at: 2018-04-12 19:03:07 UTC  
> Updated_at: 2018-04-12 21:23:44 UTC  
> Url: https://github.com/boostorg/gil/pull/83#discussion_r181189508  

Fair enough. But meanwhile, we should replace `boost/gil/gil_all.hpp` by `boost/gil.hpp`, so we can retire `boost/gil/gil_all.hpp` ASAP.

> Username: mloskot  
> Created_at: 2018-04-12 19:35:13 UTC  
> Updated_at: 2018-04-12 21:23:44 UTC  
> Url: https://github.com/boostorg/gil/pull/83#discussion_r181197799  

I considered that, but then I remembered this:  
  
https://svn.boost.org/trac10/wiki/Guidelines/MaintenanceGuidelines  
> Avoid the use of include all features files at the /boost level use instead specific files at boost/lib/file.hpp. Including less code reduce your user code breaking chances.   
  
So, I decided to keep the deprecated header.

> Username: stefanseefeld  
> Created_at: 2018-04-12 19:40:25 UTC  
> Updated_at: 2018-04-12 21:23:44 UTC  
> Url: https://github.com/boostorg/gil/pull/83#discussion_r181199054  

Well, OK, so the guideline is about including only the header that's needed. I don't disagree (though there are arguments for using a higher-level header, notably about robustness against code refactoring as we have just done.)  
But my point really is that we are replacing `boost/gil/gil_all.hpp` by `boost/gil.hpp`, so if you are already touching that line above, you might as well remove the reference to a deprecated file.

> Username: mloskot  
> Created_at: 2018-04-12 19:46:13 UTC  
> Updated_at: 2018-04-12 21:23:44 UTC  
> Url: https://github.com/boostorg/gil/pull/83#discussion_r181200499  

> But my point really is that we are replacing boost/gil/gil_all.hpp by boost/gil.hpp,  
> so if you are already touching that line above, you might as well remove the reference to a deprecated file.  
  
Since I don't know (or I'm too lazy now to find out) what detailed headers are required in this file,  
I preferred to leave the deprecated header - if I switched to the preferred `gil.hpp`, chances are we will never properly refactor that issue what would be not good :)

> Username: stefanseefeld  
> Created_at: 2018-04-12 19:52:42 UTC  
> Updated_at: 2018-04-12 21:23:44 UTC  
> Url: https://github.com/boostorg/gil/pull/83#discussion_r181202126  

Hehe, that argument could have come from me. :-)  
OK, let's keep it there as a reminder to come back and clean up at the latest when we are about to remove `gil/gil_all.hpp`.

> Username: mloskot  
> Created_at: 2018-04-12 19:55:27 UTC  
> Updated_at: 2018-04-12 21:23:44 UTC  
> Url: https://github.com/boostorg/gil/pull/83#discussion_r181202828  

OK.  
  
There is another round of refactoring coming soon: we need to switch `#include` from `"x.hpp"` to qualified `<boost/gil/x.hpp>` as the former is discouraged.  
  
Per https://www.boost.org/development/header.html  
> Then both your code and user code specifies the sub-directory in #include directives.


---

## Review 3 [Commented]

> Username: stefanseefeld  
> Created_at: 2018-04-12 19:05:56 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/gil/pull/83#pullrequestreview-111753628  

📁 test/image.cpp

```diff
 501 | 
 502 |+     static_assert(boost::is_same<derived_view_type<cmyk8c_planar_step_view_t, std::uint16_t, rgb_layout_t>::type,  rgb16c_planar_step_view_t>::value, "XXXXXXXXXXXXXXXXXX");
 503 |+ 
```

> Username: stefanseefeld  
> Created_at: 2018-04-12 19:05:56 UTC  
> Updated_at: 2018-04-12 21:23:44 UTC  
> Url: https://github.com/boostorg/gil/pull/83#discussion_r181190202  

You can surely come up with a more informative (and eloquent) message here, right ? ;-) )

> Username: mloskot  
> Created_at: 2018-04-12 19:36:34 UTC  
> Updated_at: 2018-04-12 21:23:44 UTC  
> Url: https://github.com/boostorg/gil/pull/83#discussion_r181198141  

Shoot, that `static_assert` sneaked by mistake! I will remove it.  
  
I used it as backup check when I was hitting some mysterious compilation errors during the refactoring. (I don't like `BOOST_STATIC_ASSERT` obscurity :-))


---

## Comment 4

> Username: stefanseefeld  
> Created_at: 2018-04-12 19:07:20 UTC  
> Url: https://github.com/boostorg/gil/pull/83#issuecomment-380912824  

I left a few inline comments. Overall this looks great ! Thanks for doing this tedious work !

---

## Comment 5

> Username: mloskot  
> Created_at: 2018-04-12 19:39:23 UTC  
> Url: https://github.com/boostorg/gil/pull/83#issuecomment-380921162  

@stefanseefeld Thank you for your review.  
  
Let's decide what to do with this `boost/gil/gil_all.hpp` and, given CI-s pass, it should be good to merge.  
  
@chhenning unless you are having some comments (Remember, this 'ditching' was all your idea, I'm just a messanger :-))

---

## Comment 6

> Username: chhenning  
> Created_at: 2018-04-12 20:40:11 UTC  
> Url: https://github.com/boostorg/gil/pull/83#issuecomment-380937479  

Sorry for being late to the party.

---

## Comment 7

> Username: chhenning  
> Created_at: 2018-04-12 20:45:25 UTC  
> Updated_at: 2018-04-12 20:45:41 UTC  
> Url: https://github.com/boostorg/gil/pull/83#issuecomment-380938836  

@mloskot Where do you include `cstdint` for gil, excluding the tests? I cannot find it.

---

## Comment 8

> Username: mloskot  
> Created_at: 2018-04-12 20:49:11 UTC  
> Url: https://github.com/boostorg/gil/pull/83#issuecomment-380939895  

@chhenning   
> Where do you include for gil, excluding the tests?  
  
Sorry, I'm not clear what you mean.

---

## Comment 9

> Username: chhenning  
> Created_at: 2018-04-12 21:12:24 UTC  
> Url: https://github.com/boostorg/gil/pull/83#issuecomment-380945988  

I mean where do you include the header that defines `std::uint8_t`?

---

## Comment 10

> Username: mloskot  
> Created_at: 2018-04-12 21:27:21 UTC  
> Url: https://github.com/boostorg/gil/pull/83#issuecomment-380949714  

There are `#include <cstdint>`, eg. in typedefs.hpp, channel.hpp  
  
BTW, `channel.hpp` cannot include `typedefs.hpp` due to circular dependency via `metafunctions.hpp` - that is something to solve during the includes refactoring.

---

## Review 11 [Commented]

> Username: chhenning  
> Created_at: 2018-04-12 21:32:46 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/gil/pull/83#pullrequestreview-111797560  

📁 include/boost/gil/channel.hpp

```diff
 604 |- ///////////////////////////////////////////
 605 |- 
 606 |- /// \defgroup bits8 bits8
```

> Username: chhenning  
> Created_at: 2018-04-12 21:16:45 UTC  
> Updated_at: 2018-04-12 21:32:46 UTC  
> Url: https://github.com/boostorg/gil/pull/83#discussion_r181224479  

Can we keep these comments? Rewritten for std types?

> Username: chhenning  
> Created_at: 2018-04-12 21:18:17 UTC  
> Updated_at: 2018-04-12 21:32:46 UTC  
> Url: https://github.com/boostorg/gil/pull/83#discussion_r181224821  

Ah, I see you moved them to typedefs.hpp

> Username: mloskot  
> Created_at: 2018-04-12 21:36:15 UTC  
> Url: https://github.com/boostorg/gil/pull/83#discussion_r181228980  

Yup, the commit log says  
> (...) move from channel.hpp to typedefs.hpp for easier access


---

## Comment 12

> Username: chhenning  
> Created_at: 2018-04-12 21:34:49 UTC  
> Url: https://github.com/boostorg/gil/pull/83#issuecomment-380951499  

OK. Thanks! Otherwise your PR looks good! Must have been quite a struggle to get it right!

---

## Comment 13

> Username: mloskot  
> Created_at: 2018-04-12 21:40:04 UTC  
> Updated_at: 2018-04-12 21:43:41 UTC  
> Url: https://github.com/boostorg/gil/pull/83#issuecomment-380952737  

@chhenning Thanks for reviewing it.  I will wait for the CI builds to finish, then merge.  
  
It wasn't bad. At least, now I understand why, when I hit "go to definition" for `rgb8_pixel_t` I don't see a s**t about it. The macros are very cool...for types producer, but not quite for the types consumer :-)

---
