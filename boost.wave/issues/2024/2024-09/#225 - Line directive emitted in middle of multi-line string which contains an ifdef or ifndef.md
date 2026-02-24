# #225 - Line directive emitted in middle of multi-line string which contains an ifdef or ifndef [Closed]

> Username: njnobles  
> Created at: 2024-09-25 19:17:27 UTC  
> Updated at: 2024-10-02 18:02:10 UTC  
> Closed at: 2024-10-02 18:02:10 UTC  
> Url: https://github.com/boostorg/wave/issues/225  

Hi, I'm running into an issue where a line directive is being emitted in the middle of a multi-line string if there's an ifdef or ifndef directive in the middle of the string. The line directive was not emitted in Boost 1.77.0.  
I suspect this issue was introduced in this PR: https://github.com/boostorg/wave/pull/140  
After reverting that change, I no longer see line directives emitted in the middle of the multi-line string.  
Here's an example to illustrate the issue:  
**test.cpp**  
```  
"1  
#ifdef A  
    2a"  
#else  
    2b  
    3"  
#endif  
```  
The following are using `default_preprocessing_hooks`.  
**Boost 1.77.0 preprocessed output:**  
```  
"1  
    2 b  
    3"  
```  
**Boost 1.86.0 preprocessed output:**  
```  
"1  
    #line 5 "t_2_032.cpp"  
2 b  
    3"  
```  
Boost 1.86.0 output is identical to Boost 1.77.0 output if I revert https://github.com/boostorg/wave/pull/140  
  
**And for comparison, here's output from Visual Studio's cl.exe preprocessor:**  
```  
#line 1 "test.cpp"  
"1  
  
  
  
    2b  
    3"  
#line 8 "test.cpp"  
```

---

## Comment 1

> Username: jefftrull  
> Created at: 2024-09-25 21:02:31 UTC  
> Url: https://github.com/boostorg/wave/issues/225#issuecomment-2375253313  

I'm wondering why the tokenizer doesn't catch that one... Thanks for the report!

---

## Comment 2

> Username: njnobles  
> Created at: 2024-09-25 21:15:07 UTC  
> Url: https://github.com/boostorg/wave/issues/225#issuecomment-2375274085  

To be fair, it is a strange case to run into. But my partners have a knack for abusing preprocessor functionality :)  
  
I've created https://github.com/boostorg/wave/pull/226 which reverts the code change from https://github.com/boostorg/wave/pull/140. I left the new test from 140 in however and, interestingly, it seems to still pass in my local environment.

---

## Comment 3

> Username: jefftrull  
> Created at: 2024-09-25 21:55:29 UTC  
> Url: https://github.com/boostorg/wave/issues/225#issuecomment-2375332974  

Unfortunately changes to test code aren't properly propagated in the build system - try touching [test.cfg](https://github.com/boostorg/wave/blob/develop/test/testwave/testfiles/test.cfg) to rerun.

---

## Comment 4

> Username: njnobles  
> Created at: 2024-09-25 22:18:26 UTC  
> Url: https://github.com/boostorg/wave/issues/225#issuecomment-2375366085  

I did notice that occurring and figured out that trick. This particular test is run as default_hooks.exe. I was able to trigger it to rerun by removing the default_hooks.run and default_hooks.test files. I also ran it directly to make sure it was actually running. Seems like it passed in the PR automation as well.

---

## Comment 5

> Username: jefftrull  
> Created at: 2024-09-26 03:41:00 UTC  
> Updated at: 2024-09-26 03:41:26 UTC  
> Url: https://github.com/boostorg/wave/issues/225#issuecomment-2375801869  

