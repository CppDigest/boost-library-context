# #43 - wave sample - compiler error [C2955] with Visual Studio 2017 [Closed]

> Username: malickf  
> Created at: 2019-04-14 18:18:52 UTC  
> Updated at: 2019-05-04 10:10:13 UTC  
> Closed at: 2019-05-03 13:18:52 UTC  
> Url: https://github.com/boostorg/wave/issues/43  

Compilation of the wave sample with Visual Studio 2017 (toolset msvc-14.1) fails with error code [C2955](https://docs.microsoft.com/fr-fr/cpp/error-messages/compiler-errors-2/compiler-error-c2955?view=vs-2019). It seems that since vs 2017, `we cannot use a class template or class generic as an identifier without a template or generic argument list`.  
  
First error :   
  
 [`boost::wave::cpplexer::re2clex::Scanner`](https://github.com/boostorg/wave/blob/3d6ad95384bff8d074c211596bf9a3a7c4944f82/samples/waveidl/idllexer/idl_re.hpp#L27)  use of class template requires template argument list.  
  
The `Scanner` is used multiples times without a template currently, for instance here:  https://github.com/boostorg/wave/blob/3d6ad95384bff8d074c211596bf9a3a7c4944f82/samples/waveidl/idllexer/idl_re.cpp#L73),   
  
I have tried to fix, unfortunately without success.

---

## Comment 1

> Username: hkaiser  
> Created at: 2019-04-14 18:51:25 UTC  
> Url: https://github.com/boostorg/wave/issues/43#issuecomment-483039602  

@malickf Thanks for reporting this. You're right we turned this class into a template a while back but apparently forgot to fix the example. @jefftrull, would that be something you could have a look at (I think you changed the Scanner to begin with)?

---

## Comment 2

> Username: jefftrull  
> Created at: 2019-04-14 21:35:02 UTC  
> Url: https://github.com/boostorg/wave/issues/43#issuecomment-483060296  

Ouch, sorry about that. I'll look into it this week. Will try to ensure the examples get built during CI too.

---

## Comment 3

> Username: jefftrull  
> Created at: 2019-04-19 14:23:04 UTC  
> Url: https://github.com/boostorg/wave/issues/43#issuecomment-484910821  

About 50% of the way through this but going on vacation :( Thanks for your patience.  
  
I also found an issue with Filesystem usage - probably related to an API deprecation a few releases back. I could file a separate bug if desired.  
  
You can follow my work here: https://github.com/jefftrull/wave/tree/bugfix/idl_sample_scanner

---

## Comment 4

> Username: jefftrull  
> Created at: 2019-05-03 04:53:30 UTC  
> Url: https://github.com/boostorg/wave/issues/43#issuecomment-488933690  

I built waveidl under VS2017 just now with the above changes; hopefully it will work for you too.

---

## Comment 5

> Username: malickf  
> Created at: 2019-05-04 10:10:13 UTC  
> Url: https://github.com/boostorg/wave/issues/43#issuecomment-489313778  

Thank you very much @jefftrull , I confirm that I can now correctly build the idl sample with Visual Studio 2017 (command:  `b2 toolset=msvc-14.1 link=static `) . Great !
