# #79 Added parsing for clang-cl of BOOST_LEAF_PRETTY_FUNCTION [Merged]

> Username: Mishura4  
> Created at: 2025-02-17 23:23:32 UTC  
> Updated at: 2025-02-23 07:27:24 UTC  
> Merged at: 2025-02-23 07:27:10 UTC  
> Closed at: 2025-02-23 07:27:10 UTC  
> Url: https://github.com/boostorg/leaf/pull/79  

Fixes #78

---

## Comment 1

> Username: Mishura4  
> Created_at: 2025-02-17 23:32:50 UTC  
> Url: https://github.com/boostorg/leaf/pull/79#issuecomment-2664228234  

Tested on clang-cl 20.1.0-rc2 & MSVC 14.43.34808

---

## Comment 2

> Username: zajo  
> Created_at: 2025-02-18 01:14:32 UTC  
> Url: https://github.com/boostorg/leaf/pull/79#issuecomment-2664335774  

Thanks, this looks OK but I think we should add compatible parsing for old clang style as well as gcc style on windows. Do you want to update your pull request with these more complete changes? It'd be better if the commit comes from you since you can test (I should add this to the unit tests but I haven't yet).  
  
```  
    template <class T>  
    BOOST_LEAF_ALWAYS_INLINE r p()  
    {  
        // C++11 compile-time parsing of __PRETTY_FUNCTION__/__FUNCSIG__. The sizeof hacks are a  
        // workaround for older GCC versions, where __PRETTY_FUNCTION__ is not constexpr, which triggers  
        // compile errors when used in constexpr expressinos, yet evaluating a sizeof exrpession works.  
  
        // We don't try to recognize the compiler based on compiler-specific macros. Any compiler/version  
        // is supported as long as it uses one of the formats we recognize.  
  
        // Unrecognized __PRETTY_FUNCTION__/__FUNCSIG__ formats will result in compiler diagnostics.  
        // In that case, please file an issue on https://github.com/boostorg/leaf.  
  
#define BOOST_LEAF_P(P) (sizeof(char[1 + detail::check_prefix(BOOST_LEAF_PRETTY_FUNCTION, P)]) - 1)  
        // clang style:  
        int const p01 = BOOST_LEAF_P("r boost::leaf::n::p() [T = ");  
        int const p02 = BOOST_LEAF_P("r __cdecl boost::leaf::n::p() [T = ");  
        int const p03 = BOOST_LEAF_P("r __stdcall boost::leaf::n::p() [T = ");  
        int const p04 = BOOST_LEAF_P("r __fastcall boost::leaf::n::p() [T = ");  
        // old clang style:  
        int const p05 = BOOST_LEAF_P("boost::leaf::n::r boost::leaf::n::p() [T = ");  
        int const p06 = BOOST_LEAF_P("boost::leaf::n::r __cdecl boost::leaf::n::p() [T = ");  
        int const p07 = BOOST_LEAF_P("boost::leaf::n::r __stdcall boost::leaf::n::p() [T = ");  
        int const p08 = BOOST_LEAF_P("boost::leaf::n::r __fastcall boost::leaf::n::p() [T = ");  
        // gcc style:  
        int const p09 = BOOST_LEAF_P("boost::leaf::n::r boost::leaf::n::p() [with T = ");  
        int const p10 = BOOST_LEAF_P("boost::leaf::n::r __cdecl boost::leaf::n::p() [with T = ");  
        int const p11 = BOOST_LEAF_P("boost::leaf::n::r __stdcall boost::leaf::n::p() [with T = ");  
        int const p12 = BOOST_LEAF_P("boost::leaf::n::r __fastcall boost::leaf::n::p() [with T = ");  
        // msvc style, struct:  
        int const p13 = BOOST_LEAF_P("struct boost::leaf::n::r __cdecl boost::leaf::n::p<struct ");  
        int const p14 = BOOST_LEAF_P("struct boost::leaf::n::r __stdcall boost::leaf::n::p<struct ");  
        int const p15 = BOOST_LEAF_P("struct boost::leaf::n::r __fastcall boost::leaf::n::p<struct ");  
        // msvc style, class:  
        int const p16 = BOOST_LEAF_P("struct boost::leaf::n::r __cdecl boost::leaf::n::p<class ");  
        int const p17 = BOOST_LEAF_P("struct boost::leaf::n::r __stdcall boost::leaf::n::p<class ");  
        int const p18 = BOOST_LEAF_P("struct boost::leaf::n::r __fastcall boost::leaf::n::p<class ");  
        // msvc style, enum:  
        int const p19 = BOOST_LEAF_P("struct boost::leaf::n::r __cdecl boost::leaf::n::p<enum ");  
        int const p20 = BOOST_LEAF_P("struct boost::leaf::n::r __stdcall boost::leaf::n::p<enum ");  
        int const p21 = BOOST_LEAF_P("struct boost::leaf::n::r __fastcall boost::leaf::n::p<enum ");  
        // msvc style, built-in type:  
        int const p22 = BOOST_LEAF_P("struct boost::leaf::n::r __cdecl boost::leaf::n::p<");  
        int const p23 = BOOST_LEAF_P("struct boost::leaf::n::r __stdcall boost::leaf::n::p<");  
        int const p24 = BOOST_LEAF_P("struct boost::leaf::n::r __fastcall boost::leaf::n::p<");  
#undef BOOST_LEAF_P  
  
#define BOOST_LEAF_S(S) (sizeof(char[1 + detail::check_suffix(BOOST_LEAF_PRETTY_FUNCTION, S)]) - 1)  
        // clang/gcc style:  
        int const s01 = BOOST_LEAF_S("]");  
        // msvc style:  
        int const s02 = BOOST_LEAF_S(">(void)");  
#undef BOOST_LEAF_S  
  
        char static_assert_unrecognized_pretty_function_format_please_file_github_issue[sizeof(  
            char[  
                (s01 && (1 == (!!p01 + !!p02 + !!p03 + !!p04 + !!p05 + !!p06 + !!p07 + !!p08 + !!p09 + !!p10 + !!p11 + !!p12)))  
                ||  
                (s02 && (1 == (!!p13 + !!p14 + !!p15 + !!p16 + !!p17 + !!p18 + !!p19 + !!p20 + !!p21 + !!p22 + !!p23 + !!p24)))  
            ]  
        ) * 2 - 1];  
        (void) static_assert_unrecognized_pretty_function_format_please_file_github_issue;  
  
        if( int const p = sizeof(char[1 + !!s01 * (p01 + p02 + p03 + p04 + p05 + p06 + p07 + p08 + p09 + p10 + p11 + p12)]) - 1 )  
            return { BOOST_LEAF_PRETTY_FUNCTION + p, s01 - p };  
        int const p = sizeof(char[1 + !!s02 * (p22 + p23 + p24)]) - 1; // p is not zero, we've static asserted the hell out of it  
        return { BOOST_LEAF_PRETTY_FUNCTION + p, s02 - p };  
    }  
}  
```

