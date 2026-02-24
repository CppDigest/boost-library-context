# #126 - Boost.Math tests' poisoning of isnan can break standard libraries [Closed]

> Username: BillyONeal  
> Created at: 2018-05-29 20:45:10 UTC  
> Updated at: 2018-08-19 16:56:09 UTC  
> Closed at: 2018-08-19 16:56:09 UTC  
> Url: https://github.com/boostorg/math/issues/126  

https://github.com/boostorg/math/blob/develop/test/compile_test/poison.hpp defines a macro isnan. When I added an isnan assert into unordered_Xxx::max_load_factor, and that explodes. The standard explicitly prohibits doing this: http://eel.is/c++draft/requirements#macro.names  
  
As a possible workaround, `poison.hpp` could include all standard library headers, not only math.h and cmath, if it wants to expose only its code to this.  
  
(I have worked around this in MSVC++ for now but Boost shouldn't be doing this)

---

## Comment 1

> Username: jzmaddock  
> Created at: 2018-05-30 16:55:00 UTC  
> Url: https://github.com/boostorg/math/issues/126#issuecomment-393235381  

The point is that some std lib's (and third party code) are still #defining isnan/isinf etc and this causes our code to explode unless we always make calls to those functions in such a way that macro expansion is disabled.  It's a similar situation to the min/max macros in msvc.  So... the point of this header is simply to validate that we're calling those functions "correctly".  
  
So... I guess my question is how come you're #including our private testing headers?

---

## Comment 2

> Username: pabristow  
> Created at: 2018-05-30 17:58:08 UTC  
> Url: https://github.com/boostorg/math/issues/126#issuecomment-393258723  

I’m sure John Maddock will provide a more expert reply, but as I understand it the whole idea of using this header and running the test is to make sure it *doesn’t* compile (and is only used when running Boost.Math *tests* rather than in normal use).  
  
But is *your* problem that it doesn’t only cause an engine-stall compile fail, but blows a head gasket – crashing the compiler?  
  
Paul  
  
  
  
From: BillyONeal [mailto:notifications@github.com]  
Sent: 29 May 2018 21:45  
To: boostorg/math  
Cc: Subscribed  
Subject: [boostorg/math] Boost.Math tests' poisoning of isnan can break standard libraries (#126)  
  
  
https://github.com/boostorg/math/blob/develop/test/compile_test/poison.hpp defines a macro isnan. When I added an isnan assert into unordered_Xxx::max_load_factor, and that explodes. The standard explicitly prohibits doing this: http://eel.is/c++draft/requirements#macro.names  
  
As a possible workaround, poison.hpp could include all standard library headers, not only math.h and cmath, if it wants to expose only its code to this.  
  
(I have worked around this in MSVC++ for now but Boost shouldn't be doing this)  
  
—  
You are receiving this because you are subscribed to this thread.  
Reply to this email directly, view it on GitHub<https://github.com/boostorg/math/issues/126>, or mute the thread<https://github.com/notifications/unsubscribe-auth/ABBuAdgBjgSToGvTgar38ymJROo199EAks5t3bNagaJpZM4USMWy>.

---

## Comment 3

> Username: BillyONeal  
> Created at: 2018-05-30 18:21:30 UTC  
> Url: https://github.com/boostorg/math/issues/126#issuecomment-393266390  

> The point is that some std lib's (and third party code) are still #defining isnan/isinf etc and this causes our code to explode  
  
I get that, but <unordered_map> and <unordered_set> (and other standard library headers) are under no obligation to support a broken C99 implementation when they ship with a working C99 implementation.  
  
> I guess my question is how come you're #including our private testing headers?  
  
We are running your tests with nightly builds of the compiler and standard library to make sure we don't break you. I added an assert to unordered_Xxx::max_load_factor, e.g. _STL_ASSERT(!_STD isnan(_New_load_factor)). But because your tests are exposing standard library headers to this that doesn't compile. I have worked around it in <unordered_foo> for now but we are concerned about expanding the min/max/NOMINMAX mess to even more names.  
  
> But is *your* problem that it doesn’t only cause an engine-stall compile fail, but blows a head gasket – crashing the compiler?  
  
Given that the standard explicitly makes this UB it isn't the standard library's problem, no. Even if it did make the compiler "blow a head gasket" that wouldn't technically be the compiler's problem either but ICEs are always bugs :)

---

## Comment 4

> Username: NAThompson  
> Created at: 2018-05-31 01:13:18 UTC  
> Url: https://github.com/boostorg/math/issues/126#issuecomment-393370247  

> The point is that some std lib's (and third party code) are still #defining isnan/isinf etc and this causes our code to explode  
  
Maybe we should file some bug reports against the implementations that do this. It sounds like something that would've been useful in `C++03`, but can now be safely avoided with the keyword pandemonium of `inline const constexpr . . .`. But is there any chance we will win?

---

## Comment 5

> Username: jzmaddock  
> Created at: 2018-05-31 07:38:41 UTC  
> Url: https://github.com/boostorg/math/issues/126#issuecomment-393439006  

>Maybe we should file some bug reports against the implementations that do this. It sounds like something that would've been useful in C++03, but can now be safely avoided with the keyword pandemonium of inline const constexpr . . .. But is there any chance we will win?  
  
As this mostly effects "legacy" platforms it's unlikely.  
  
>We are running your tests with nightly builds of the compiler and standard library to make sure we don't break you.  
  
Ah OK, got it, and yes you are correct that this could randomly break new std lib releases - I'll have to do as you suggest and #include all the std lib headers we use before poisoning those macros.  I'm actually a bit surprised that those headers are pulled in at all as we don't explicitly include them.  
  
BTW it's worth pointing out that the one test that uses this (std_real_concept_check) breaks the letter of the std anyway as it declares classes and functions in namespace std.  It does catch bugs that are otherwise almost impossible to track down though...

---

## Comment 6

> Username: BillyONeal  
> Created at: 2018-05-31 15:52:34 UTC  
> Url: https://github.com/boostorg/math/issues/126#issuecomment-393578780  

> I'm actually a bit surprised that those headers are pulled in at all as we don't explicitly include them.  
  
If you pull in different headers you're likely to get these bits. For example, `<functional>` needs `<unordered_map>` to implement C++17 boyer_moore search.  
  
>BTW it's worth pointing out that the one test that uses this (std_real_concept_check) breaks the letter of the std anyway as it declares classes and functions in namespace std.   
  
Yeah, if you are getting away with it, I'm not saying we need to go out of our way to make problems for ya :).  I cite the standard here mostly as a justification of "even though my change to <unordered_map> is most recent, the change is justified." After all, the whole reason you have this mess at all is to workaround implementations that aren't meeting their end of the contract :)

---

## Comment 7

> Username: NAThompson  
> Created at: 2018-06-25 11:38:41 UTC  
> Url: https://github.com/boostorg/math/issues/126#issuecomment-399922064  

Should we close this issue? It looks like John has a reasonable solution if it works for Billy.

---

## Comment 8

> Username: jzmaddock  
> Created at: 2018-08-19 16:56:09 UTC  
> Url: https://github.com/boostorg/math/issues/126#issuecomment-414140666  

No further updates so closing down.
