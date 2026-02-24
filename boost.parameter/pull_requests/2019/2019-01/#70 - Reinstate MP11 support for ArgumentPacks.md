# #70  Reinstate MP11 support for ArgumentPacks [Merged]

> Username: CromwellEnage  
> Created at: 2019-01-21 06:16:15 UTC  
> Updated at: 2019-01-22 19:00:04 UTC  
> Merged at: 2019-01-21 21:43:10 UTC  
> Closed at: 2019-01-21 21:43:10 UTC  
> Url: https://github.com/boostorg/parameter/pull/70  

Argument packs qualify as Boost.MP11-style maps as well as MPL sequences.  These maps store the keyword tag types as their keys.

---

## Comment 1

> Username: CromwellEnage  
> Created_at: 2019-01-21 06:16:34 UTC  
> Url: https://github.com/boostorg/parameter/pull/70#issuecomment-455959906  

I'll ask @pdimov to review this first.

---

## Comment 2

> Username: pdimov  
> Created_at: 2019-01-21 16:57:01 UTC  
> Url: https://github.com/boostorg/parameter/pull/70#issuecomment-456139778  

This looks good from a cursory look. I see that you want to support both MPL and mp11 use at the same time, in which case making the mp11-style flat_arg_list inherit from the MPL-style non-flat one is reasonable.  
  
This makes me wonder if a separate parameter11 library wouldn't be better though. If MPL is dropped and everything is rewritten using parameter packs and mp11, the implementation ought to become simpler.  
  
Having the mp11 support optional and behind a macro makes it tricky to rely upon from another library's interface. Suppose you want to use Parameter in mp11 mode, what do you do? #define the macro in your headers? But what if the user includes another library's header first, one that also uses Parameter, but doesn't define the macro. Yes, optional behind a macro is correct in the sense that it's least likely to create backward compatibility issues, but it's less useful this way.

---

## Comment 3

> Username: CromwellEnage  
> Created_at: 2019-01-21 17:45:27 UTC  
> Url: https://github.com/boostorg/parameter/pull/70#issuecomment-456152958  

I agree that MP11 can simplify the parameter pack implementation.  (As an aside, in fact, this and the possible future direction of phasing out MPL were the main reasons I introduced those extraneous metafunctions.)  But I don't think a separate Parameter11 implementation should be necessary.  
  
Although MP11 support is available only for sufficiently advanced compilers, this 'mp11 mode' is on for those compilers by default unless the user explicitly disables it by #defining `BOOST_PARAMETER_DISABLE_MP11_USAGE`.  Does that affect your view on its usefulness?  
  
If we *are* planning to phase out MPL some time in the future, I could, later on, introduce two more configuration macros, `BOOST_PARAMETER_CAN_USE_MPL` and `BOOST_PARAMETER_DISABLE_MPL_USAGE`, which are analagous to the configuration macros for MP11.  When `BOOST_PARAMETER_CAN_USE_MP11` is #defined but `BOOST_PARAMETER_CAN_USE_MPL` is not, `flat_arg_list` can be implemented in the way you suggested (once I figure out how to reimplement argument deduction and parameter requirement satisfaction for SFINAE purposes).

---

## Comment 4

> Username: pdimov  
> Created_at: 2019-01-21 17:51:27 UTC  
> Url: https://github.com/boostorg/parameter/pull/70#issuecomment-456154544  

I hadn't noticed that mp11 mode is on by default, yes.  
  
The advantage of having a separate Parameter11 is that it doesn't affect existing users of Parameter, doesn't have to maintain backward compatibility, and allows a library (header) using Parameter and a library (header) using Parameter11 to coexist in the same translation unit.

---

## Comment 5

> Username: CromwellEnage  
> Created_at: 2019-01-21 17:55:34 UTC  
> Url: https://github.com/boostorg/parameter/pull/70#issuecomment-456155539  

Okay, I see what you're saying regarding the use case of including another library's header first, one that uses Boost.Parameter but #defines `BOOST_PARAMETER_DISABLE_MP11_USAGE`.  Would it suffice for me to add to the reference documentation that if user code includes the header of a library that #defines this macro, then MP11 support for Boost.Parameter is also unavailable for the user code?

---

## Comment 6

> Username: pdimov  
> Created_at: 2019-01-21 17:58:28 UTC  
> Url: https://github.com/boostorg/parameter/pull/70#issuecomment-456156274  

I don't think that this would be necessary. My remark was made when I thought that mp11 mode was off by default. It would (hopefully!) be rare for libraries to want to explicitly disable it.

---

## Comment 7

> Username: CromwellEnage  
> Created_at: 2019-01-21 18:32:59 UTC  
> Updated_at: 2019-01-21 18:33:35 UTC  
> Url: https://github.com/boostorg/parameter/pull/70#issuecomment-456164631  

I wouldn't mind implementing a separate lean-and-mean Parameter11 or even Parameter20, but other users might want to see additional (or fewer!) features.  For example, I know @Lastique wants `arg_list` to be part of the public interface (though, as an aside, I hope he looks at the `boost::parameter::compose` function and the `BOOST_PARAMETER_NO_SPEC_*` code generation macros to see if they can fulfill his needs for Boost.Log).  
  
In the meantime, I await the results of your further investigation.

---

## Comment 8

> Username: Lastique  
> Created_at: 2019-01-22 09:03:56 UTC  
> Url: https://github.com/boostorg/parameter/pull/70#issuecomment-456322124  

@CromwellEnage Any config macros won't be helpful for Boost.Log since a library cannot define config macros for other libraries. It is the user's choice as to which features of each library he wants.  
  
As for `compose`, I don't see immediate use for it in Boost.Log. I do combine multiple arguments into an argument pack by manually invoking `operator,`, but since Boost.Log is a C++03 library I'm doing it with preprocessor magic and I don't see how `compose` would change that. I may be missing something, please comment more specifically if so.

---

## Comment 9

> Username: Lastique  
> Created_at: 2019-01-22 09:47:30 UTC  
> Url: https://github.com/boostorg/parameter/pull/70#issuecomment-456335916  

OTOH, using `compose` instead of `operator,` could result in less templates instantiated, and thus better compile performance. Is this something that can be expected? Do you have any estimates or benchmarks on compile performance?

---

## Comment 10

> Username: CromwellEnage  
> Created_at: 2019-01-22 16:32:29 UTC  
> Url: https://github.com/boostorg/parameter/pull/70#issuecomment-456466367  

I didn't make this clear in my previous post, but I also introduced the `BOOST_PARAMETER_NO_SPEC_FUNCTION()` code generation macro and its companions, which delegate to `compose()` directly instead of instantiating `parameters<>`.  I have assessed qualitatively that test/preprocessor_eval_cat_no_spec.cpp compiles noticeably faster than test/preprocessor_eval_category.cpp.  With these new macros, you shouldn't need to resort to your own preprocessor magic.  
  
Regarding `compose()` versus `operator,`, better compiler performance should be expected from the former, but I don't yet have quantitative estimates.  I could use some pointers on how to set up such a benchmark test, although I do figure it would involve splitting up and expanding from test/compose.cpp.

---

## Comment 11

> Username: Lastique  
> Created_at: 2019-01-22 19:00:04 UTC  
> Url: https://github.com/boostorg/parameter/pull/70#issuecomment-456520985  

Ok, thank you. Compile performance is important for Boost.Log, so I will have a look at these utilities.

---