---

## Comment 3

> Username: zajo  
> Created_at: 2025-02-18 20:46:28 UTC  
> Url: https://github.com/boostorg/leaf/pull/79#issuecomment-2666895856  

Ah, right, I need to update the GHA stuff, I'm actually working on that. I'll merge this soon.

---

## Comment 4

> Username: Mishura4  
> Created_at: 2025-02-19 18:40:42 UTC  
> Url: https://github.com/boostorg/leaf/pull/79#issuecomment-2669472456  

Sure about old clang, I'll update the pr later today

---

## Comment 5

> Username: Mishura4  
> Created_at: 2025-02-20 16:37:08 UTC  
> Url: https://github.com/boostorg/leaf/pull/79#issuecomment-2672041936  

Done

---

## Comment 6

> Username: Mishura4  
> Created_at: 2025-02-20 16:42:44 UTC  
> Updated_at: 2025-02-20 16:44:03 UTC  
> Url: https://github.com/boostorg/leaf/pull/79#issuecomment-2672057203  

Can't exactly test the older clang style, hopefully it works 🤔  
  
Or gcc-win for that matter

---

## Comment 7

> Username: zajo  
> Created_at: 2025-02-23 07:27:23 UTC  
> Url: https://github.com/boostorg/leaf/pull/79#issuecomment-2676675679  

Thanks!

---
