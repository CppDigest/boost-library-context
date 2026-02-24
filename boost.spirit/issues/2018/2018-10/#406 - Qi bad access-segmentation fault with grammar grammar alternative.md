# #406 - Qi bad access/segmentation fault with grammar grammar alternative [Closed]

> Username: Superlokkus  
> Created at: 2018-10-13 14:06:54 UTC  
> Updated at: 2019-02-09 01:34:16 UTC  
> Closed at: 2018-10-13 14:47:01 UTC  
> Url: https://github.com/boostorg/spirit/issues/406  

Hi,  
  
I'am writing a RTSP1.0 parser with Qi, so I created a grammar for response message types and one for request message types. All worked perfectly so far, unit tested them, using no state or semantic actions but the attributed types struct bindings. Now I just wanted to combine them as a simple alternative into a boost::variant. (see https://github.com/Superlokkus/boost_spirit_bug/blob/parser_vtable_bad_access_minimal_example/src/streaming_lib/include/rtsp_message.hpp#L142 )  
(so that I can just parse in general rtsp messages, which is need for rtsp clients since they could also get request from the server, and so messages are even rfc defined as the alternative)  
  
But this simple grammar which combines the mentioned grammars, explodes with segmentation faults/memory access violation: invalid permissions in `boost::function4` with `fusion` while parsing.   
   
I tested it with boost 1.66 Apple LLVM version 10.0.0 (clang-1000.11.45.2) on a mac host and with boost 1.69 gcc (SUSE Linux) 7.3.1 20180323 [gcc-7-branch revision 258812] on a suse x86-64 host.  
  
I would paste a wandbox link, but boost::test or boost::spirit needs to much ram for compiling and quits because of this. Find a stack trace attached if you can not clone and execute it yourself.  
  
**I can try to make the example more minimal, but I hope since the grammars don't have custom side effects and valid grammars, that you can already use it.**  
  
**Code/Repo**: https://github.com/Superlokkus/boost_spirit_bug  
[boost_spirt_stack.txt](https://github.com/boostorg/spirit/files/2475525/boost_spirt_stack.txt)

---

## Comment 1

> Username: Kojoley  
> Created at: 2018-10-13 14:47:01 UTC  
> Url: https://github.com/boostorg/spirit/issues/406#issuecomment-429547835  

You are binding to a temporary here  
```cpp  
        start %= rtsp_request_grammar<Iterator>{} | rtsp_response_grammar<Iterator>{};  
```

---

## Comment 2

> Username: Superlokkus  
> Created at: 2018-10-13 14:49:49 UTC  
> Url: https://github.com/boostorg/spirit/issues/406#issuecomment-429548041  

You are right. I'm so sorry. What was I thinking.

---

## Comment 3

> Username: Kojoley  
> Created at: 2018-10-13 14:53:51 UTC  
> Url: https://github.com/boostorg/spirit/issues/406#issuecomment-429548297  

You can try to use ASan to catch these type of bugs and prevent yourself from wasting anybody's time :-)
