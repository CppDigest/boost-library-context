# #10 - UBSAN related to any and visibility [Closed]

> Username: jeking3  
> Created at: 2019-04-24 22:12:26 UTC  
> Updated at: 2019-05-01 07:14:43 UTC  
> Closed at: 2019-05-01 07:14:43 UTC  
> Url: https://github.com/boostorg/any/issues/10  

This is from the mailing list, with an excellent summary from @pdimov :  
  
Chris Rorvick wrote:  
> I am seeing my GCC ubsan builds littered with "downcast of address   
> xxxxxxxx which does not point to an object of type 'holder'" errors [3]   
> coming from Boost.Program_options (Boost 1.69).  The issue has been   
> documented in various places [1][2] but I do not see anything on the   
> mailing list in recent months; apologies if I have missed something.  
>  
> A workaround is proposed on the StackOverflow post [1].  Does anyone have   
> any thoughts on this?  
>  
> Many thanks!  
>  
> Chris  
>  
> [1]   
> https://stackoverflow.com/questions/38987728/why-does-boostany-exhibit-undefined-behaviour-in-boostprogram-options  
> [2] https://github.com/boostorg/program_options/issues/76  
> [3]  
> .../boost/boost/any.hpp:249:17: runtime error: downcast of address  
> 0x602000053610 which does not point to an object of type 'holder'  
> 0x602000053610: note: object is of type 'boost::any::holder<std::string>'  
>  6f 00 80 67  d0 91 ec 88 fe 7f 00 00  68 33 09 00 40 60 00 00  00 00  
> 00 00 00 00 00 00  00 00 00 00  
>               ^~~~~~~~~~~~~~~~~~~~~~~  
>               vptr for 'boost::any::holder<std::string>'  
...  
  
This doesn't seem to be the same issue as in the SO link. The cv qualifiers   
are now being stripped by the constructor at  
  
https://github.com/boostorg/any/blob/develop/include/boost/any.hpp#L49  
  
twice (once by `decay` and once by `remove_cv` for added safety) and in your   
message the held object is non-const:  
  
> note: object is of type 'boost::any::holder<std::string>'  
  
My guess is that `holder` needs BOOST_SYMBOL_VISIBLE. That is,  
  
    template<typename ValueType>  
    class holder : public placeholder  
  
at   
https://github.com/boostorg/any/blob/develop/include/boost/any.hpp#L168-L169   
needs to be  
  
    template<typename ValueType>  
    class BOOST_SYMBOL_VISIBLE holder : public placeholder

---

## Comment 1

> Username: apolukhin  
> Created at: 2019-04-25 20:12:41 UTC  
> Url: https://github.com/boostorg/any/issues/10#issuecomment-486820899  

You're probably right. Many thanks for the investigations and fix!

---

## Comment 2

> Username: jeking3  
> Created at: 2019-04-26 16:05:55 UTC  
> Url: https://github.com/boostorg/any/issues/10#issuecomment-487111720  

I didn't see a corresponding unit test for this @apolukhin, so I spun up boost-ci on my fork to add jobs like valgrind, cppcheck, *ubsan*, coverity scan, and more compilers, language levels, and platforms (cygwin 32/64 and mingw 32/64).  If interested see here: https://github.com/jeking3/any/pull/1 - none of the jobs found anything of issue.  I can submit a PR if you'd like, or take from it what you find useful.

---

## Comment 3

> Username: apolukhin  
> Created at: 2019-04-26 22:39:11 UTC  
> Url: https://github.com/boostorg/any/issues/10#issuecomment-487222726  

I'm planning to rewrite the internals and drop all the virtual functions in #11. This should remove all the nasty RTTI related warnings. It also reduces the size of programs that use boost::any. For example program_options lost about 5KB of weight with the new approach.

---

## Comment 4

> Username: crorvick  
> Created at: 2019-04-30 15:07:10 UTC  
> Url: https://github.com/boostorg/any/issues/10#issuecomment-487989977  

Thanks everyone.  FYI, I upgraded to Boost 1.70 yesterday and that seems to have cleared up the specific issue I was reporting.  I had some technical hurdles to doing this upgrade earlier, so I tried to look for recent changes that may have fixed the issue before reporting it and did not see anything.  So not sure what the specific fix was, but the ubsan errors I was seeing in Boost.Program_options are no longer occurring.

---

## Comment 5

> Username: apolukhin  
> Created at: 2019-05-01 07:14:42 UTC  
> Url: https://github.com/boostorg/any/issues/10#issuecomment-488224707  

Probably it was fixed in https://github.com/boostorg/any/commit/40a0e4b89697cba0b1784867a29773e5f074e55b . That fix went into Boost 1.69
