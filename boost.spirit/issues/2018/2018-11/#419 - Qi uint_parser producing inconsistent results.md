# #419 - Qi uint_parser producing inconsistent results [Closed]

> Username: mwpowellhtx  
> Created at: 2018-11-20 17:52:19 UTC  
> Updated at: 2018-11-20 20:49:22 UTC  
> Closed at: 2018-11-20 20:37:36 UTC  
> Url: https://github.com/boostorg/spirit/issues/419  

Using *Boost.Spirit Qi* Based on the *Google protobuf specification*:  
  
```  
strLit = ( "'" { charValue } "'" ) | ( '"' { charValue } '"' )  
charValue = hexEscape | octEscape | charEscape | /[^\0\n\\]/  
hexEscape = '\' ( "x" | "X" ) hexDigit hexDigit  
octEscape = '\' octalDigit octalDigit octalDigit  
charEscape = '\' ( "a" | "b" | "f" | "n" | "r" | "t" | "v" | '\' | "'" | '"' )  
quote = "'" | '"'  
```  
  
I have the base rules, given:  
  
```C++  
using str_t = std::string;  
// ...  
using char_rule_type = qi::rule<It, char()>;  
using string_rule_type = qi::rule<It, ast::str_t()>;  
```  
  
Declared as:  
  
```C++  
char_rule_type hex_esc, oct_esc, char_esc, char_val;  
string_rule_type str_lit;  
```  
  
And the definition:  
  
```C++  
hex_esc %= no_case["\\x"] >> uint_parser<unsigned char, 16, 2, 2>{};  
oct_esc %= '\\' >> uint_parser<unsigned char, 8, 3, 3>{};  
char_val %= hex_esc | oct_esc | char_esc | ~char_("\0\n\\");  
str_lit %= ("'" >> *(char_val - "'") >> "'")  
    | ('"' >> *(char_val - '"') >> '"');  
// ...  
struct escapes_t : qi::symbols<char, char> {  
    escapes_t() {  
        this->add("\\a", '\a')  
            ("\\b", '\b')  
            ("\\f", '\f')  
            ("\\n", '\n')  
            ("\\r", '\r')  
            ("\\t", '\t')  
            ("\\v", '\v')  
            ("\\\\", '\\')  
            ("\\'", '\'')  
            ("\\\"", '"')  
            ;  
    }  
} char_esc;  
```  
  
I am finding that Octal encoded strings parse and land in the raw characters. However, the Hexadecimal encoded strings are somehow landing as the string, whereas I would expect the raw character itself.  
  
So, in other words, given input ``"\\100\\101\\102\\103"``, I get in response, ``"@ABC"``. However, given ``"\\x40\\x41\\x42\\x43"`` yields ``"\\x40\\x41\\x42\\x43"``. Not at all what I would expect; in fact, given the rules, I'm not sure how you get that and not the decoded raw result?

---

## Comment 1

> Username: mwpowellhtx  
> Created at: 2018-11-20 18:18:54 UTC  
> Url: https://github.com/boostorg/spirit/issues/419#issuecomment-440378656  

And with similar results from ``char_esc`` not producing the unescaped raw character. Really not sure why the *Octal* phrase is producing that, *Hexadecimal* and ``char_esc`` are not.

---

## Comment 2

> Username: Kojoley  
> Created at: 2018-11-20 19:48:00 UTC  
> Url: https://github.com/boostorg/spirit/issues/419#issuecomment-440406802  

Thanks for the bug report, but currently it contains bunch of unrelated stuff and it is not clear for me what is the problem and why you think there is a bug in Qi rather than in your code. Please, make the bug report simpler and provide an [MCVE](https://stackoverflow.com/help/mcve). Ideally it should be a test case like https://github.com/boostorg/spirit/blob/cfcfe6d0cd0b521d887cfb0e2dfb1c0c56eacaab/test/x3/uint1.cpp#L78-L83

---

## Comment 3

> Username: mwpowellhtx  
> Created at: 2018-11-20 20:00:01 UTC  
> Url: https://github.com/boostorg/spirit/issues/419#issuecomment-440410332  

@Kojoley Hmm, well, to be clear, this is a Qi (Spirit v2, as far as I can tell) behavior. Quite possibly there is some Qi element I am missing to help persuade the parser to do the right thing, but I do not know what that is more than I have already done.

---

## Comment 4

> Username: Kojoley  
> Created at: 2018-11-20 20:09:46 UTC  
> Url: https://github.com/boostorg/spirit/issues/419#issuecomment-440413143  

> Hmm, well, to be clear, this is a Qi  
  
I wrongly gave you the link to x3 tests, you can simply replace x3 with qi in it https://github.com/boostorg/spirit/blob/cfcfe6d0cd0b521d887cfb0e2dfb1c0c56eacaab/test/qi/uint1.cpp#L78-L83  
  
> Quite possibly there is some Qi element I am missing to help persuade the parser to do the right thing, but I do not know what that is more than I have already done.  
  
If it is not a bug report, I am sorry I cannot help you, it is not a support board. You can try your luck on [stack overflow](https://stackoverflow.com/questions/tagged/boost-spirit-qi) or [mail list](https://sourceforge.net/projects/spirit/lists/spirit-general).

---

## Comment 5

> Username: mwpowellhtx  
> Created at: 2018-11-20 20:28:55 UTC  
> Url: https://github.com/boostorg/spirit/issues/419#issuecomment-440418533  

I've got a [start on a test case](http://coliru.stacked-crooked.com/a/1dc3309f51f6cc6c), but it is not even parsing to start with, so not sure what's going on. This is about as bare bones a [grammar subset](http://developers.google.com/protocol-buffers/docs/reference/proto2-spec#string_literals) as I can determine.

---

## Comment 6

> Username: mwpowellhtx  
> Created at: 2018-11-20 20:31:49 UTC  
> Url: https://github.com/boostorg/spirit/issues/419#issuecomment-440419390  

@Kojoley At the moment I think it is a bug report, or unless it can be shown why it is otherwise. I am expecting a raw character be distilled out given the radix, either Octal (base 8), which appears to be "working", whereas at least Hexadecimal (base 16) is not working, or the result just runs very counter to what I would otherwise expect, almost as though the phrase was being rejected altogether and falling through to one of the other phrases.

---

## Comment 7

> Username: mwpowellhtx  
> Created at: 2018-11-20 20:37:36 UTC  
> Url: https://github.com/boostorg/spirit/issues/419#issuecomment-440421032  

I'm not sure what's going on with it. [Seems to work here](http://coliru.stacked-crooked.com/a/ff22a191636b6068). In further context, not so much.  
  
Thanks for the effort.

---

## Comment 8

> Username: mwpowellhtx  
> Created at: 2018-11-20 20:48:24 UTC  
> Url: https://github.com/boostorg/spirit/issues/419#issuecomment-440424163  

I think I got it sorted on my end. One too many escapes in the test cases...

---

## Comment 9

> Username: Kojoley  
> Created at: 2018-11-20 20:49:11 UTC  
> Url: https://github.com/boostorg/spirit/issues/419#issuecomment-440424358  

Glad you solved your problem.
