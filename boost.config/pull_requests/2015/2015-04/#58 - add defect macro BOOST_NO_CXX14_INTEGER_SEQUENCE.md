# #58 add defect macro BOOST_NO_CXX14_INTEGER_SEQUENCE [Closed]

> Username: olk  
> Created at: 2015-04-23 06:59:37 UTC  
> Updated at: 2018-08-06 11:30:54 UTC  
> Closed at: 2018-08-06 11:30:54 UTC  
> Url: https://github.com/boostorg/config/pull/58  

hopefully the patch is now correct

---

## Comment 1

> Username: jzmaddock  
> Created_at: 2015-04-23 10:12:00 UTC  
> Url: https://github.com/boostorg/config/pull/58#issuecomment-95523945  

OK, I think we're nearly there.  
- There's a spelling mistake "useing" should be "using".  
- I don't have anything locally to verify the test case with, have you checked it does build with a conforming compiler?  It doesn't look like it should, but I'm not up to speed with C++14 yet :(  
- Now for the main issue: we spent a bit of time recently removing Boost.Config's dependency on <utility> - see https://github.com/boostorg/config/pull/40.  The issue is that <utility> is no longer a lightweight header post C++11, and this patch ends up including it everywhere.  I see 2 solutions - either we find a way to detect this without including <utility>, or we say that we're not going to add feature macros for C++14 or later features, as these have associated feature test macros anyway in the standard.  Another option might be to include <utility> for libc++ only as that seems to be the only std lib consistently implementing the std macros anyway?  Everything else can define the macro unconditionally (no <utility> include or std feature check) until they introduce support.

---

## Comment 2

> Username: jzmaddock  
> Created_at: 2015-04-23 10:16:11 UTC  
> Url: https://github.com/boostorg/config/pull/58#issuecomment-95524479  

Update: looking at http://www.open-std.org/jtc1/sc22/wg21/docs/papers/2013/n3745, we will end up including the whole of the std lib just to check the std feature test macros if we add Boost macros that 1:1 correspond to these.  I don't know what the answer is, but including everything everywhere isn't it IMO.

---

## Comment 3

> Username: codykrieger  
> Created_at: 2015-07-27 16:57:15 UTC  
> Url: https://github.com/boostorg/config/pull/58#issuecomment-125271301  

Any update on this? This just bit me using boost 1.58.0 with a GCC 4.8-based cross-compilation toolchain targeting armv7. (I did manage to get everything to build by disabling both `context` and `coroutine`, though.)

---

## Comment 4

> Username: jzmaddock  
> Created_at: 2015-07-27 18:32:20 UTC  
> Url: https://github.com/boostorg/config/pull/58#issuecomment-125298551  

> Any update on this? This just bit me using boost 1.58.0 with a GCC   
> 4.8-based cross-compilation toolchain targeting armv7. (I did manage   
> to get everything to build by disabling both |context| and   
> |coroutine|, though.)  
>   
> Yes: we discussed this at some length on the mailing list, and whatever   
> the fix is, this isn't it.  It's probably better for individual Boost   
> libraries to check the SD6 macros directly rather than duplicate that   
> functionality here... but we'll have to see how that pans out.

---

## Comment 5

> Username: jzmaddock  
> Created_at: 2015-07-27 18:34:24 UTC  
> Url: https://github.com/boostorg/config/pull/58#issuecomment-125299283  

Forgot to say, Boost.Context appears to have this fixed for the next   
release by checking the SD6 macros directly as suggested.

---

## Comment 6

> Username: codykrieger  
> Created_at: 2015-07-27 18:35:25 UTC  
> Url: https://github.com/boostorg/config/pull/58#issuecomment-125299529  

👍 thanks!

---

## Comment 7

> Username: pdimov  
> Created_at: 2017-10-23 14:10:58 UTC  
> Url: https://github.com/boostorg/config/pull/58#issuecomment-338671704  

FWIW, `mp11` has `make_integer_sequence` that can be used on C++11.

---

## Comment 8

> Username: jzmaddock  
> Created_at: 2018-08-06 11:30:54 UTC  
> Url: https://github.com/boostorg/config/pull/58#issuecomment-410677994  

Since we have SD6 macros for this, I'm closing the issue.

---
