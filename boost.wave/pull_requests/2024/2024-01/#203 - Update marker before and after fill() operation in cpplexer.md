# #203 Update marker before and after fill() operation in cpplexer [Merged]

> Username: chisolm  
> Created at: 2024-01-26 00:51:37 UTC  
> Updated at: 2024-01-30 18:16:08 UTC  
> Merged at: 2024-01-30 18:16:07 UTC  
> Closed at: 2024-01-30 18:16:08 UTC  
> Url: https://github.com/boostorg/wave/pull/203  

I think this change is in line with how s->ptr and marker are expected to interact.  
  
This is in reference to:  
https://github.com/boostorg/wave/issues/202

---

## Review 1 [Changes requested]

> Username: hkaiser  
> Created_at: 2024-01-26 01:17:56 UTC  
> State: CHANGES_REQUESTED  
> Url: https://github.com/boostorg/wave/pull/203#pullrequestreview-1844953827  

Could you add a test that reproduces the isuue, please?

---

## Comment 2

> Username: jefftrull  
> Created_at: 2024-01-26 16:49:21 UTC  
> Url: https://github.com/boostorg/wave/pull/203#issuecomment-1912370020  

We'll need to take a careful look at this change, as `YYFILL` plays a central role in the lexer. I'd be surprised if it's _this_ wrong, given our extensive test suite, but of course it's possible...

---

## Comment 3

> Username: jefftrull  
> Created_at: 2024-01-26 17:58:11 UTC  
> Url: https://github.com/boostorg/wave/pull/203#issuecomment-1912461728  

