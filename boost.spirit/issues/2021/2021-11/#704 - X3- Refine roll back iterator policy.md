# #704 - X3: Refine roll back iterator policy [Open]

> Username: Kojoley  
> Created at: 2021-11-03 19:20:35 UTC  
> Updated at: 2025-05-09 23:49:29 UTC  
> Url: https://github.com/boostorg/spirit/issues/704  

A long time ago I had an idea about rich and low overhead error reporting and it did not work out because parsers roll back iterator in some unexpected places (like sequence parsers) which prevents easy way to locate the error location, while in other cases iterators are not rolled back when it is logically expected, most prominent is alternative parser (see https://github.com/boostorg/spirit/issues/703). The story is most likely more complicated because of skippers (see https://svn.boost.org/trac10/ticket/11824).  
  
If I solved the puzzle correctly in my head we need to roll back the iterator only in alternative parser + (some?) repeat/container parsers and also extract skipper execution from end-parsers to sequence parser + repeat/container parsers.

---

## Comment 1

> Username: djowel  
> Created at: 2021-11-04 00:28:51 UTC  
> Updated at: 2021-11-04 01:07:40 UTC  
> Url: https://github.com/boostorg/spirit/issues/704#issuecomment-960325156  

The fundamental rules for parsing are as follows (https://www.boost.org/doc/libs/1_67_0/libs/spirit/doc/html/spirit/advanced/indepth/parsers_indepth.html):  
  
Here are the basic rules for parsing:  
  
- The parser returns true if successful, false otherwise.  
- If successful, first is incremented N number of times, where N is the number of characters parsed. N can be zero --an empty (epsilon) match.  
- If successful, the parsed attribute is assigned to `attr`  
- If unsuccessful, first is reset to its position before entering the parser function. `attr` is untouched.  
  
If this is not applied consistently, then it is a bug (or the doc should be updated). I recall a time when a suggestion was to make the rule that If unsuccessful, then first position is undefined. I don't recall the details now, but I recall having numerous difficulties with that behavior.   
  
That said, sure, you can investigate further. It can probably also be done such that each parser can have its own "unsuccessful first policy".

---

## Comment 2

> Username: Kojoley  
> Created at: 2021-11-04 00:51:07 UTC  
> Updated at: 2021-11-04 01:08:19 UTC  
> Url: https://github.com/boostorg/spirit/issues/704#issuecomment-960346178  

> - If successful, the parsed attribute is assigned to `attr`  
  
I am pretty sure optional parser violates that.  
  
> - If unsuccessful, first is reset to its position before entering the parser function. ...  
  
I think it is not done in every `parse` function because it will mean a lot of reskipping.  
  
> - If unsuccessful, ... `attr` is untouched.  
  
This is clearly violated by container parsers (hello backtracking #703!).

---

## Comment 3

> Username: djowel  
> Created at: 2021-11-04 01:22:55 UTC  
> Url: https://github.com/boostorg/spirit/issues/704#issuecomment-960358535  

> This is clearly violated by container parsers (hello backtracking #703!).  
  
Agreed. It's a good chance to refine the rules then. Those are excellent observations.

---

## Comment 4

> Username: djowel  
> Created at: 2021-11-04 01:46:58 UTC  
> Url: https://github.com/boostorg/spirit/issues/704#issuecomment-960366910  

> I think it is not done in every `parse` function because it will mean a lot of reskipping.  
>   
  
I think this should be refined with skipping in mind.

---

## Comment 5

> Username: Kojoley  
> Created at: 2021-11-04 01:55:51 UTC  
> Url: https://github.com/boostorg/spirit/issues/704#issuecomment-960370510  

I also should note that the linked documentation is about Qi and I was not proposing to touch it nor I have a such desire.

---

## Comment 6

> Username: djowel  
> Created at: 2021-11-04 02:01:26 UTC  
> Url: https://github.com/boostorg/spirit/issues/704#issuecomment-960372551  

> I also should note that the linked documentation is about Qi and I was not proposing to touch it nor I have a such desire.  
  
Sure, but general Qi rules should also apply to X3 as intended.

---

## Comment 7

> Username: Kojoley  
> Created at: 2021-11-04 02:50:24 UTC  
> Url: https://github.com/boostorg/spirit/issues/704#issuecomment-960407351  

```cpp  
#include <boost/spirit/home/x3.hpp>  
#include <iostream>  
  
int main()  
{  
    using namespace boost::spirit::x3;  
      
    char const* it = "Hello World", * const e = it + std::strlen(it);  
  
    if (phrase_parse(it, e, "Hello" >> ("Joel" | no_skip["World"]), ascii::blank))  
        std::clog << "success\n";  
    else  
        std::clog << "fail\n";  
}  
```  
A curious question: what this example is expected to print?

---

## Comment 8

> Username: djowel  
> Created at: 2021-11-04 04:35:13 UTC  
> Updated at: 2021-11-04 04:42:20 UTC  
> Url: https://github.com/boostorg/spirit/issues/704#issuecomment-960452997  

Primitives do the pre-skip. "Joel" pre-skips. When it fails, it resets the iterator. no_skip["World"] should also fail bc. it points to white space and "World" will not skip. So it should fail too and the whole alternative should fail.  
  
I did not test. Just based on the parsing rules. It can be argued that the skipper may or may not apply to the reset iterator rule because conceptually, `phrase_parse` works on lexemes, not characters.

---

## Comment 9

> Username: Kojoley  
> Created at: 2021-11-04 18:00:14 UTC  
> Updated at: 2021-11-04 18:00:30 UTC  
> Url: https://github.com/boostorg/spirit/issues/704#issuecomment-961287628  

> Primitives do the pre-skip. "Joel" pre-skips. When it fails, it resets the iterator. no_skip["World"] should also fail bc. it points to white space and "World" will not skip. So it should fail too and the whole alternative should fail.  
  
Yeah, that's how I read the rules in https://www.boost.org/doc/libs/1_67_0/libs/spirit/doc/html/spirit/advanced/indepth/parsers_indepth.html too, but in reality it succeeds on both X3 https://godbolt.org/z/8ne1bWr3q and Qi https://godbolt.org/z/356jzjEz5  
  
The thing is that `no_skip` by definition is a time machine, it affects something that is done before it.

---

## Comment 10

> Username: Kojoley  
> Created at: 2021-11-04 18:12:28 UTC  
> Url: https://github.com/boostorg/spirit/issues/704#issuecomment-961296776  

I brought that up because if I simply add an iterator roll back to alternative parser (to fix #703) that will change behavior of that example, but I genuinely think that pre-skipping should be done in sequence parser and not in primitive parsers because it is logically happens there and that will also fix #551, but such a change will make impossible to implement `no_skip`.

---

## Comment 11

> Username: djowel  
> Created at: 2021-11-05 01:38:48 UTC  
> Url: https://github.com/boostorg/spirit/issues/704#issuecomment-961561022  

> I brought that up because if I simply add an iterator roll back to alternative parser (to fix #703) that will change behavior of that example, but I genuinely think that pre-skipping should be done in sequence parser and not in primitive parsers because it is logically happens there and that will also fix #551, but such a change will make impossible to implement `no_skip`.  
  
Good point on the sequence parser doing the pre-skipping. I suppose that observation also applies to all sequence-like parsers such as +P and *P and all repeats. Something to ponder on... I kinda tend to agree. Do you see any potential backward compatibility problems?

---

## Comment 12

> Username: djowel  
> Created at: 2021-11-05 01:42:24 UTC  
> Url: https://github.com/boostorg/spirit/issues/704#issuecomment-961562264  

> Good point on the sequence parser doing the pre-skipping. I suppose that observation also applies to all sequence-like parsers such as +P and *P and all repeats. Something to ponder on... I kinda tend to agree. Do you see any potential backward compatibility problems?  
  
Ah, one possible difficulty is that the sequence parsers will need to know about its children such as no_skips, but yes it can be done.

---

## Comment 13

> Username: djowel  
> Created at: 2021-11-05 01:47:58 UTC  
> Url: https://github.com/boostorg/spirit/issues/704#issuecomment-961564283  

> Yeah, that's how I read the rules in https://www.boost.org/doc/libs/1_67_0/libs/spirit/doc/html/spirit/advanced/indepth/parsers_indepth.html too, but in reality it succeeds on both X3 https://godbolt.org/z/8ne1bWr3q and Qi https://godbolt.org/z/356jzjEz5  
>   
> The thing is that `no_skip` by definition is a time machine, it affects something that is done before it.  
  
I suppose the rule can be refined such that on unsuccessful parse, the iterator will be positioned at the original position, and on phrase-parse, applying the skipper thereafter.
