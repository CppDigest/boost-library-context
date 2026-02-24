# #21 - MSVC: using BOOST_PP_STRINGIZE with argument containing commas [Closed]

> Username: tobias-loew  
> Created at: 2018-10-06 09:52:32 UTC  
> Updated at: 2018-10-26 22:44:59 UTC  
> Closed at: 2018-10-26 22:44:59 UTC  
> Url: https://github.com/boostorg/preprocessor/issues/21  

Hi,  
  
the current version VS 2015 Update 3 compiler emits warning   
warning C4002: too many actual parameters for macro 'BOOST_PP_STRINGIZE_I'  
on  
  
#define TEST 1,2,3,4  
auto text = BOOST_PP_STRINGIZE(TEST);  
  
and it only produces "1" as output.  
Using the non-MSVC version of BOOST_PP_STRINGIZE works correct!

---

## Comment 1

> Username: eldiener  
> Created at: 2018-10-15 19:53:31 UTC  
> Updated at: 2018-10-15 19:53:49 UTC  
> Url: https://github.com/boostorg/preprocessor/issues/21#issuecomment-429990378  

I am actually surprised your case works on any compiler since even on non-MSVC compilers BOOST_PP_STRINGIZE(x) expands to BOOST_PP_STRINGIZE_I(x) and when x = 1,2,3,4 you end up with   
BOOST_PP_STRINGIZE_I(1,2,3,4) which is a mismatch between the number of parameters which BOOST_PP_STRINGIZE_I takes ( 1 ) and the number of arguments you are passing to it ( 4 ).

---

## Comment 2

> Username: eldiener  
> Created at: 2018-10-16 21:05:56 UTC  
> Url: https://github.com/boostorg/preprocessor/issues/21#issuecomment-430400734  

I have pushed a fix to the 'develop' branch which fixes the problem you have reported, not only for VC++ but for all other compilers as long as variadic macro support is on.  The only problem I am not able to fix concerns VC++ and passing emptiness to the BOOST_PP_STRINGIZE and BOOST_PP_WSTRINGIZE macros, which is perfectly legal and should result in "" or L "" as the expansion. Other compilers ( gcc, clang ) work with an empty parameter but VC++ does not. I do not think this is fixable in the preprocessor library but I will probably add a fix to this in the VMD library, which has a much better variadic test for detecting emptiness, which I can then use to fix the VC++ problem. However passing emptiness to the BOOST_PP_STRINGIZE or BOOST_PP_WSTRINGIZE macros is something that is much more rare than passing variadic data ( 1,2,3,4 as in your example ), and my fix on 'develop' now allows this. Thanks for reporting this.

---

## Comment 3

> Username: tobias-loew  
> Created at: 2018-10-20 07:47:40 UTC  
> Url: https://github.com/boostorg/preprocessor/issues/21#issuecomment-431558212  

Thanks a lot. I've also tested with the latest VS 2017 (15.8.5) with conformant preprocessor (https://blogs.msdn.microsoft.com/vcblog/2018/07/06/msvc-preprocessor-progress-towards-conformance/):  
- with the MSVC-specific BOOST-PP  macros: same as with VS 2015 (neither commas nor emptiness supported)  
- with the "generic" BOOST-PP macros: also same as with VS 2015 (commas supported, but not emptiness)  
  
So it seems, nothing improved for this case.

---

## Comment 4

> Username: eldiener  
> Created at: 2018-10-20 13:16:25 UTC  
> Url: https://github.com/boostorg/preprocessor/issues/21#issuecomment-431580945  

There is now a stringize test ( stringize.cxx include by stringize.cpp and stringize.c ) run as part of the tests in the test jamfile. I manipulated this test so that emptiness when using BOOST_PP_STRINGIZE and BOOST_PP_WSTRINGIZE is not being tested for vc++, but commas are being tested and it succeeds with vc++ in my tests as long as BOOST_PP_VARIADICS is 1, which it is by default with vc++ for all but very old versions. I am using the default preprocessor, which is not very compliant to the C++ standard preprocessor specs.  
  
As I understand it you are testing with the conformant preprocessor in VS 2017 (15.8.5) using the /experimental:preprocessor compiler switch. Is that correct ? I have not updated Boost PP in order to treat vc++ as a conformant preprocessor yet. This is the reason you are seeing what you specify above. Can you test without the /experimental:preprocessor switch to verify that vc++ is handling the case with commas ?

---

## Comment 5

> Username: eldiener  
> Created at: 2018-10-20 18:18:21 UTC  
> Url: https://github.com/boostorg/preprocessor/issues/21#issuecomment-431606085  

I have updated Boost PP with support for vc++ /experimental:preprocessor. BOOST_PP_STRINGIZE and BOOST_PP_WSTRINGIZE should work with commas.

---

## Comment 6

> Username: eldiener  
> Created at: 2018-10-20 19:46:26 UTC  
> Updated at: 2018-10-20 19:47:15 UTC  
> Url: https://github.com/boostorg/preprocessor/issues/21#issuecomment-431612496  

Actually with the new vc++ preprocessor support BOOST_PP_STRINGIZE and BOOST_PP_WSTRINGIZE also work with emptiness also.  
  
However the new preprocessor report still gives "warning C4003: not enough arguments for function-like macro invocation" in quite a few cases when the number of arguments are perfectly correct. It also gives that warning message correctly in other cases but then continues on rather than create a compiler error. Finally there is some sort of problem with the new preprocessor and the BOOST_PP_ITERATE constructs. I will try to report some of these bugs to Microsoft if I can isolate them to simple examples but doing so will not be easy. Still if you do not use the Boost PP iterate facilities you should be OK using the new experimental preprocessor and Boost PP with the 'develop' branch. Once this cycles through the testing cycle I will push the change to the 'master' branch for the next Boost release. Maybe by that time Microsoft will have fixed the bugs that exist with their new preprocessor. If you discover any further bugs with the new preprocessor and Boost PP, please feel free to bring it up here or in a PR. I do appreciate your reporting this current issue.
