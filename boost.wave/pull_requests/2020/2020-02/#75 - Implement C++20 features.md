# #75 Implement C++20 features [Merged]

> Username: jefftrull  
> Created at: 2020-02-28 05:03:21 UTC  
> Updated at: 2020-03-06 19:49:14 UTC  
> Merged at: 2020-03-06 19:48:50 UTC  
> Closed at: 2020-03-06 19:48:50 UTC  
> Url: https://github.com/boostorg/wave/pull/75  

I believe this implements the two key features:  
  
1. the ability to supply 0 variadic arguments in more cases  
2. using `__VA_OPT__` in macro definitions  
  
however it's a big change and I'm not sure I did it right. Hoping for some feedback and very happy to change things around.

---

## Comment 1

> Username: jefftrull  
> Created_at: 2020-02-28 05:07:32 UTC  
> Url: https://github.com/boostorg/wave/pull/75#issuecomment-592316307  

This is targeting 1.74, if that wasn't clear

---

## Review 2 [Commented]

> Username: hkaiser  
> Created_at: 2020-02-28 13:33:23 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/wave/pull/75#pullrequestreview-366368851  

@jefftrull Sorry for the long list of comments. Most of them are questions, however - feel free to ignore. Thanks a lot for working on this!

📁 doc/wave_driver.html

```diff
  59 |-     --c++11                      enable C++11 mode (implies --variadics and --long_long)
  59 |+     --c++11:                     enable C++11 mode (implies --variadics and --long_long)
  60 |+     --c++20:                     enable C++20 mode (adds __VA_OPT__ to variadics)
```

> Username: hkaiser  
> Created_at: 2020-02-28 12:40:34 UTC  
> Updated_at: 2020-03-06 00:38:34 UTC  
> Url: https://github.com/boostorg/wave/pull/75#discussion_r385673916  

Does this option imply `--variadics` and `--long_long`?

> Username: jefftrull  
> Created_at: 2020-02-28 19:52:51 UTC  
> Updated_at: 2020-03-06 00:38:34 UTC  
> Url: https://github.com/boostorg/wave/pull/75#discussion_r385892086  

I guess it does :)


📁 include/boost/wave/language_support.hpp

```diff
  50 |+         support_option_no_newline_at_end_of_file | support_option_va_opt | 0x20000,
  51 |+     support_cpp20 = support_cpp2a,
  52 |+ #endif
```

> Username: hkaiser  
> Created_at: 2020-02-28 12:44:30 UTC  
> Updated_at: 2020-03-06 00:38:34 UTC  
> Url: https://github.com/boostorg/wave/pull/75#discussion_r385675589  

Should this #if/#endif section be inside the `#if BOOST_WAVE_SUPPORT_CPP0X != 0/#endif` above?

> Username: jefftrull  
> Created_at: 2020-02-28 20:04:07 UTC  
> Updated_at: 2020-03-06 00:38:34 UTC  
> Url: https://github.com/boostorg/wave/pull/75#discussion_r385897518  

Yes, given that it depends on `support_option_no_newline_at_end_of_file`! I will make the change.


📁 include/boost/wave/token_ids.hpp

```diff
 314 |-     T_EXTPARAMETERBASE = TOKEN_FROM_ID(T_LAST_TOKEN+4, ExtParameterTokenType)
 315 |+     T_EXTPARAMETERBASE = TOKEN_FROM_ID(T_LAST_TOKEN+4, ExtParameterTokenType),
 316 |+     T_OPTPARAMETERBASE = TOKEN_FROM_ID(T_LAST_TOKEN+4, OptParameterTokenType)
```

> Username: hkaiser  
> Created_at: 2020-02-28 12:46:01 UTC  
> Updated_at: 2020-03-06 00:38:34 UTC  
> Url: https://github.com/boostorg/wave/pull/75#discussion_r385676250  

Should this be `T_LAST_TOKEN+5` instead (to make the token_id unique)?

> Username: jefftrull  
> Created_at: 2020-02-28 20:05:45 UTC  
> Updated_at: 2020-03-06 00:38:34 UTC  
> Url: https://github.com/boostorg/wave/pull/75#discussion_r385898180  

I am following the pattern of `T_PARAMETERBASE` AND `T_EXTPARAMETERBASE` here - both have the same id but a different type. So if `T_EXTPARAMETERBASE` is correct this should be as well, I think (hope?)

> Username: hkaiser  
> Created_at: 2020-02-28 20:26:50 UTC  
> Updated_at: 2020-03-06 00:38:34 UTC  
> Url: https://github.com/boostorg/wave/pull/75#discussion_r385906760  

Fair enough!


📁 include/boost/wave/util/cpp_iterator.hpp

```diff
1823 |+                     {
1824 |+                         seen_va_opt = true;
1825 |+                     }
```

> Username: hkaiser  
> Created_at: 2020-02-28 12:48:19 UTC  
> Updated_at: 2020-03-06 00:38:34 UTC  
> Url: https://github.com/boostorg/wave/pull/75#discussion_r385677236  