I was able to identify the commit that separately (from #140) fixed the problem it was addressing - it's #170. Now to understand why...

---

## Comment 6

> Username: jefftrull  
> Created at: 2024-09-26 08:49:18 UTC  
> Url: https://github.com/boostorg/wave/issues/225#issuecomment-2376341436  

Interestingly it seems that multi-line string literals are not allowed in C - at least not like that. You can have raw string literals with a different syntax. Not that Wave produces an error or anything... but gcc does, in my experiments. What's your use case?

---

## Comment 7

> Username: jefftrull  
> Created at: 2024-09-26 09:22:40 UTC  
> Url: https://github.com/boostorg/wave/issues/225#issuecomment-2376417171  

Some additional insight into what is happening here: in #140 I made handling of `#ifdef` and `#ifndef` consistent with `if` - and that's actually the issue - `#if` had this behavior (inserting line directives inside strings) before, and still has it, though it's not reflected in your test case at the moment.  
  
The big question for me now is what the correct behavior should be given that multi-line string constants as you have them may be errors. Can you use raw string literals instead? Or string concatenation? Should Wave actually throw an exception?

---

## Comment 8

> Username: hkaiser  
> Created at: 2024-09-26 11:22:43 UTC  
> Url: https://github.com/boostorg/wave/issues/225#issuecomment-2376668207  

> Should Wave actually throw an exception?  
  
I'm not a language lawyer, however I'd say this is ill-formed code. https://eel.is/c++draft/lex.pptoken#2 describes what preprocessing tokens are and string literals are explicitly listed as one. OTOH, a string literal is not allowed to contain newline characters. Those can consist of "any member of the translation character set except the U+0022 quotation mark, U+005c reverse solidus, or new-line character" (see https://eel.is/c++draft/lex.string#nt:basic-s-char).

---

## Comment 9

> Username: njnobles  
> Created at: 2024-09-26 17:47:29 UTC  
> Updated at: 2024-09-26 17:58:26 UTC  
> Url: https://github.com/boostorg/wave/issues/225#issuecomment-2377580492  

One of teammates recently posted an issue and fix so I'll borrow his language for our general setup:  
"I'm attempting to use wave as a pre-processor for a C/PASCAL like DSL. We are trying to migrate away from a 20 year old MSVC pre-processor."  
  
Essentially, we have a DSL that we've created and we have several hundred repos coded against that DSL. Our clients have learned to abuse the preprocessor a bit to work around some of the sort comings of our DSL. We do have some ability to fix our clients' code, but tracking down where and how often they use the preprocessor like this isn't exactly easy.   
  
We do a test build of a handful of our clients' repos before we rollout changes to them and we caught at least one instance of them using the preprocessor like this.  
  
Fixing this issue would certainly make my life easier :sweat_smile: But I can also understand that a 20 year old MSVC preprocessor might not have been the most spec-compliant thing :laughing:

---

## Comment 10

> Username: jefftrull  
> Created at: 2024-09-26 21:31:28 UTC  
> Url: https://github.com/boostorg/wave/issues/225#issuecomment-2377975276  

Honestly sounds like "throw exception" might be a better action for Wave to do here - we do try to be standard compliant, and to match gcc (especially when _they_ are standard compliant). Furthermore it would make it easier for you to track down "where and how often they use the preprocessor like this".  String concatenation should give users what they need here despite the extra work. And finally, what other bugs might Wave have w.r.t. multi-line string literals? We'd be buying into a maintenance headache.

---

## Comment 11

> Username: hkaiser  
> Created at: 2024-09-26 21:44:09 UTC  
> Url: https://github.com/boostorg/wave/issues/225#issuecomment-2377992831  

I'd suggest converting your multi-line strings either into raw string literals or to convert the newlines into `'\n'`. Generally macro expansion is allowed to construct preprocessing tokens, those should be valid, though.

---

## Comment 12

> Username: njnobles  
> Created at: 2024-09-26 22:21:37 UTC  
> Url: https://github.com/boostorg/wave/issues/225#issuecomment-2378036259  

I think that's fair and reasonable. I would agree it's better to stay standard compliant.   
The one instance of the issue in my clients' code should be fairly easy to workaround. My bigger concern are the instances we haven't found yet, but that's not a problem for you guys to worry about 😄

---

## Comment 13

> Username: jefftrull  
> Created at: 2024-09-28 00:25:54 UTC  
> Url: https://github.com/boostorg/wave/issues/225#issuecomment-2380314210  

@hkaiser should we add a `lexing_exception` for this case? AFAICT we happily accept multiline strings, at the moment.

---

## Comment 14

> Username: hkaiser  
> Created at: 2024-09-28 00:44:21 UTC  
> Updated at: 2024-09-28 00:45:35 UTC  
> Url: https://github.com/boostorg/wave/issues/225#issuecomment-2380322219  

> @hkaiser should we add a `lexing_exception` for this case? AFAICT we happily accept multiline strings, at the moment.  
  
I tend towards reporting this as a problem, if possible. This would have to be reported after macro expansion, however. So we would have to go back and reparse if we want to report things.

---

## Comment 15

> Username: jefftrull  
> Created at: 2024-10-02 04:32:02 UTC  
> Url: https://github.com/boostorg/wave/issues/225#issuecomment-2387606449  

I feel like the problem is upstream of macro expansion, namely, that we accept multi-line strings _at all_. On top of that, of course, is the fact that we are currently interpreting directives inside such strings. But if the bad "string literals" themselves were errors we would never get to the expansion and generating the line directives. i.e.:  
  
```  
"1  
    2a"  
```  
and  
```  
"1  
#ifdef A  
    2a"  
```  
are equal errors and should produce the same kind of "unterminated string literal" exception on the first line.  
  
In short, I think we have a pure lexing issue. We lex the first case like this:  
```  
<UnknownToken>   (#34 ) at /tmp/bar.cpp (  1/ 1): >"<  
INTLIT           (#384) at /tmp/bar.cpp (  1/ 2): >1<  
NEWLINE          (#394) at /tmp/bar.cpp (  1/ 3): >\n<  
SPACE            (#392) at /tmp/bar.cpp (  2/ 1): >    <  
INTLIT           (#384) at /tmp/bar.cpp (  2/ 5): >2<  
IDENTIFIER       (#380) at /tmp/bar.cpp (  2/ 6): >a<  
<UnknownToken>   (#34 ) at /tmp/bar.cpp (  2/ 7): >"<  
NEWLINE          (#394) at /tmp/bar.cpp (  2/ 8): >\n<  
EOF              (#401) at /tmp/bar.cpp (  3/ 1): ><  
```  
and the second like this:  
```  
<UnknownToken>   (#34 ) at /tmp/bar.cpp (  1/ 1): >"<  
INTLIT           (#384) at /tmp/bar.cpp (  1/ 2): >1<  
NEWLINE          (#394) at /tmp/bar.cpp (  1/ 3): >\n<  
PP_IFDEF         (#370) at /tmp/bar.cpp (  2/ 1): >#ifdef<  
SPACE            (#392) at /tmp/bar.cpp (  2/ 7): > <  
IDENTIFIER       (#380) at /tmp/bar.cpp (  2/ 8): >A<  
NEWLINE          (#394) at /tmp/bar.cpp (  2/ 9): >\n<  
SPACE            (#392) at /tmp/bar.cpp (  3/ 1): >    <  
INTLIT           (#384) at /tmp/bar.cpp (  3/ 5): >2<  
IDENTIFIER       (#380) at /tmp/bar.cpp (  3/ 6): >a<  
<UnknownToken>   (#34 ) at /tmp/bar.cpp (  3/ 7): >"<  
NEWLINE          (#394) at /tmp/bar.cpp (  3/ 8): >\n<  
EOF              (#401) at /tmp/bar.cpp (  4/ 1): ><  
```  
but a proper single-line string with the same contents:  
```  
"1\n#ifdef A\n    2a"  
```  
is just:  
```  
STRINGLIT        (#390) at /tmp/bar.cpp (  1/ 1): >"1\n#ifdef A\n    2a"<  
NEWLINE          (#394) at /tmp/bar.cpp (  1/22): >\n<  
EOF              (#401) at /tmp/bar.cpp (  2/ 1): ><  
```  
  
So the lexer is getting lost right away.

---

## Comment 16

> Username: hkaiser  
> Created at: 2024-10-02 11:37:03 UTC  
> Url: https://github.com/boostorg/wave/issues/225#issuecomment-2388429108  

@jefftrull I assumed it is possible to constuct a string from macros piece by piece, i.e.  
```  
#define QUOTE "  
QUOTE abc QUOTE  
```  
but other compilers don't accept that. So I think you're right, string constants must be valid before macro expansion.

---

## Comment 17

> Username: jefftrull  
> Created at: 2024-10-02 18:02:10 UTC  
> Url: https://github.com/boostorg/wave/issues/225#issuecomment-2389307799  

Closing this and creating a different issue about how we accept unterminated string literals as multiple tokens
