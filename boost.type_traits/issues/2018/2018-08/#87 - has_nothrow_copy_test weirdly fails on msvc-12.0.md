# #87 - has_nothrow_copy_test weirdly fails on msvc-12.0 [Closed]

> Username: pdimov  
> Created at: 2018-08-23 17:11:03 UTC  
> Updated at: 2018-08-23 19:02:12 UTC  
> Closed at: 2018-08-23 19:02:12 UTC  
> Url: https://github.com/boostorg/type_traits/issues/87  

```  
====== BEGIN OUTPUT ======  
test\has_nothrow_copy_test.cpp:243: The expression: "::boost::has_nothrow_copy<delete_move>::value" had an invalid value (found 1, expected 0)  
  
EXIT STATUS: 1  
====== END OUTPUT ======  
```  
  
I have `define=CI_SUPPRESS_KNOWN_ISSUES` on the command line. Interestingly, Appveyor passes.  
  
I see  
  
```  
#if !(defined(CI_SUPPRESS_KNOWN_ISSUES) && BOOST_WORKAROUND(BOOST_GCC, < 40600)) && !(defined(CI_SUPPRESS_KNOWN_ISSUES) && BOOST_WORKAROUND(BOOST_MSVC, == 1800))  
```  
  
All right, this middle `&&` probably needs to be `||`, but why does Appveyor pass? It's a mystery.

---

## Comment 1

> Username: jzmaddock  
> Created at: 2018-08-23 19:02:12 UTC  
> Url: https://github.com/boostorg/type_traits/issues/87#issuecomment-415532409  

Works for me, and the && is correct too I believe.  
  
If you have a previous build, you may need to pass -a to b2 in order to force a rebuild, otherwise it just runs the same old .exe over again, or does for me anyway.
