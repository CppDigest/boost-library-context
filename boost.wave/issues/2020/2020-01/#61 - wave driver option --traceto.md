# #61 - wave driver option --traceto [Closed]

> Username: quinzio  
> Created at: 2020-01-22 12:00:25 UTC  
> Updated at: 2020-05-22 02:52:10 UTC  
> Closed at: 2020-05-22 02:52:10 UTC  
> Url: https://github.com/boostorg/wave/issues/61  

Hello to everyone,   
I am very new to boost library, so please excuse me if my supposed issue is due to my lack of experience.  
  
I am using the boost wave driver example, that you find in the tools folder of the library.   
The --traceto option does not produce any output, even if I specify a file, or to the std err ( - parameter).   
My example is very basic and obvious.   
Thanks.  
  
  
wave --traceto - myfile.cpp  
  
myfile.cpp:  
  
#define A 1  
#define B 3  
#define C (A + B)  
#define D (2 * C)  
  
int main() {  
    int k = D;  
    return C;  
}

---

## Comment 1

> Username: jefftrull  
> Created at: 2020-01-22 22:02:14 UTC  
> Url: https://github.com/boostorg/wave/issues/61#issuecomment-577406832  

Thanks for the feedback! It looks like a bug to me.  
  
@hkaiser I think maybe this center operator should be an `||` instead of `&&` [here](https://github.com/boostorg/wave/blob/69239bba69f4e78ebb09c1c8abe6118f5735f2ed/tool/trace_macro_expansion.hpp#L1405), what do you think? When I make that change both `-c` and `-t` options work again.  
  
@quinzio give that fix a try and see if it helps you.

---

## Comment 2

> Username: quinzio  
> Created at: 2020-01-23 11:38:33 UTC  
> Url: https://github.com/boostorg/wave/issues/61#issuecomment-577644816  

> Thanks for the feedback! It looks like a bug to me.  
>   
> @quinzio give that fix a try and see if it helps you.  
  
Yep, now it's working and macros expansions are pouring down.  
I changed the line you mentioned from  
        return (flags & trace_macros) && (logging_flags & trace_macros);  
to  
        return (flags & trace_macros) || (logging_flags & trace_macros);  
  
Thanks for the quick reply. Wave is a useful tool to navigate into heaviliy preprocessed software.

---

## Comment 3

> Username: hkaiser  
> Created at: 2020-01-23 14:35:32 UTC  
> Url: https://github.com/boostorg/wave/issues/61#issuecomment-577707732  

I think the initial idea was that you would have to enable the tracing from inside your code using a wave specific `#pragma` (or `_Pragma()`) in addition to specifying the command line flag. This was done in order to be able to limit the generated trace to the macro(s) of interest. Otherwise the information produced tends to be overwhelming. Please see [here](https://www.boost.org/doc/libs/1_72_0/libs/wave/doc/supported_pragmas.html) for details.

---

## Comment 4

> Username: jefftrull  
> Created at: 2020-01-23 17:40:10 UTC  
> Url: https://github.com/boostorg/wave/issues/61#issuecomment-577789854  

Thanks @hkaiser I do see that now. I was also confused though :) Shall we call it a documentation bug? I think adding some information to [the invocation page](https://www.boost.org/doc/libs/1_72_0/libs/wave/doc/wave_driver.html) could help.

---

## Comment 5

> Username: hkaiser  
> Created at: 2020-01-23 17:51:58 UTC  
> Url: https://github.com/boostorg/wave/issues/61#issuecomment-577794418  

@jefftrull yes, I agree. Let's rather fix the documentation. Thanks!

---

## Comment 6

> Username: jefftrull  
> Created at: 2020-01-23 23:11:17 UTC  
> Url: https://github.com/boostorg/wave/issues/61#issuecomment-577920792  

OK I tried inserting `#pragma wave(enable)` and `#pragma wave(disable)` around the sections of interest in the test code and it works with the old code.

---

## Comment 7

> Username: hkaiser  
> Created at: 2020-01-23 23:27:32 UTC  
> Url: https://github.com/boostorg/wave/issues/61#issuecomment-577925246  

FWIW, there is a whole section about the `#pragma wave trace()` [here](https://www.boost.org/doc/libs/1_72_0/libs/wave/doc/tracing_facility.html).

---

## Comment 8

> Username: quinzio  
> Created at: 2020-01-24 07:40:32 UTC  
> Url: https://github.com/boostorg/wave/issues/61#issuecomment-578025081  

Thanks again for your prompt help, and it turned out my lack of experience was the cause of the issue.   
It's just another case of "read the ****ing manual". :)   
Problem is that you could spend your entire life just by reading manuals. :)  
  
More seriously, perhaps there could be 2 tracing options, one that requires enabling #pragmas, and one that doesn't.   
Or in case that no enabling pragma is found in the code, a warning could appear in the log.  
Just my idea.

---

## Comment 9

> Username: hkaiser  
> Created at: 2020-01-24 08:03:10 UTC  
> Url: https://github.com/boostorg/wave/issues/61#issuecomment-578030776  

> More seriously, perhaps there could be 2 tracing options, one that requires enabling #pragmas, and one that doesn't.  
> Or in case that no enabling pragma is found in the code, a warning could appear in the log.  
> Just my idea.  
  
I think this is a nice idea and it shouldn't be too hard to implement in the wave driver. No knowledge of the internals of the library are necessary for this. Would you be interested in preparing a PR implementing such an option?

---

## Comment 10

> Username: jefftrull  
> Created at: 2020-04-28 15:01:33 UTC  
> Url: https://github.com/boostorg/wave/issues/61#issuecomment-620662380  

@quinzio the new Boost release containers the updated documentation which you can see [here](https://www.boost.org/doc/libs/1_73_0/libs/wave/doc/wave_driver.html). If you have no objections I'd like to close this bug. Thanks!