Please surround this new section with a `#if BOOST_WAVE_SUPPORT_VA_OPT != 0/#endif`.

> Username: hkaiser  
> Created_at: 2020-02-28 20:57:34 UTC  
> Updated_at: 2020-03-06 00:38:34 UTC  
> Url: https://github.com/boostorg/wave/pull/75#discussion_r385918718  

P0306r4 introduces `__VA_OPT__` as a _functional_ macro. That means that it has to be followed by a `T_LEFT_PAREN` (possibly with whitespace in between) in order to be recognized as such. Using `__VA_OPT__` without a parenthesis should not invoke the corresponding logic but should leave the token alone, possibly for later expansion.

> Username: hkaiser  
> Created_at: 2020-02-28 21:08:17 UTC  
> Updated_at: 2020-03-06 00:38:34 UTC  
> Url: https://github.com/boostorg/wave/pull/75#discussion_r385922768  

Correction: P0306r4 says, that `__VA_OPT__` shall always occur as part of the token sequence `__VA_OPT__(content)`, so using it without parenthesis the program is ill-formed.   
  
It is not entirely clear whether that means that only the eventually expanded token sequence should form the `__VA_OPT__(content)` (i.e. whether it is allowed to construct this sequence while expanding macros such that the `__VA_OPT__` may appear without parenthesis and the required sequence is piecewise constructed) or whether any occurrence of `__VA_OPT__` without parenthesis is ill-formed.

> Username: hkaiser  
> Created_at: 2020-02-28 21:23:28 UTC  
> Updated_at: 2020-03-06 00:38:34 UTC  
> Url: https://github.com/boostorg/wave/pull/75#discussion_r385928639  

IOW, is the following valid code or not:  
```c++  
#define MAKE_FUNC(x, ...) x __VA_ARGS__  
#define F(x, f, ...)  x(f MAKE_FUNC(__VA_OPT__, (,)) __VA_ARGS__))  
F(print, "%d", 1)  
```

> Username: jefftrull  
> Created_at: 2020-02-28 21:24:59 UTC  
> Updated_at: 2020-03-06 00:38:34 UTC  
> Url: https://github.com/boostorg/wave/pull/75#discussion_r385929221  

Is there a testcase you can think of we could use to see what gcc/clang do?

> Username: hkaiser  
> Created_at: 2020-02-28 21:31:56 UTC  
> Updated_at: 2020-03-06 00:38:34 UTC  
> Url: https://github.com/boostorg/wave/pull/75#discussion_r385931909  

clang reports a `t.cpp:2:47: error: missing '(' following __VA_OPT__` for the example above. I'm inclined to think this is a mistake, however.

> Username: jefftrull  
> Created_at: 2020-02-28 22:03:46 UTC  
> Updated_at: 2020-03-06 00:38:34 UTC  
> Url: https://github.com/boostorg/wave/pull/75#discussion_r385943466  

I feel like  
> shall always occur as part of the token sequence `__VA_OPT__(content)`  
  
is pretty specific, and implies the macro `F` is ill-formed. gcc also rejects it...  
  
If Wave were to support this, what changes should be made in the checking code?

> Username: hkaiser  
> Created_at: 2020-02-28 22:19:17 UTC  
> Updated_at: 2020-03-06 00:38:34 UTC  
> Url: https://github.com/boostorg/wave/pull/75#discussion_r385948739  

Ok, let's raise an error then. It's probably impossible to detect whether `__VA_OPT__` is 'eventually' composed to be followed by parenthesis...

> Username: jefftrull  
> Created_at: 2020-02-28 22:32:44 UTC  
> Updated_at: 2020-03-06 00:38:34 UTC  
> Url: https://github.com/boostorg/wave/pull/75#discussion_r385953188  

I'll add a unit test, and any code necessary to ensure an error fires.

> Username: eldiener  
> Created_at: 2020-02-29 00:09:05 UTC  
> Updated_at: 2020-03-06 00:38:34 UTC  
> Url: https://github.com/boostorg/wave/pull/75#discussion_r385976517  

>   
>   
> Correction: P0306r4 says, that `__VA_OPT__` shall always occur as part of the token sequence `__VA_OPT__(content)`, so using it without parenthesis the program is ill-formed.  
>   
> It is not entirely clear whether that means that only the eventually expanded token sequence should form the `__VA_OPT__(content)` (i.e. whether it is allowed to construct this sequence while expanding macros such that the `__VA_OPT__` may appear without parenthesis and the required sequence is piecewise constructed) or whether any occurrence of `__VA_OPT__` without parenthesis is ill-formed.  
  
