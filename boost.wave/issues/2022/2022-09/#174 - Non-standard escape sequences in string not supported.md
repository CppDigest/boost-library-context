# #174 - Non-standard escape sequences in string not supported [Closed]

> Username: p8nut  
> Created at: 2022-09-12 15:48:09 UTC  
> Updated at: 2022-11-09 02:08:51 UTC  
> Closed at: 2022-11-09 02:08:51 UTC  
> Url: https://github.com/boostorg/wave/issues/174  

During the parsing, it seem that Wave is not able to parse properly a string containing the escape character `\e`.  
Using a slightly modified [quick_start exemple](https://www.boost.org/doc/libs/1_80_0/libs/wave/samples/quick_start/quick_start.cpp) you can reproduce the following output.  
change the line 93 with `            std::cout << "["<< current_position <<"]" << (*first).get_value() << std::endl;` to display each line and token on a different line.   
  
the following C file produce   
`C file:`  
```c  
char *c = "test\n";  
```  
Output  
```  
[/tmp/./test.cpp:1:1]char  
[/tmp/./test.cpp:1:5]   
[/tmp/./test.cpp:1:6]*  
[/tmp/./test.cpp:1:7]c  
[/tmp/./test.cpp:1:8]   
[/tmp/./test.cpp:1:9]=  
[/tmp/./test.cpp:1:10]   
[/tmp/./test.cpp:1:11]"test\n"  
[/tmp/./test.cpp:1:19];  
[/tmp/./test.cpp:1:20]  
  
[/tmp/./test.cpp:2:1]  
```  
  
the following C file produce   
`C file:`  
```c  
char *c = "test\e";  
```  
Output  
```  
[/tmp/test.cpp:1:1]char  
[/tmp/test.cpp:1:5]   
[/tmp/test.cpp:1:6]*  
[/tmp/test.cpp:1:7]c  
[/tmp/test.cpp:1:8]   
[/tmp/test.cpp:1:9]=  
[/tmp/test.cpp:1:10]   
[/tmp/test.cpp:1:11]"  
[/tmp/test.cpp:1:12]test  
[/tmp/test.cpp:1:16]\  
[/tmp/test.cpp:1:17]e  
[/tmp/test.cpp:1:18]"  
[/tmp/test.cpp:1:19];  
[/tmp/test.cpp:1:20]  
  
[/tmp/test.cpp:2:1]  
```  
  
as you can see the `stringlit` is not detected anymore as the right type.  
  
is there any solution to handle that right ?

---

## Comment 1

> Username: jefftrull  
> Created at: 2022-09-13 21:16:32 UTC  
> Url: https://github.com/boostorg/wave/issues/174#issuecomment-1245962526  

It looks like `\e` is among the _conditionally supported_ escape sequences. For more see: https://cplusplus.github.io/CWG/issues/505.html  
  
Is there a strong need for this feature?

---

## Comment 2

> Username: p8nut  
> Created at: 2022-09-14 03:47:53 UTC  
> Url: https://github.com/boostorg/wave/issues/174#issuecomment-1246196055  

Yes I am currently working on a project that strongly need that feature.  
  
My only other solution would be to escape the body before parsing by wave but seem to be a hacky solution and it won't fully fit my need.

---

## Comment 3

> Username: jefftrull  
> Created at: 2022-09-14 06:04:04 UTC  
> Url: https://github.com/boostorg/wave/issues/174#issuecomment-1246283493  

Unfortunately those types of escape sequences are compiler-specific extensions. I suspect it would be a low priority (and require special code to enable/disable support) to go into mainline Wave.  
  
If you are interested in implementing this yourself, I suggest changing the lexer definition [here](https://github.com/boostorg/wave/blob/44362764b92412a1d6b8b79ef293ebdcaa56579d/include/boost/wave/cpplexer/re2clex/cpp.re#L34). This file is input to a tool called [re2c](https://github.com/skvadrik/re2c/releases/tag/1.0.2);  you can use it to generate a new `cpp_re.inc` file in the same directory like this:  
  
```  
cd include/boost/wave/cpplexer/re2clex  
re2c -b -o cpp_re.inc cpp.re  
```  
  
Then you can rebuild Wave and see if it works for you. Good luck!

---

## Comment 4

> Username: p8nut  
> Created at: 2022-09-14 08:20:59 UTC  
> Url: https://github.com/boostorg/wave/issues/174#issuecomment-1246412846  

Thank you i'll take a look at it.  
and keep you updated.

---

## Comment 5

> Username: jefftrull  
> Created at: 2022-09-14 16:06:36 UTC  
> Url: https://github.com/boostorg/wave/issues/174#issuecomment-1246989658  

@hkaiser we could maybe put this in the "non-strict" lexer... any thoughts?

---

## Comment 6

> Username: p8nut  
> Created at: 2022-09-20 10:13:26 UTC  
> Updated at: 2022-09-20 10:49:28 UTC  
> Url: https://github.com/boostorg/wave/issues/174#issuecomment-1252137030  

I found some more information about the `\e` support in gcc [here](https://gcc.gnu.org/onlinedocs/gcc/Character-Escapes.html) and in clang [here](https://clang.llvm.org/doxygen/classclang_1_1StringLiteralParser.html#details)  
  
At least i think it could be interesting that an error could be raise when an invalid character like an unknown escaped character is found.  
  
or maybe the parser can process any escaped printable character like gcc and clang do.  
  
what do you think ?

---

## Comment 7

> Username: jefftrull  
> Created at: 2022-09-20 21:32:10 UTC  
> Url: https://github.com/boostorg/wave/issues/174#issuecomment-1252937290  

Yeah, I'm not crazy about the "break up the string" result we currently have. Maybe an error would be better? Hopefully @hkaiser will weigh in.  
  
I think, though, that `\e` is not the same as `e` - it produces an ESC character, if I understand correctly. So that would be special.

---

## Comment 8

> Username: jefftrull  
> Created at: 2022-09-21 01:04:59 UTC  
> Url: https://github.com/boostorg/wave/issues/174#issuecomment-1253072789  

I retract that last comment. We just pass through the character and the backslash both when it's a valid control character.  
  
@p8nut did you try making that change to Wave I suggested?

---

## Comment 9

> Username: hkaiser  
> Created at: 2022-09-21 20:37:08 UTC  
> Updated at: 2022-09-21 20:37:33 UTC  
> Url: https://github.com/boostorg/wave/issues/174#issuecomment-1254202375  

> It looks like `\e` is among the _conditionally supported_ escape sequences. For more see: https://cplusplus.github.io/CWG/issues/505.html  
>   
> Is there a strong need for this feature?  
  
I think just generally accepting the strings that contain those 'conditionally-supported' characters would be ok. At least clang does that without diagnostics. The current logic in Wave was implemented before CWG 505.

---

## Comment 10

> Username: p8nut  
> Created at: 2022-09-22 15:41:01 UTC  
> Url: https://github.com/boostorg/wave/issues/174#issuecomment-1255211699  

Hello @jefftrull,  
  
i try to change the escape `EscapeSequence` in the `cpp.re`, `strict_cpp.re`, `idl.re` where  
```diff  
diff --git a/include/boost/wave/cpplexer/re2clex/cpp.re b/include/boost/wave/cpplexer/re2clex/cpp.re  
index 9839901..3567689 100644  
--- a/include/boost/wave/cpplexer/re2clex/cpp.re  
+++ b/include/boost/wave/cpplexer/re2clex/cpp.re  
@@ -31,7 +31,7 @@ IntegerSuffix       = [uU] [lL]? | [lL] [uU]?;  
 LongIntegerSuffix   = [uU] ("ll" | "LL") | ("ll" | "LL") [uU]?;  
 MSLongIntegerSuffix = "u"? "i64";  
 Backslash           = [\\] | "??/";  
-EscapeSequence      = Backslash ([abfnrtv?'"] | Backslash | "x" HexDigit+ | OctalDigit OctalDigit? OctalDigit?);  
+EscapeSequence      = Backslash ([\x20-\x7E] | Backslash | "x" HexDigit+ | OctalDigit OctalDigit? OctalDigit?);  
 HexQuad             = HexDigit HexDigit HexDigit HexDigit;  
 UniversalChar       = Backslash ("u" HexQuad | "U" HexQuad HexQuad);  
 Newline             = "\r\n" | "\n" | "\r";  
```  
  
when i try with now it produce as expected the following output  
```shell  
[/tmp/./test.cpp:1:1]char  
[/tmp/./test.cpp:1:5]   
[/tmp/./test.cpp:1:6]*  
[/tmp/./test.cpp:1:7]c  
[/tmp/./test.cpp:1:8]   
[/tmp/./test.cpp:1:9]=  
[/tmp/./test.cpp:1:10]   
[/tmp/./test.cpp:1:11]"test\e"  
[/tmp/./test.cpp:1:19];  
[/tmp/./test.cpp:1:20]  
  
[/tmp/./test.cpp:2:1]  
```  
this solution is a little bit more too inclusive and escape any printable character which is maybe not what we want...

---

## Comment 11

> Username: jefftrull  
> Created at: 2022-09-22 16:59:23 UTC  
> Url: https://github.com/boostorg/wave/issues/174#issuecomment-1255302741  

@hkaiser are you comfortable with this? Also, should we distinguish in this case between the "strict" and non-strict lexers?

---

## Comment 12

> Username: hkaiser  
> Created at: 2022-09-22 18:27:32 UTC  
> Url: https://github.com/boostorg/wave/issues/174#issuecomment-1255396289  

The standard says that (https://eel.is/c++draft/lex.ccon#nt:conditional-escape-sequence-char):  
```  
conditional-escape-sequence-char:  
    any member of the basic character set that is not an octal-digit, a simple-escape-sequence-char,   
    or the characters N, o, u, U, or x  
```  
So I think we shouldn't use `[\x20-\x7E]` as a catch-all regex.

---

## Comment 13

> Username: p8nut  
> Created at: 2022-09-22 19:57:10 UTC  
> Updated at: 2022-09-22 19:59:53 UTC  
> Url: https://github.com/boostorg/wave/issues/174#issuecomment-1255485338  

ok it is possible to negate the regexp which look like that now  
`[^\\01234567NouUx\x00-\x1F\x7F]`  
which is based on the eel.is definition.  
  
but with that definition if there is an incomplete definition it break again  
```c  
char *c = "test\N";  
```  
produce  
  
```shell  
[/tmp/test.cpp:1:1]char  
[/tmp/test.cpp:1:5]   
[/tmp/test.cpp:1:6]*  
[/tmp/test.cpp:1:7]c  
[/tmp/test.cpp:1:8]   
[/tmp/test.cpp:1:9]=  
[/tmp/test.cpp:1:10]   
[/tmp/test.cpp:1:11]"  
[/tmp/test.cpp:1:12]test  
[/tmp/test.cpp:1:16]\  
[/tmp/test.cpp:1:17]N  
[/tmp/test.cpp:1:18]"  
[/tmp/test.cpp:1:19];  
[/tmp/test.cpp:1:20]  
  
[/tmp/test.cpp:2:1]  
```  
  
maybe the eager match could be an option or when we match a stringlit we could use the eager match because inside a string lit (i think) there is no need to process octal and or any other sequence

---

## Comment 14

> Username: hkaiser  
> Created at: 2022-09-22 20:32:47 UTC  
> Url: https://github.com/boostorg/wave/issues/174#issuecomment-1255519429  

Frankly, I'm not sure why `\N` is not allowed. I'm not aware of any possibly conflicting escaped character sequence. We should conform to the Standard, however (there is usually reason to the madness ;-) ).

---

## Comment 15

> Username: p8nut  
> Created at: 2022-09-23 09:01:40 UTC  
> Url: https://github.com/boostorg/wave/issues/174#issuecomment-1255962723  

My only point is that when we encounter a stringlit it should not break it apart because there is an escape character inside

---

## Comment 16

> Username: hkaiser  
> Created at: 2022-09-23 12:43:14 UTC  
> Url: https://github.com/boostorg/wave/issues/174#issuecomment-1256163086  

> My only point is that when we encounter a stringlit it should not break it apart because there is an escape character inside  
  
Should we report an error instead?

---

## Comment 17

> Username: p8nut  
> Created at: 2022-09-30 21:46:07 UTC  
> Url: https://github.com/boostorg/wave/issues/174#issuecomment-1264052723  

for my project, the best solution would be to get the `stringlit` otherwise an exception could do.  
(sorry for the long time to answer :-) )

---

## Comment 18

> Username: jefftrull  
> Created at: 2022-10-21 01:44:15 UTC  
> Url: https://github.com/boostorg/wave/issues/174#issuecomment-1286350613  

`\N` is excluded because it begins a "named universal character escape", new in C++23.  
  
Currently our approach to many invalid things is to report them as a sequence of simpler tokens instead, so breaking up a string literal in the face of an erroneous escape sequence seems OK to me.  
  
I propose that we:  
  
1. Implement _in the non-strict lexer_ support for conditional escape sequences as described in the standard - the "basic character set" minus all escape characters mentioned elsewhere (including N - since we currently don't support it there is no change there).  
2. Implement this in the sample lexers also for consistency - that will be the bulk of the work, actually. For example, `slex` uses a Spirit Classic escape sequence parser, which we will need to fork.  
  
@p8nut can you make progress with your fork? I think we will miss the 1.81 cutoff.

---

## Comment 19

> Username: hkaiser  
> Created at: 2022-10-21 13:45:10 UTC  
> Url: https://github.com/boostorg/wave/issues/174#issuecomment-1286983934  

> `\N` is excluded because it begins a "named universal character escape", new in C++23.  
  
Right, I missed that. Thanks!

---

## Comment 20

> Username: jefftrull  
> Created at: 2022-11-08 06:51:08 UTC  
> Updated at: 2022-11-08 06:51:42 UTC  
> Url: https://github.com/boostorg/wave/issues/174#issuecomment-1306715809  

Upon reflection - and seeing what would need to change to support it - I decided to try implementing just `\e` and `\E`, which seem to be supported by both gcc and Clang. I couldn't find any others they both supported. Let me know what you think of the above PR and whether it would be enough. I think we could squeeze it in for 1.81.
