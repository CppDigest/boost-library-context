# #202 - YYMARKER not updated when fill called on BOOST_WAVE_BSIZE boundary [Closed]

> Username: chisolm  
> Created at: 2024-01-24 23:23:39 UTC  
> Updated at: 2024-01-30 18:16:09 UTC  
> Closed at: 2024-01-30 18:16:09 UTC  
> Url: https://github.com/boostorg/wave/issues/202  

Hello,  
  
I'm attempting to use wave as a pre-processor for a C/PASCAL like DSL.  We are trying to migrate away from a 20 year old MSVC pre-processor.  I'm using the command line wave driver to see if this is feasible for us.  When working through our DSL code base I was hitting an error:  
  
XXFILENAMEXX:5715:12: exception caught: bad allocation  
  
It's in part due to the fact that our language supports linefeeds in the middle of strings.  I'd like to change it, but that design decision was 30 years ago and a couple million lines of code ago.  
  
This happens on about a BOOST_WAVE_BSIZE boundary.  
  
In https://github.com/boostorg/wave/blob/543a0e9e1d0e6190a571c940945a68fbded5da56/include/boost/wave/cpplexer/re2clex/cpp_re.hpp#L173   
in fill() when it is called in the middle of lexing a quoted string which is iterating through (lex states in cpp_re.inc) yy14 to yy99 to yy98(fill called here), the YYMARKER variable is not updated on the return from fill.  
  
When there is a linefeed in the middle of the quoted string(or other character) that causes a jump to yy100, YYCURSOR is set from the bad YYMARKER value.  
  
Then when count_backslash_newlines is called:  
https://github.com/boostorg/wave/blob/543a0e9e1d0e6190a571c940945a68fbded5da56/include/boost/wave/cpplexer/re2clex/cpp_re.hpp#L152  
  
The diff value becomes something like a small negative number or 2^64 in size_t.  Then when returning to lexer() the token category is UnknownTokenType, the default case does similar math and tries to allocate huge memory.  In my case the flex_string() constructor is call with a request for too much memory.  
  
My target goes does have previous cases of linefeeds in quoted strings.  I have not worked through the path it takes when it successfully handles those.  I can see that It does preserve the linefeed in the string.  
  
I think there is one bug here and maybe 2 or 3 bugs here:  
1. YYMARKER is not updated on return from the fill() call  
2. in fill() s->ptr ends up pointing to a point earlier than the buffer allocs.  This chunk of code probably needs to copy over chars from the earlier of s->tok or s->ptr since the s->ptr can point to an earlier mark.  I cannot see how s->ptr is used in my bug case, so it may not be an issue for this case.  
https://github.com/boostorg/wave/blob/543a0e9e1d0e6190a571c940945a68fbded5da56/include/boost/wave/cpplexer/re2clex/cpp_re.hpp#L189  
3. It would be nice to handle the UnknownTokenType in lexer() a bit better, but that would not be needed if the other two issues were fixed.  
  
My apologies for the long writeup, let me know if you'd actually perfer a PR.  I don't open contribute much, so I'm not sure about your process.  
  
thanks

---

## Comment 1

> Username: chisolm  
> Created at: 2024-01-24 23:24:56 UTC  
> Url: https://github.com/boostorg/wave/issues/202#issuecomment-1909089336  

Also, I can submit an anonymized test file.  It's about 200k of 'x' characters and then a quoted string with a linefeed in it.

---

## Comment 2

> Username: jefftrull  
> Created at: 2024-01-25 08:02:05 UTC  
> Url: https://github.com/boostorg/wave/issues/202#issuecomment-1909588210  

This is great detective work and I think we would be happy to see your test file as well! Thanks for the report.

---

## Comment 3

> Username: jefftrull  
> Created at: 2024-01-25 08:08:41 UTC  
> Url: https://github.com/boostorg/wave/issues/202#issuecomment-1909605646  

PS: I am sorry (but impressed) you spent so much time with the re2c output. If you are not already aware of [its source file](https://github.com/boostorg/wave/blob/develop/include/boost/wave/cpplexer/re2clex/cpp.re) and [the re2c manual](https://re2c.org/manual/manual_c.html) they may prove helpful.

---

## Comment 4

> Username: chisolm  
> Created at: 2024-01-25 23:46:28 UTC  
> Url: https://github.com/boostorg/wave/issues/202#issuecomment-1911178853  

[linefeedfail2.txt](https://github.com/boostorg/wave/files/14058631/linefeedfail2.txt)  
  
It really wasn't too hard to chase this down from the throw of the bad alloc.  Frankly the re2c generated state machine was way easier to debug than any table lexer I've ever touched.  I gather that traceability is supposed to be a feature of re2c.  I liked it a lot.   Frankly I spent way more time trying to get boost/wave setup to use a debugger than I did debugging it.  
  
If you have any pointers to setting up a development environment for boost, I'd love to see that.  
  
Here is the test file.  I was running with just:  
wave.exe linefeedfail2.f -E  
  
uploaded the file as .txt to make the file type filter happy.  I realized that this file is from a windows box, it has CRLF line endings.  I'm fairly certain I didn't see CR in the input stream as I was debugging.  This file is about 5700 characters longer than I think it should take to trigger the bug on the 196608(BOOST_WAVE_BSIZE ) interval.  The file is 202353 - 196608 = 5745 longer than the interval, subtract about 5716 characters probably puts the fill() operation in the middle of the text string with the linefeed in it.  So, you might have to shorten the file by 5716 characters on the front end to make it replicate on linux if the endings get changed.  
  
I tried to submit a bug fix branch with this change, but was getting permission denied from github.com.  :  
  
diff --git a/include/boost/wave/cpplexer/re2clex/cpp_re.hpp b/include/boost/wave/cpplexer/re2clex/cpp_re.hpp  
index f054038..8f1f180 100644  
--- a/include/boost/wave/cpplexer/re2clex/cpp_re.hpp  
+++ b/include/boost/wave/cpplexer/re2clex/cpp_re.hpp  
@@ -38,8 +38,10 @@  
 #define YYMARKER  marker  
 #define YYFILL(n)                                                             \  
     {                                                                         \  
+        s->ptr = marker;                                                      \  
         cursor = uchar_wrapper(fill(s, cursor), cursor.column);               \  
         limit = uchar_wrapper (s->lim);                                       \  
+        marker = s->ptr;                                                      \  
     }                                                                         \  
     /**/  
  
I think that fits with how marker and s->ptr are meant to be used.

---

## Comment 5

> Username: jefftrull  
> Created at: 2024-01-26 16:58:31 UTC  
> Url: https://github.com/boostorg/wave/issues/202#issuecomment-1912382324  

@chisolm re: _setting up a development environment for boost_, I would follow the "build from source" instructions for Boost from [here](https://www.boost.org/doc/libs/1_84_0/more/getting_started/index.html), then retarget the Wave submodule to point to your own repo.  I build the standalone Wave like this:  
  
    cd my_boost_repo/libs/wave  
    ../../b2 variant=debug tool/build test  
  
which builds the standalone `wave` binary and runs the unit tests, both with symbols.

---

## Comment 6

> Username: jefftrull  
> Created at: 2024-01-26 17:09:47 UTC  
> Url: https://github.com/boostorg/wave/issues/202#issuecomment-1912397494  

I ran the standalone wave tool on your test file and was unable to reproduce a segfault. The output produced seems to be at first glance identical to the input but with CR removed and some line number directives added. Can you help me reproduce this?