I tnink that __VA_OPT__ not followed by '( optional_content )' is ill-formed. I do not think you can have a __VA_OPT__ followed by something , which expands to '( optional_content )', to make it acceptable. Of course in the form of '__VA_OPT__ ( optional_content )' the optional_content may eventually expand into other tokens but that only occurs if  '__VA_OPT__ ( optional_content )' is resolved to 'optional_content' when the macro output gets rescanned for replacement. Of course I could be wrong but this is my reading of the C++20 standard as it now exists in N4842.

> Username: jefftrull  
> Created_at: 2020-03-03 00:46:33 UTC  
> Updated_at: 2020-03-06 00:38:34 UTC  
> Url: https://github.com/boostorg/wave/pull/75#discussion_r386738249  

Added the code and unit test to the PR

---

📁 include/boost/wave/util/cpp_iterator.hpp

```diff
1837 |+                         macroname.get_value().c_str(), act_token.get_position());
1838 |+                     return;
1839 |+                 }
```

> Username: hkaiser  
> Created_at: 2020-02-28 12:48:55 UTC  
> Updated_at: 2020-03-06 00:38:34 UTC  
> Url: https://github.com/boostorg/wave/pull/75#discussion_r385677488  

Same here, please guard this with `#if BOOST_WAVE_SUPPORT_VA_OPT != 0/#endif`.

> Username: jefftrull  
> Created_at: 2020-02-28 20:08:47 UTC  
> Updated_at: 2020-03-06 00:38:34 UTC  
> Url: https://github.com/boostorg/wave/pull/75#discussion_r385899546  

I will do both of those as well as the declaration of `seen_va_opt`


📁 include/boost/wave/util/cpp_macromap.hpp

```diff
1052 |+                         // no args; insert placemarker
1053 |+                         *std::back_inserter(expanded) =
1054 |+                             typename ContainerT::value_type(T_PLACEMARKER, "\xA7", pos);
```

> Username: hkaiser  
> Created_at: 2020-02-28 12:58:17 UTC  
> Updated_at: 2020-03-06 00:38:34 UTC  
> Url: https://github.com/boostorg/wave/pull/75#discussion_r385681264  

Wouldn't a simple   
```c++  
expanded.push_back(typename ContainerT::value_type(T_PLACEMARKER, "\xA7", pos));  
```   
do the trick?

> Username: hkaiser  
> Created_at: 2020-02-28 13:24:37 UTC  
> Updated_at: 2020-03-06 00:38:34 UTC  
> Url: https://github.com/boostorg/wave/pull/75#discussion_r385692723  

Also, this inserts a place marker even if the token sequence already contains one. Is that intended?

> Username: jefftrull  
> Created_at: 2020-02-28 20:32:55 UTC  
> Updated_at: 2020-03-06 00:38:34 UTC  
> Url: https://github.com/boostorg/wave/pull/75#discussion_r385909098  

1. Yes :) I will make the change.  
2. I'm not sure how to avoid inserting two placemarkers. Is there an appropriate Wave idiom, or a different technique I should use?

> Username: hkaiser  
> Created_at: 2020-02-28 20:40:11 UTC  
> Updated_at: 2020-03-06 00:38:34 UTC  
> Url: https://github.com/boostorg/wave/pull/75#discussion_r385911957  

> 2. I'm not sure how to avoid inserting two placemarkers. Is there an appropriate Wave idiom, or a different technique I should use?  
  
I think using `is_whitespace_only` instead of `is_blank_only` would be sufficient to resolve this.

> Username: jefftrull  
> Created_at: 2020-02-28 20:49:21 UTC  
> Updated_at: 2020-03-06 00:38:34 UTC  
> Url: https://github.com/boostorg/wave/pull/75#discussion_r385915441  