In particular, the [re2c manual](https://re2c.org/manual/manual_c.html#api-primitives) calls `YYMARKER` _A pointer-like l-value (usually a pointer of type YYCTYPE*) that stores the position of the latest matched rule. It is used to restore the YYCURSOR position if the longer match fails and the lexer needs to rollback_. By contrast `YYFILL` is used to provide additional input when the current buffer has been emptied. At the time `YYFILL` is run, the input it is going to return has not been seen by the lexer, and thus IMO it would be inappropriate to change _the position of the latest matched rule_ as a side effect.  
  
I think there may be a more likely explanation: some kind of bug in [the fill function](https://github.com/boostorg/wave/blob/543a0e9e1d0e6190a571c940945a68fbded5da56/include/boost/wave/cpplexer/re2clex/cpp_re.hpp#L173)

---

## Comment 4

> Username: chisolm  
> Created_at: 2024-01-26 19:19:50 UTC  
> Url: https://github.com/boostorg/wave/pull/203#issuecomment-1912570559  

I would not be surprised if there is a better solution or that this solution doesn't hold for some use cases.  
  
I think I can show that YYMARKER is at least part of the problem and/or solution.  
  
The state machine for picking out the quoted strings is the code I've been working through, so I think I can explain it there.  
  
In that state machine, we go to yy14 on the opening quote:  
https://github.com/boostorg/wave/blob/543a0e9e1d0e6190a571c940945a68fbded5da56/include/boost/wave/cpplexer/re2clex/cpp_re.inc#L136  
  
yy14:  
    yyaccept = 0;  
    yych = *(YYMARKER = ++YYCURSOR);  
    if (yych <= '\n') {  
        if (yych == '\t') goto yy99;  
    } else {  
        if (yych <= '\f') goto yy99;  
        if (yych >= ' ') goto yy99;  
    }  
  
YYMARKER is set to the next character in the quote, providing the rewind capability, that I think you're mentioning.  
  
The in yy99 and yy98 cyle, which we go to next:  
https://github.com/boostorg/wave/blob/543a0e9e1d0e6190a571c940945a68fbded5da56/include/boost/wave/cpplexer/re2clex/cpp_re.inc#L775C1-L787C1  
  
We continue consuming characters in the input until YYLIMIT == YYCURSOR and then we need to YYFILL() to   
increase the buffer size.  Then in the fill, we update the buffers:  
  
https://github.com/boostorg/wave/blob/543a0e9e1d0e6190a571c940945a68fbded5da56/include/boost/wave/cpplexer/re2clex/cpp_re.hpp#L39  
  
to:  
  
https://github.com/boostorg/wave/blob/543a0e9e1d0e6190a571c940945a68fbded5da56/include/boost/wave/cpplexer/re2clex/cpp_re.hpp#L197  
  
at this point in the fill we are copying the bit of old data left in the old buffer via the memove and the for loop after this chunk fills the buffer up with up to BOOST_WAVE_BSIZE amount of data.  
  
Now we free the old buffer where YYMARKER is still pointing:  
https://github.com/boostorg/wave/blob/543a0e9e1d0e6190a571c940945a68fbded5da56/include/boost/wave/cpplexer/re2clex/cpp_re.hpp#L217  
  
The returning back to yy98 we eventually hit a character (newline in my case) that is an error and YYCURSOR is updated from YYMARKER which points into free'd memory.  
  
With that I'd at least say YYMARKER is not being handled correctly.  
  
There is also a secondary problem in:  
https://github.com/boostorg/wave/blob/543a0e9e1d0e6190a571c940945a68fbded5da56/include/boost/wave/cpplexer/re2clex/cpp_re.hpp#L182C1-L193C10  
that updating s->ptr from YYMARKER on the way into fill() will fix as well.  
  
So, I'm basing my concept of how YYMARKER and s->pointer are suppossed to interact based on:  
https://github.com/boostorg/wave/blob/543a0e9e1d0e6190a571c940945a68fbded5da56/include/boost/wave/cpplexer/re2clex/cpp_re.hpp#L182C1-L193C10  
and  
https://github.com/boostorg/wave/blob/543a0e9e1d0e6190a571c940945a68fbded5da56/include/boost/wave/cpplexer/re2clex/cpp_re.hpp#L375  
  
Seems like s->ptr and marker are intended to track each other.  
  
BTW I'm trying to get my dev environment setup for boost and having some trouble, still working on helping with the test case reproduction.

---

## Comment 5

> Username: jefftrull  
> Created_at: 2024-01-26 19:52:54 UTC  
> Url: https://github.com/boostorg/wave/pull/203#issuecomment-1912609654  

In case you find the re2c input more readable - as I do - the command line we use is as follows:  
  
re2c -b -o cpp_re.inc cpp.re  
  
You want the 0.13.5 version, which appears to now be unavailable on their website :( but [0.13.6](https://github.com/skvadrik/re2c/releases/tag/0.13.6) is there and will probably produce similar results

---

## Comment 6

> Username: chisolm  
> Created_at: 2024-01-27 01:13:10 UTC  
> Url: https://github.com/boostorg/wave/pull/203#issuecomment-1912891839  

I made a new branch on my fork:  
https://github.com/chisolm/wave/tree/YYMARKER_reproduction  
  
I was unable to reproduce the same behavior.  In fact it is seemingly exiting with no error code but it is truncating output.  I did this testing on linux.  
  
I was unable to get a good break point under gdb, it did not seem to be indexing the .hpp files under re2c as source files.  So was was reduced to debugging by printf.  
  
The test file I committed was basically the same, some of the x characters changed to something else to make it easier to see the diffs.  There are two calls to fill, one for the initial buffer and the second is the one of interest and it shows marker with the old pointer.    
  
I put in a few prints to show marker is refering to the free'd page.  I think this behavior at least should replicate for you fairly easily.  
  
(base) cchisolm@ubuntu:~/boostdev/boost/libs/wave$ ../../bin.v2/libs/wave/tool/build/gcc-7/debug/threading-multi/wavetool-on/wave test/testwave/testfiles/t_9_029.cpp -E  
count 0  
  
Old block pointers bot          0 0 0 0 0  
Old block adjusted pointers bot 0 0 0 0 0  
Freeing old block               0 0x30000 0x7fd30797f010 0x7fd30797f010 0x7fd30797f010  
New block adjusted pointers bot 0x7fd30797f010 0x7fd30797f010 0x7fd30797f010 0x7fd30797f010 0x7fd30797f010  
marker points to:               0  
count 196570  
"1234567891123456789212345678931234567 1234567891123456789212345678931234567 234567891123456789212345678931234567 34567891123456789212345678931234567 4567891123456789212345678931234567 567891123456789212345678931234567 67891123456789212345678931234567 7891123456789212345678931234567 891123456789212345678931234567 91123456789212345678931234567 1123456789212345678931234567 123456789212345678931234567 23456789212345678931234567 3456789212345678931234567 456789212345678931234567 56789212345678931234567 6789212345678931234567 789212345678931234567 89212345678931234567 9212345678931234567 212345678931234567 12345678931234567 2345678931234567 345678931234567 45678931234567 5678931234567 678931234567 78931234567 8931234567 931234567 31234567 1234567 234567 34567 4567 567 67 7   
Old block pointers bot          0x7fd30797f010 0x7fd3079aefea 0x7fd3079aefea 0x7fd3079aefea 0x7fd3079af010  
Old block adjusted pointers bot 0x7fd30797f010 0x7fd30797f010 0x7fd30797f010 0x7fd30797f010 0x7fd30797f036  
Freeing old block               0x7fd30797f010 0x7fd3079af010 0x7fd307833010 0x7fd307833010 0x7fd307833036  
New block adjusted pointers bot 0x7fd307833010 0x7fd307833010 0x7fd307833010 0x7fd307833010 0x7fd307833036  
marker points to:               0x7fd3079aefeb  
/home/cchisolm/boostdev/boost/libs/wave/test/testwave/testfiles/t_9_029.cpp:5715:42: warning: last line of file ends without a newline  
(base) cchisolm@ubuntu:~/boostdev/boost/libs/wave$ tail test/testwave/testfiles/t_9_029.cpp  
xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx  
xxxxxxxxxxxxxxxxxxxxxx  
xxxxxxxxxx  
xxxxxxxxxx  
xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx  
l1  
l2xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx"1234567891123456789212345678931234567894  
        "  
xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx  
do we see this data after the fill?  
(base) cchisolm@ubuntu:~/boostdev/boost/libs/wave$ tail test/testwave/testfiles/t_9_029.i  
xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx  
xxxxxxxxxxx  
xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx  
xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx  
xxxxxxxxxxxxxxxxxxxxxx  
xxxxxxxxxx  
xxxxxxxxxx  
xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx  
l1  
l2xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx(base) cchisolm@ubuntu:~/boostdev/boost/libs/wave$

---

## Comment 7

> Username: hkaiser  
> Created_at: 2024-01-27 14:38:59 UTC  
> Url: https://github.com/boostorg/wave/pull/203#issuecomment-1913175384  

I think this should be much easier to debug when setting `BOOST_WAVE_BSIZE` to something small.

---

## Comment 8

> Username: jefftrull  
> Created_at: 2024-01-27 19:25:34 UTC  
> Updated_at: 2024-01-27 19:27:06 UTC  
> Url: https://github.com/boostorg/wave/pull/203#issuecomment-1913303157  

It's funny you should mention that :) I think I've found at least two bugs since yesterday that way. One of them does indeed point to an issue with updating `marker` (and I think, also `limit`) when a fill occurs. @chisolm I am coming around to your point of view!

---

## Comment 9

> Username: jefftrull  
> Created_at: 2024-01-29 05:39:34 UTC  
> Url: https://github.com/boostorg/wave/pull/203#issuecomment-1913997530  

@hkaiser I created a test case for this issue that seems to be independent of `BOOST_WAVE_BSIZE` (though it does require knowledge of its value, for which purpose I removed a `#undef`). If @chisolm accepts my PRs it will become part of this.

---

## Comment 10

> Username: hkaiser  
> Created_at: 2024-01-29 12:01:17 UTC  
> Url: https://github.com/boostorg/wave/pull/203#issuecomment-1914550656  

> @hkaiser I created a test case for this issue that seems to be independent of `BOOST_WAVE_BSIZE` (though it does require knowledge of its value, for which purpose I removed a `#undef`). If @chisolm accepts my PRs it will become part of this.  
  
Your fix (https://github.com/jefftrull/wave/tree/bugfix/marker-testcase) looks good to me. Thanks for your work on this!

---

## Comment 11

> Username: chisolm  
> Created_at: 2024-01-29 17:49:00 UTC  
> Url: https://github.com/boostorg/wave/pull/203#issuecomment-1915259237  

@jefftrull Your fix looks good, I really like the logic, it's a clean and simple test.  
  
I'm getting a compiler error on my linux setup when I try to compile and test your test:  
  
test/build/../testwave/fill_boundary.cpp:38:71: error: use of deleted function ‘boost::wave::context<__gnu_cxx::__normal_iterator<char*, std::__cxx11::basic_string<char> >, boost::wave::cpplexer::lex_iterator<boost::wave::cpplexer::lex_token<> >, boost::wave::iteration_context_policies::load_file_to_string, boost::wave::context_policies::default_preprocessing_hooks>::context(boost::wave::context<__gnu_cxx::__normal_iterator<char*, std::__cxx11::basic_string<char> >, boost::wave::cpplexer::lex_iterator<boost::wave::cpplexer::lex_token<> >, boost::wave::iteration_context_policies::load_file_to_string, boost::wave::context_policies::default_preprocessing_hooks>&&)’  
     auto ctx =  ctx_t( inp_txt.begin(), inp_txt.end(), "longfile.cpp" );  
  
Plus I get a warning on trigraphs and multiple for a redef of BOOST_FUNCTION_NUM_ARGS.  
  
The most likely culprit is that my build setup is not correct.  If it works on yours and will be tested by normal regression tests, I'd be inclined to just accept it.  You're the experts here, I'll take your lead on this.

---

## Comment 12

> Username: chisolm  
> Created_at: 2024-01-29 18:01:59 UTC  
> Url: https://github.com/boostorg/wave/pull/203#issuecomment-1915280215  

@jefftrull Ah, I see:  
  
auto ctx =  ctx_t( inp_txt.begin(), inp_txt.end(), "longfile.cpp" );  
to  
ctx_t ctx( inp_txt.begin(), inp_txt.end(), "longfile.cpp" );  
  
resolves the issue.  Although I don't understand how that can be setup dependent.

---

## Comment 13

> Username: jefftrull  
> Created_at: 2024-01-29 18:05:39 UTC  
> Url: https://github.com/boostorg/wave/pull/203#issuecomment-1915286543  

That's odd, it's treating it as though we were creating a context object and then moving it into place, as opposed to initializing it. What's your compiler version? I am on linux also (I'm 11.4). CI should tell us if there is an issue.  
  
I don't know what the `BOOST_FUNCTION_NUM_ARGS` issue is but we should certainly avoid it. I wonder if there is a missing include guard somewhere.

---

## Comment 14

> Username: chisolm  
> Created_at: 2024-01-29 18:24:34 UTC  
> Url: https://github.com/boostorg/wave/pull/203#issuecomment-1915316528  

I'm on a ubuntu 18.04 and a gcc 5.4.  That does probably explain why it doesn't find the optimization of not moving the initialized object.  It makes sense.  I had forgot that on the older ubuntu the default gcc packages were really old.  
  
Is there a lower bound on gcc support in boost?    
  
I tested the test with the modified initialization, and it does get the lexing exception.  It

---

## Comment 15

> Username: jefftrull  
> Created_at: 2024-01-29 20:39:18 UTC  
> Url: https://github.com/boostorg/wave/pull/203#issuecomment-1915523340  

Well, the CI fails with that issue, on all versions :) I can't explain why it works on my system!  
Would you mind applying the fix you described?

---

## Comment 16

> Username: chisolm  
> Created_at: 2024-01-29 21:04:30 UTC  
> Url: https://github.com/boostorg/wave/pull/203#issuecomment-1915563581  

Sure, not a problem.

---

## Comment 17

> Username: jefftrull  
> Created_at: 2024-01-29 22:15:34 UTC  
> Url: https://github.com/boostorg/wave/pull/203#issuecomment-1915670602  

@chisolm it looks like there were two separate changes with C++17. One of them made the `auto X = Y(...)` thing work correctly. The other causes trigraphs inside string literals to not be translated, by default. So for my test to work in all versions of the Standard I have to insert the trigraph one character at a time in the code! See my latest PR.

---

## Comment 18

> Username: chisolm  
> Created_at: 2024-01-29 23:02:12 UTC  
> Url: https://github.com/boostorg/wave/pull/203#issuecomment-1915726678  

I accepted it, you're much more likely than me to know what changes there are in the language spec.  
  
Is the order of approval/acceptance of changes a little off in this setup?  Is this just because I initiated a PR off of a fork?  Let me know if you'd like to switch this around to a PR you own.  
  
It seems that at least a couple builds failed because of:  
[errno 2] failed to scan file '': No such file or directory  
  
It doesn't seem to be part of the compile of this file nor the test of this file.  On my local build it seems like it occurs before both build and test stages.  I tried the following flags to locate it a bit better:  
../../b2 --v2 -d2 --debug-configuration variant=debug tool/build test  
  
However I'm not narrowing it down.

---

## Comment 19

> Username: chisolm  
> Created_at: 2024-01-29 23:37:33 UTC  
> Url: https://github.com/boostorg/wave/pull/203#issuecomment-1915764275  

Looks like we just had a successful run.  I checked the logs on one run the "failed to scan file" message was just a wild goose chase, I had thought it was a second problem.

---

## Comment 20

> Username: jefftrull  
> Created_at: 2024-01-30 00:00:50 UTC  
> Url: https://github.com/boostorg/wave/pull/203#issuecomment-1915792030  

Yes, agreed, I've seen it before but it doesn't seem to affect the result.  
  
Re: this process, I wanted to be sure to give you credit by having the PR reflect your contribution. Not only did we find the issue you identified, we found two others at the same time. It's remarkable that no one noticed before... This should help a  lot with stability for power users with large input files.

---

## Comment 21

> Username: chisolm  
> Created_at: 2024-01-30 18:06:17 UTC  
> Url: https://github.com/boostorg/wave/pull/203#issuecomment-1917609812  

Thanks I appreciate it, I don't get to contribute to open source very often.  I got my 2 lines of changes in!  :-)

---