Well, unfortunately making that change causes two of my test cases to fail: [this one](https://github.com/jefftrull/wave/blob/9121d11e20adb392160f2d4ec8f19a64a901677b/test/testwave/testfiles/t_8_001.cpp#L29) and [this one](https://github.com/jefftrull/wave/blob/9121d11e20adb392160f2d4ec8f19a64a901677b/test/testwave/testfiles/t_8_001.cpp#L43). And that's why the code was change to test for placemarkers... Why that was necessary I'm not sure and I would not be surprised if there was a better solution (or a bug I introduced elsewhere).

> Username: hkaiser  
> Created_at: 2020-02-28 21:24:55 UTC  
> Updated_at: 2020-03-06 00:38:34 UTC  
> Url: https://github.com/boostorg/wave/pull/75#discussion_r385929196  

Ok, I'll investigate this further.

> Username: hkaiser  
> Created_at: 2020-02-28 21:52:32 UTC  
> Updated_at: 2020-03-06 00:38:34 UTC  
> Url: https://github.com/boostorg/wave/pull/75#discussion_r385939665  

How about writing it as:  
```c++  
if ((i == arguments.size()) ||             // no variadic argument  
    impl::is_whitespace_only(arguments[i])) {   // no visible tokens  
// no args; insert placemarker if one is not already present  
    expanded.push_back(  
        typename ContainerT::value_type(T_PLACEMARKER, "\xA7", pos));  
}  
else if (!impl::is_blank_only(arguments[i])) {  
    // …  
}  
```

> Username: jefftrull  
> Created_at: 2020-02-28 22:12:12 UTC  
> Updated_at: 2020-03-06 00:38:34 UTC  
> Url: https://github.com/boostorg/wave/pull/75#discussion_r385946268  

It works!

---

📁 include/boost/wave/util/cpp_macromap.hpp

```diff
1120 |+                         // no argument supplied; insert placemarker
1121 |+                         *std::back_inserter(expanded) =
1122 |+                             typename ContainerT::value_type(T_PLACEMARKER, "\xA7", pos);
```

> Username: hkaiser  
> Created_at: 2020-02-28 12:58:52 UTC  
> Updated_at: 2020-03-06 00:38:34 UTC  
> Url: https://github.com/boostorg/wave/pull/75#discussion_r385681522  

Same here: `push_back`?

---

📁 include/boost/wave/util/cpp_macromap.hpp

```diff
1313 |+             }
1314 |+         }
1315 |+ #endif
```

> Username: hkaiser  
> Created_at: 2020-02-28 13:21:24 UTC  
> Updated_at: 2020-03-06 00:38:34 UTC  
> Url: https://github.com/boostorg/wave/pull/75#discussion_r385691216  

Would you mind adding a comment explaining that in C++20 the rules have changed for how many arguments are required if ellipses are involved? I.e. something like:  
> The number of arguments must be more than (until C++20) / at least as many as (since C++20) the number of parameters (not counting ...).

> Username: jefftrull  
> Created_at: 2020-02-28 21:36:40 UTC  
> Updated_at: 2020-03-06 00:38:34 UTC  
> Url: https://github.com/boostorg/wave/pull/75#discussion_r385933680  

Sure, no problem. Going with:  
  
>            // Starting with C++20, variable arguments may be left out  
>            // entirely, so reduce the mandatory argument count by one  
>            // if the last parameter is ellipsis:

---

📁 include/boost/wave/util/cpp_macromap.hpp

```diff
1121 |+                         *std::back_inserter(expanded) =
1122 |+                             typename ContainerT::value_type(T_PLACEMARKER, "\xA7", pos);
1123 |+                     }
```

> Username: hkaiser  
> Created_at: 2020-02-28 13:26:04 UTC  
> Updated_at: 2020-03-06 00:38:34 UTC  
> Url: https://github.com/boostorg/wave/pull/75#discussion_r385693441  

Should this `else` be C++20 only? Or does it fix a bug?

> Username: jefftrull  
> Created_at: 2020-02-28 20:58:59 UTC  
> Updated_at: 2020-03-06 00:38:34 UTC  
> Url: https://github.com/boostorg/wave/pull/75#discussion_r385919249  

Sounds reasonable. I will make the change.


📁 include/boost/wave/util/macro_helpers.hpp

```diff
 288 |+             // arrived at end without matching rparen
 289 |+             mdit = mdstart_it;
 290 |+             return false;
```

> Username: hkaiser  
> Created_at: 2020-02-28 13:28:33 UTC  
> Updated_at: 2020-03-06 00:38:34 UTC  
> Url: https://github.com/boostorg/wave/pull/75#discussion_r385694643  

Should this rather report an error?

> Username: jefftrull  
> Created_at: 2020-02-28 21:02:02 UTC  
> Updated_at: 2020-03-06 00:38:34 UTC  
> Url: https://github.com/boostorg/wave/pull/75#discussion_r385920431  

[the caller](https://github.com/jefftrull/wave/blob/9121d11e20adb392160f2d4ec8f19a64a901677b/include/boost/wave/util/cpp_macromap.hpp#L1044) reports an error, in this case.


📁 test/testwave/testfiles/t_8_001.cpp

```diff
  42 |+ //R printf("at line=%d" ": ", 44); printf("All well in zone %d", n); printf("\n");
  43 |+ LOG2();
  44 |+ LOG2("All well in zone %d", n);
```

> Username: hkaiser  
> Created_at: 2020-02-28 13:29:53 UTC  
> Updated_at: 2020-03-06 00:38:34 UTC  
> Url: https://github.com/boostorg/wave/pull/75#discussion_r385695188  

Do you think we should add the hook verification as well (i.e. `//H...` comments)?

> Username: hkaiser  
> Created_at: 2020-02-28 15:07:05 UTC  
> Updated_at: 2020-03-06 00:38:34 UTC  
> Url: https://github.com/boostorg/wave/pull/75#discussion_r385746163  

FWIW, adding a `//H` line at the end of t_8_001.cpp makes testwave produce the following recorded hooks:  
```c++  
//H 10: t_8_001.cpp(17) : #define  
//H 08: t_8_001.cpp(17) : LOG(msg, ...) = printf(msg __VA_OPT__(, ) __VA_ARGS__)  
//H 10: t_8_001.cpp(18) : #define  
//H 08: t_8_001.cpp(18) : SDEF(sname, ...) = S sname __VA_OPT__(= { __VA_ARGS__ })  
//H 10: t_8_001.cpp(19) : #define  
//H 08: t_8_001.cpp(19) : LOG2(...) = printf("at line=%d" __VA_OPT__(": "), __LINE__);      __VA_OPT__(printf(__VA_ARGS__);)                      printf("\n")  
//H 00: t_8_001.cpp(28) : LOG("hello world\n"), [t_8_001.cpp(17):LOG(msg, ...) = printf(msg __VA_OPT__(, ) __VA_ARGS__)]  
//H 02: printf("hello world\n" º)  
//H 03: printf("hello world\n")  
//H 00: t_8_001.cpp(29) : LOG("hello world\n", º), [t_8_001.cpp(17):LOG(msg, ...) = printf(msg __VA_OPT__(, ) __VA_ARGS__)]  
//H 02: printf("hello world\n" º)  
//H 03: printf("hello world\n")  
//H 00: t_8_001.cpp(30) : LOG("hello %d\n", n), [t_8_001.cpp(17):LOG(msg, ...) = printf(msg __VA_OPT__(, ) __VA_ARGS__)]  
//H 02: printf("hello %d\n", n)  
//H 03: printf("hello %d\n", n)  
//H 00: t_8_001.cpp(35) : SDEF(foo), [t_8_001.cpp(18):SDEF(sname, ...) = S sname __VA_OPT__(= { __VA_ARGS__ })]  
//H 02: S foo º  
//H 03: S foo  
//H 00: t_8_001.cpp(36) : SDEF(bar, 1, 2, 3), [t_8_001.cpp(18):SDEF(sname, ...) = S sname __VA_OPT__(= { __VA_ARGS__ })]  
//H 02: S bar = { 1, 2, 3 }  
//H 03: S bar = { 1, 2, 3 }  
//H 00: t_8_001.cpp(43) : LOG2(º), [t_8_001.cpp(19):LOG2(...) = printf("at line=%d" __VA_OPT__(": "), __LINE__);      __VA_OPT__(printf(__VA_ARGS__);)                      printf("\n")]  
//H 02: printf("at line=%d" º, __LINE__);      º                      printf("\n")  
//H 03: printf("at line=%d", 43);                            printf("\n")  
//H 00: t_8_001.cpp(44) : LOG2("All well in zone %d", n), [t_8_001.cpp(19):LOG2(...) = printf("at line=%d" __VA_OPT__(": "), __LINE__);      __VA_OPT__(printf(__VA_ARGS__);)                      printf("\n")]  
//H 02: printf("at line=%d" ": ", __LINE__);      printf("All well in zone %d", n);                      printf("\n")  
//H 03: printf("at line=%d" ": ", 44);      printf("All well in zone %d", n);                      printf("\n")  
```  
This looks bogus as it apparently contains placemarker tokens (the `º` characters). I think that shouldn't happen.

> Username: hkaiser  
> Created_at: 2020-02-28 16:09:39 UTC  
> Updated_at: 2020-03-06 00:38:34 UTC  
> Url: https://github.com/boostorg/wave/pull/75#discussion_r385782956  

Actually, the placemarker symbols may only become removed after the `H 02` (`expanded_macro` hook) is called (during rescanning of the replacement list). Thus, seeing those in the output could be correct after all. I just have never seen any placemarkers being printed in this context before.   
  
We might want to remove placemarkers from the generated string representation, similarly to what we do while generating string literals (see: https://github.com/boostorg/wave/blob/develop/include/boost/wave/util/macro_helpers.hpp#L128-L135).

> Username: jefftrull  
> Created_at: 2020-02-28 21:03:59 UTC  
> Updated_at: 2020-03-06 00:38:34 UTC  
> Url: https://github.com/boostorg/wave/pull/75#discussion_r385921122  

1. I will add hook verification.  
2. I will investigate whether the placemarkers are new, and if not, look at removing them from string reps.

> Username: jefftrull  
> Created_at: 2020-03-03 01:28:14 UTC  
> Updated_at: 2020-03-06 00:38:34 UTC  
> Url: https://github.com/boostorg/wave/pull/75#discussion_r386750533  

It looks like on develop branch `t_1_032.cpp`, which I haven't touched, also shows placemarkers in the output, so I think this is not new. I will investigate scrubbing them.

> Username: hkaiser  
> Created_at: 2020-03-03 02:03:17 UTC  
> Updated_at: 2020-03-06 00:38:34 UTC  
> Url: https://github.com/boostorg/wave/pull/75#discussion_r386760078  

Ok, I wasn't even aware of this anymore... In that case, sorry for the noise. It might be even correct to leave them in to ensure proper functioning (for instance to make sure that we don't see two of them in a row, etc.)

> Username: jefftrull  
> Created_at: 2020-03-04 19:22:56 UTC  
> Updated_at: 2020-03-06 00:38:34 UTC  
> Url: https://github.com/boostorg/wave/pull/75#discussion_r387881747  

I've reviewed the hook output and concluded it's correct, so I've updated the testcase accordingly.


📁 tool/cpp.cpp

```diff
1352 |+ #endif
1353 |+ #if BOOST_WAVE_SUPPORT_CPP2A != 0
1354 |+         ("c++20", "enable C++20 mode (implies --variadics and --long_long, adds __VAR__OPT__)")
```

> Username: hkaiser  
> Created_at: 2020-02-28 13:32:11 UTC  
> Updated_at: 2020-03-06 00:38:34 UTC  
> Url: https://github.com/boostorg/wave/pull/75#discussion_r385696212  

Indentation?

> Username: hkaiser  
> Created_at: 2020-02-28 20:29:45 UTC  
> Updated_at: 2020-03-06 00:38:34 UTC  
> Url: https://github.com/boostorg/wave/pull/75#discussion_r385907857  

Also, should probably be `__VAR_OPT__`.

> Username: jefftrull  
> Created_at: 2020-02-28 21:40:49 UTC  
> Updated_at: 2020-03-06 00:38:34 UTC  
> Url: https://github.com/boostorg/wave/pull/75#discussion_r385935217  

:man_facepalming:

> Username: jefftrull  
> Created_at: 2020-02-28 21:41:32 UTC  
> Updated_at: 2020-03-06 00:38:34 UTC  
> Url: https://github.com/boostorg/wave/pull/75#discussion_r385935495  

I did that in *two* places


---

## Review 3 [Commented]

> Username: hkaiser  
> Created_at: 2020-02-28 15:18:56 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/wave/pull/75#pullrequestreview-366472864  

📁 include/boost/wave/util/cpp_macromap.hpp

```diff
1054 |+                             typename ContainerT::value_type(T_PLACEMARKER, "\xA7", pos);
1055 |+                     } else {
1056 |+                         ++cstart; ++cstart;
```

> Username: hkaiser  
> Created_at: 2020-02-28 15:18:56 UTC  
> Updated_at: 2020-03-06 00:38:34 UTC  
> Url: https://github.com/boostorg/wave/pull/75#discussion_r385753005  

I think we should make sure that the replacement list does not contain a `__VA_OPT__` itself; P0306r4 states:  
> The identifier `__VA_OPT__` shall always occur as part of the token sequence `__VA_OPT__(content)`, where content is an arbitrary sequence of preprocessing-tokens other than `__VA_OPT__`.

> Username: jefftrull  
> Created_at: 2020-02-28 22:17:05 UTC  
> Updated_at: 2020-03-06 00:38:34 UTC  
> Url: https://github.com/boostorg/wave/pull/75#discussion_r385947941  

I will add a test case and whatever code is required to properly check


---

## Review 4 [Commented]

> Username: hkaiser  
> Created_at: 2020-02-28 15:23:47 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/wave/pull/75#pullrequestreview-366476399  

📁 include/boost/wave/util/cpp_macromap.hpp

```diff
1026 |+ 
1027 |+ #if BOOST_WAVE_SUPPORT_VA_OPT != 0
1028 |+                 if (is_va_opt) {
```

> Username: hkaiser  
> Created_at: 2020-02-28 15:23:47 UTC  
> Updated_at: 2020-03-06 00:38:34 UTC  
> Url: https://github.com/boostorg/wave/pull/75#discussion_r385755800  

Is using `__VA_OPT__` without ellipses in the macro definition parameter list valid?

> Username: hkaiser  
> Created_at: 2020-02-28 20:59:18 UTC  
> Updated_at: 2020-03-06 00:38:34 UTC  
> Url: https://github.com/boostorg/wave/pull/75#discussion_r385919369  

P0306r4 states that `__VA_OPT__` may only be used in the replacement text of a variadic macro.

> Username: jefftrull  
> Created_at: 2020-02-28 21:09:09 UTC  
> Updated_at: 2020-03-06 00:38:34 UTC  
> Url: https://github.com/boostorg/wave/pull/75#discussion_r385923084  

No, and I have [a test](https://github.com/jefftrull/wave/blob/9121d11e20adb392160f2d4ec8f19a64a901677b/test/testwave/testfiles/t_8_007.cpp#L15) for that.


---

## Review 5 [Commented]

> Username: hkaiser  
> Created_at: 2020-02-28 15:32:36 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/wave/pull/75#pullrequestreview-366483453  

📁 include/boost/wave/util/cpp_macromap.hpp

```diff
1059 |+                         expand_replacement_list(cstart, cit, arguments,
1060 |+                                                 expand_operator_defined, expanded);
1061 |+                     }
```

> Username: hkaiser  
> Created_at: 2020-02-28 15:32:36 UTC  
> Updated_at: 2020-03-06 00:38:34 UTC  
> Url: https://github.com/boostorg/wave/pull/75#discussion_r385761290  

I'm not sure if recursively expanding the replacement list is the correct solution here. I think calling `impl::replace_ellipsis` (like for `__VA_ARGS__` above) in connection with the rescanning that is done afterwards  should be sufficient. Doing recursive expanding of the replacement list might actually expand too much.

> Username: jefftrull  
> Created_at: 2020-02-28 22:07:26 UTC  
> Updated_at: 2020-03-06 00:38:34 UTC  
> Url: https://github.com/boostorg/wave/pull/75#discussion_r385944664  

I'll give that a try. Do you think this line from the proposal changes anything?  
  
> Otherwise, the replacement consists of the results of the expansion of content as the replacement list of the current function-like macro before rescanning and further replacement

> Username: hkaiser  
> Created_at: 2020-02-28 22:15:07 UTC  
> Updated_at: 2020-03-06 00:38:34 UTC  
> Url: https://github.com/boostorg/wave/pull/75#discussion_r385947236  

Hmmm, I think you're right. `__VA_OPT__` is to be treated as a function like macro... Please disregard this comment.

> Username: jefftrull  
> Created_at: 2020-02-28 22:27:30 UTC  
> Updated_at: 2020-03-06 00:38:34 UTC  
> Url: https://github.com/boostorg/wave/pull/75#discussion_r385951453  

OK so when I replace the call to `expand_replacement_list` with this simple copy (which seems to be what `replace_ellipsis` does):  
```c++  
std::copy(cstart, cit, std::inserter(expanded, expanded.end()));  
```  
I get some unit test failures, all of which seem to be that `__VA_ARGS__` is not replaced in the output.


---

## Review 6 [Commented]

> Username: jefftrull  
> Created_at: 2020-02-28 15:47:58 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/wave/pull/75#pullrequestreview-366495383  

📁 include/boost/wave/util/cpp_macromap_utils.hpp

```diff
 352 |+ template <typename ContainerT>
 353 |+ inline bool
 354 |+ is_blank_only (ContainerT const &argument)
```

> Username: jefftrull  
> Created_at: 2020-02-28 15:47:58 UTC  
> Updated_at: 2020-03-06 00:38:34 UTC  
> Url: https://github.com/boostorg/wave/pull/75#discussion_r385770738  

@hkaiser would love your thoughts here. The purpose of this function is to help detect "no variadic argument supplied" cases. I began with  
1. ellipsis index == argument count (so no argument present)  
  
but found I had to add  
  
2. argument consists only of whitespace (present but empty?)  
  
and then modify it to  
  
3. argument consists only of whitespace or placemarkers (thus "is blank")  
  
to get it to work, and that just seems hacky.

> Username: hkaiser  
> Created_at: 2020-02-28 15:56:26 UTC  
> Updated_at: 2020-03-06 00:38:34 UTC  
> Url: https://github.com/boostorg/wave/pull/75#discussion_r385775642  

@jefftrull I understand. IIUC, this function is currently used in a context where you want to decide whether a placemarker has to be inserted or not. However, `is_blank_only` will return `true` even if there already is a placemarker, causing for another one to be inserted into the replacement list.   
  
I think using `is_whitespace_only` in this context would be sufficient as you want to insert a placemarker whenever there isn't anything but whitespace in the processed sequence.

> Username: hkaiser  
> Created_at: 2020-02-28 22:21:37 UTC  
> Updated_at: 2020-03-06 00:38:34 UTC  
> Url: https://github.com/boostorg/wave/pull/75#discussion_r385949490  

I think this is resolved as discussions above. Thanks!


---

## Review 7 [Commented]

> Username: hkaiser  
> Created_at: 2020-02-28 20:38:24 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/wave/pull/75#pullrequestreview-366670995  

📁 include/boost/wave/util/cpp_macromap_predef.hpp

```diff
 250 |+                 { "__STDC_VERSION__", T_INTLIT, "199901L" },
 251 |+                 { "__STDC_HOSTED__", T_INTLIT, "0" },
 252 |+                 { "__WAVE_HAS_VARIADICS__", T_INTLIT, "1" },
```

> Username: hkaiser  
> Created_at: 2020-02-28 20:38:23 UTC  
> Updated_at: 2020-03-06 00:38:34 UTC  
> Url: https://github.com/boostorg/wave/pull/75#discussion_r385911293  

Should we define a `{ "__WAVE_HAS_VA_OPT__", T_INTLIT, "1" },` to enable for user code to detect support for it? I'm on the fence here whether this is necessary...

> Username: jefftrull  
> Created_at: 2020-02-28 21:11:05 UTC  
> Updated_at: 2020-03-06 00:38:34 UTC  
> Url: https://github.com/boostorg/wave/pull/75#discussion_r385923814  

I'm inclined to leave it out :) but maybe @eldiener would have an opinion on the subject.

> Username: jefftrull  
> Created_at: 2020-03-03 18:12:06 UTC  
> Updated_at: 2020-03-06 00:38:34 UTC  
> Url: https://github.com/boostorg/wave/pull/75#discussion_r387201720  

If there are no objections, can we leave this out for now until someone has an application?


---

## Review 8 [Commented]

> Username: hkaiser  
> Created_at: 2020-02-28 21:40:34 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/wave/pull/75#pullrequestreview-366702037  

📁 include/boost/wave/util/cpp_macromap.hpp

```diff
 994 |+ 
 995 |+             if (IS_EXTCATEGORY((*cit), OptParameterTokenType)) {
 996 |+                 BOOST_ASSERT(boost::wave::need_va_opt(ctx.get_language()));
```

> Username: hkaiser  
> Created_at: 2020-02-28 21:40:34 UTC  
> Updated_at: 2020-03-06 00:38:34 UTC  
> Url: https://github.com/boostorg/wave/pull/75#discussion_r385935111  

I think this should actually be an error. Otherwise applications will silently fail if they configure the library for C++11 but encounter a `__VA_OPT__` (I have seen this assertion fire in the `wave --c++11 ...`, which is unexpected and obscure).

> Username: jefftrull  
> Created_at: 2020-02-28 21:48:48 UTC  
> Updated_at: 2020-03-06 00:38:34 UTC  
> Url: https://github.com/boostorg/wave/pull/75#discussion_r385938183  

I am following the pattern from [this line](https://github.com/jefftrull/wave/blob/60ebbd80e35cd8a7fffc71870f7be22e3a0ad4ce/include/boost/wave/util/cpp_macromap.hpp#L988); is `__VA_OPT__` a different situation in this way?

> Username: hkaiser  
> Created_at: 2020-02-28 22:22:21 UTC  
> Updated_at: 2020-03-06 00:38:34 UTC  
> Url: https://github.com/boostorg/wave/pull/75#discussion_r385949713  

Right, it's probably wrong for `__VA_ARGS__` above as well. :/

> Username: jefftrull  
> Created_at: 2020-02-28 22:33:46 UTC  
> Updated_at: 2020-03-06 00:38:34 UTC  
> Url: https://github.com/boostorg/wave/pull/75#discussion_r385953487  

I'll add unit tests and modify the checking code.

> Username: jefftrull  
> Created_at: 2020-03-03 20:48:57 UTC  
> Updated_at: 2020-03-06 00:38:34 UTC  
> Url: https://github.com/boostorg/wave/pull/75#discussion_r387284585  

I have an alternative proposal:  
Let's keep the assert. "This should never happen" is actually a sensible idea here, because `T_EXTRAPARAMETER`/`T_OPTPARAMETER` are marked by a previous parsing step.  
Instead, maybe we can add a check for the language [at that prior point](https://github.com/jefftrull/wave/blob/f6f71ddb8cde9b99dd36db79beb75629e4c779a1/include/boost/wave/util/macro_definition.hpp#L111) - a context is available for passing into `replace_parameters()`...  
  
What do you think?

> Username: hkaiser  
> Created_at: 2020-03-03 20:59:32 UTC  
> Updated_at: 2020-03-06 00:38:34 UTC  
> Url: https://github.com/boostorg/wave/pull/75#discussion_r387290076  

Sounds good to me! Thanks!

> Username: jefftrull  
> Created_at: 2020-03-04 01:19:26 UTC  
> Updated_at: 2020-03-06 00:38:34 UTC  
> Url: https://github.com/boostorg/wave/pull/75#discussion_r387397130  

OK so I'm back again :)  
I can reproduce this issue with `__VA_OPT__` but not `__VA_ARGS__`. After further investigation I think that `__VA_ARGS__`, and thus the first location, is protected against this assertion firing because:  
  
1. `--c++11` implies `need_variadics()`  
2. without `--c++11` (or `--c++20`) defining a macro with ellipsis parameters fails with `ill formed #define directive`  
3. without ellipsis no tokens get marked as `EXTPARAMETER`/`OPTPARAMETER`  
  
so I think [the first assertion](https://github.com/jefftrull/wave/blob/60ebbd80e35cd8a7fffc71870f7be22e3a0ad4ce/include/boost/wave/util/cpp_macromap.hpp#L988) is fine. If you can find a test case that shows otherwise I would be glad to see it.  
  
The second one, on the other hand, needs a language check for the case when we run with `--c++11`. I'll put both checks in anyway for the sake of consistency...

> Username: hkaiser  
> Created_at: 2020-03-04 01:42:07 UTC  
> Updated_at: 2020-03-06 00:38:34 UTC  
> Url: https://github.com/boostorg/wave/pull/75#discussion_r387404047  

Thanks for your investigation and that analysis. I think your proposed solution is fine.


---

## Review 9 [Approved]

> Username: hkaiser  
> Created_at: 2020-03-06 01:56:00 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/wave/pull/75#pullrequestreview-370038124  

This looks good to me now! Thanks a lot!

---
