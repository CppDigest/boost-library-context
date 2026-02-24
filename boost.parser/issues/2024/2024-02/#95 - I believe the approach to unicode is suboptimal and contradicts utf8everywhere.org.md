# #95 - I believe the approach to unicode is suboptimal and contradicts utf8everywhere.org [Closed]

> Username: pavel-teramips  
> Created at: 2024-02-19 17:32:54 UTC  
> Updated at: 2024-02-23 00:24:40 UTC  
> Closed at: 2024-02-22 04:43:00 UTC  
> Url: https://github.com/boostorg/parser/issues/95  

What is referred to as "unicode aware" causes more harm than good. Instead, utf-8 string should be based on code units and not unicode code points. There's no sense in matching code points, as they are not equivalent of characters (so aren't code units, but this is the utf-8 ideology). More on this in utf8everywhere.org.  
  
A snippet from doc:  
"Boost.Parser is Unicode-aware through and through. When you parse ranges of char, Boost.Parser does not assume any particular encoding — not Unicode or any other encoding. Parsing of inputs other than plain chars assumes that the input is Unicode. In the Unicode-aware code paths, all parsing is done by matching code points. This means that you can feed UTF-8 strings into Boost.Parser, both as input and within your parser, and the right sort of matching occurs. For instance, if your parser is trying to match repetitions of the char '\xcc' (which is a lead byte from a UTF-8 sequence, and so is malformed UTF-8 if not followed by an appropriate UTF-8 code unit), it will not match the start of "\xcc\x80" (UTF-8 for the code point U+0300). Boost.Parser knows that the matching must be whole-code-point, and so it interprets the char '\xcc' as the code point U+00CC."  
  
In such case, matching subpart code point should be considered fine and causes no real harm to the industry. In case of parsing a machine language, ASCII-compatibility of UTF-8 matching will suffice. If the author disagrees - why match code points and not grapheme clusters? Read the doc.

---

## Comment 1

> Username: tzlaine  
> Created at: 2024-02-19 22:43:44 UTC  
> Updated at: 2024-02-19 22:49:35 UTC  
> Url: https://github.com/boostorg/parser/issues/95#issuecomment-1953241636  

I've read this whole thing before (as a member of the C++ Committee's SG-16); I just skimmed it this time.  Could you point specifically to the part(s) of it that apply to this parser?  I honestly can't find it.  
  
For my part, the rationale is this.  If you want to parse a `char` at a time, use `char` as the parsed range's value type.  If you want to match code points, use one of the Unicode-aware parsable range types (charN_t in C++20 and later, plus `utf8_view` in c++17).  It is 100% up to the user which one to use.  
  
In the Unicode path, the reason matching is done only on code points is that this is what *every* Unicode algorithm does.  There is not a single Unicode algorithm that operates on UTF-8 code units.  For algorithms on text, the code point is the minimum unit of work, not the code unit.  
  
I understand why you want people to represent their text in UTF-8; I agree completely.  I also agree that filenames should be matched `char` by `char` -- but that's not because it's the right thing to do w.r.t. Unicode, but instead because filesystems are crazy w.r.t. encodings.  There's UCS-2/UTF-16 confusion on older Windows filesystems, there's normalization confusion on Apple filesystems, etc.  Similar oddities occur when you deal with env. vars.  Such text should be treated as an opaque bag of bytes, to be sure.  Anything else is going to mess you up eventually.  
  
General-purpose text does not usually have these problems.  Moreover, if it does in your case, use the non-Unicode code path.

---

## Comment 2

> Username: pavel-teramips  
> Created at: 2024-02-22 02:22:06 UTC  
> Url: https://github.com/boostorg/parser/issues/95#issuecomment-1958541401  

Zach,  
  
First, thanks for the answer. You are right of course about unicode  
algorithms. Let me try to address this from a different perspective. The  
history of treating Unicode text is full of confusion and things the  
industry learned over the years. If we are going to make another text  
managing tool, I believe it's important to avoid making another mistake.  
Our claim is much stronger than a recommendation of text encoding.  
  
+ Yakov in CC  
  
So, you are saying: *"There is not a single Unicode algorithm that operates  
on UTF-8 code units. For algorithms on text, the code point is the minimum  
unit of work, not the code unit."*  
This is true, but ideally it's what should have never been like this. Let  
me say this: looking at the industry today, what I see is (near) *zero use* for  
any processing of text that takes anything more than a code unit or  
anything less than a grapheme cluster as a working unit. In particular, we  
found no real-world task that it would make sense to solve by counting,  
iterating or otherwise viewing text as a sequence of unicode code points.  
On the other hand, we see a lot of damage, both to program performance and  
occasionally even broken logic caused by developer confusion that resulted  
from reiterating the idea of a unicode code point as a reasonable  
processing unit for text algorithms.  
  
Python3 strings are a good example of this. A redundant complexity cripples  
performance of almost any software written in this language, because  
someone had an idea that an in-memory array of code points may be good for  
something. On the broken text handling side, the number of issues  
github-wide in some of the best software (e.g. PrusaSlicer) is staggering.  
  
In our view of the future, these older Windows and Apple systems you  
mention will eventually go down in history followed by any  
codepoint-iterating code, and the code points will only remain as a concept  
in the definition of the Unicode standard, but not in actual  
machine-executed software, which will focus on (specifically UTF-8) code  
units for program logic and grapheme clusters for visual representation. I  
hope we can agree on this.  
  
Kind regards and thank you very much for contributing to the ecosystem,  
Pavel  
  
  
On Tue, Feb 20, 2024 at 12:43 AM Zach Laine ***@***.***>  
wrote:  
  
>  
> I've read this whole thing before (as a member of the Committee's SG-16);  
> I just skimmed it this time. Could you point specifically to the part(s) of  
> it that apply to this parser? I honestly can't find it.  
>  
> For my part, the rationale is this. If you want to parse a char at a  
> time, use char as the parsed range's value type. If you want to match  
> code points, use one of the Unicode-aware parsable range types (charN_t in  
> C++20 and later, plus utf8_view in c++17). It is 100% up to the user  
> which one to use.  
>  
> In the Unicode path, the reason matching is done only on code points is  
> that this is what *every* Unicode algorithm does. There is not a single  
> Unicode algorithm that operates on UTF-8 code units. For algorithms on  
> text, the code point is the minimum unit of work, not the code unit.  
>  
> I understand why you want people to represent their text in UTF-8; I agree  
> completely. I also agree that filenames should be matched char by char --  
> but that's not because it's the right thing to do w.r.t. Unicode, but  
> instead because filesystems are crazy w.r.t. encodings. There's  
> UCS-2/UTF-16 confusion on older Windows filesystems, there's normalization  
> confusion on Apple filesystems, etc. Similar oddities occur when you deal  
> with env. vars. Such text should be treated as an opaque bag of bytes, to  
> be sure. Anything else is going to mess you up eventually.  
>  
> General-purpose text does not usually have these problems. Moreover, if it  
> does, use the non-Unicode code path.  
>  
> —  
> Reply to this email directly, view it on GitHub  
> <https://github.com/tzlaine/parser/issues/95#issuecomment-1953241636>, or  
> unsubscribe  
> <https://github.com/notifications/unsubscribe-auth/ALIXCFMJOJDKI2BFXSPTS3LYUPISXAVCNFSM6AAAAABDP22AZ2VHI2DSMVQWIX3LMV43OSLTON2WKQ3PNVWWK3TUHMYTSNJTGI2DCNRTGY>  
> .  
> You are receiving this because you authored the thread.Message ID:  
> ***@***.***>  
>

---

## Comment 3

> Username: tzlaine  
> Created at: 2024-02-22 04:43:00 UTC  
> Url: https://github.com/boostorg/parser/issues/95#issuecomment-1958689697  

I understand your concern.  I think the thing you might be missing though is that this library does not ever count code units, code points, or anything else (except the repetition of certain parsers).  The key thing is that when we match text A with text B, we must be sure that what we're matching makes sense.  I don't think that if A is a prefix of B that, such that A ends in code units that are not well-formed UTF-8, that the "incomplete"/ill-formed part should count.  I think if you see what kinds of things get matched with and without this rule you'll see that this choice leads to far fewer surprises.  
  
This is distinct from the other cases you mention in utf8everywhere.org, all of which I agree with.  
  
I'm closing this now, but if you have anything else to add, feel free to do so here and I'll see it.

---

## Comment 4

> Username: pavel-teramips  
> Created at: 2024-02-22 20:18:27 UTC  
> Url: https://github.com/boostorg/parser/issues/95#issuecomment-1960234460  

Hi Zach,  
  
I'd say: KISS and single-responsibility:  
My take is that the handling of encodings is not the responsibility of a  
"parsing combinator" library. It's rather the responsibility of the calling  
code and/or the individual parsers. E.g. if I have a char[] containing  
(potentially) utf-8 data, then I'd expect that, either:  
  
* individual parsers match utf-8 subsequences; if a sub-parser wants to  
match the U+00CC codepoint (Ì latin capital letter i with grave), then the  
sub-parser would be matching the utf-8 substring "\x8c\xc3" (or better to  
be written in code simply as "Ì"); or  
* the char sequence is converted to a sequence of codepoints (either as a  
separate step, or using some utf-8 -> utf-32 on-the-fly converting  
iterator) before it hits the parsing library; here the sub-parser can match  
the codepoint 0xCC directly; or  
* the char sequence is converted to some longer tokens (graphemes, words,  
what not), and the individual parsers operate on those (e.g. a statement  
parser would check for identifier token).  
  
The choice between these is in the user's hands, and personally I'd choose  
either the 1st or the 3rd option. I never found the 2nd one handy.  
  
Let me inline-reply to some of your statements though:  
  
> "If you want to parse a char at a time, use char as the parsed range's  
value type. If you want to match code points, use one of the Unicode-aware  
parsable range types (charN_t in C++20 and later, plus utf8_view in c++17)."  
It seems that the library already allows doing option 1 from above,  
therefore it passes as "unicode compatible" for me. Any additional "unicode  
awareness" (not sure what it means here), shouldn't be this library's  
responsibility, as said earlier.  
As a side note, I do think that the addition of char8_t to C++ was a  
mistake (it should have been an alias for char; which should have been  
unsigned from the start; but that's another story.)  
  
> "...reason matching is done only on code points is that this is what  
*every* Unicode algorithm does. There is not a single Unicode algorithm  
that operates on UTF-8 code units..."  
Not quite. Unicode algorithms can work on code-units directly. This isn't  
even at all hypothetical. strstr is the simplest such Unicode algorithm,  
that does the right thing when applied to UTF-8. ICU, to the best of my  
knowledge, used to operate on UTF-16 codeunits instead of UTF-32  
codepoints. It would make sense for lexers to work on UTF-8 bytes when  
matching Unicode classes if most of the input is expected to be ASCII (e.g.  
programming language identifiers). I don't know if anyone implemented a  
UTF-8-only ICU alternative yet, but I think that it's something that the  
industry actually does need.  
  
> "I don't think that if A is a prefix of B that, such that A ends in code  
units that are not well-formed UTF-8, that the "incomplete"/ill-formed part  
should count. I think if you see what kinds of things get matched with and  
without this rule you'll see that this choice leads to far fewer surprises."  
I disagree:  
* If someone writes a parser that deliberately matches a prefix with an  
incomplete UTF-8 sequence, then there should be no extra logic in this  
library that catches that and fails the match. E.g. if X matches the "\x8c"  
prefix, and Y matches the following "\xc3", then XY should match "\x8c\xc3".  
* I'd actually expect that in practice it'd be rare for correctly  
implemented parsers to ever match partial code-points. Perhaps you can give  
some examples for what kinds of surprises you're referring to.  
  
Regardless, utf8everywhere is rather outdated (Windows since added support  
for UTF-8).  
  
HTH,  
Yakov  
  
  
On Wed, Feb 21, 2024 at 9:22 PM Pavel Radzivilovsky ***@***.***>  
wrote:  
  
> Zach,  
>  
> First, thanks for the answer. You are right of course about unicode  
> algorithms. Let me try to address this from a different perspective. The  
> history of treating Unicode text is full of confusion and things the  
> industry learned over the years. If we are going to make another text  
> managing tool, I believe it's important to avoid making another mistake.  
> Our claim is much stronger than a recommendation of text encoding.  
>  
> + Yakov in CC  
>  
> So, you are saying: *"There is not a single Unicode algorithm that  
> operates on UTF-8 code units. For algorithms on text, the code point is the  
> minimum unit of work, not the code unit."*  
> This is true, but ideally it's what should have never been like this. Let  
> me say this: looking at the industry today, what I see is (near) *zero  
> use* for any processing of text that takes anything more than a code unit  
> or anything less than a grapheme cluster as a working unit. In particular,  
> we found no real-world task that it would make sense to solve by counting,  
> iterating or otherwise viewing text as a sequence of unicode code points.  
> On the other hand, we see a lot of damage, both to program performance and  
> occasionally even broken logic caused by developer confusion that resulted  
> from reiterating the idea of a unicode code point as a reasonable  
> processing unit for text algorithms.  
>  
> Python3 strings are a good example of this. A redundant complexity  
> cripples performance of almost any software written in this  
> language, because someone had an idea that an in-memory array of code  
> points may be good for something. On the broken text handling side, the  
> number of issues github-wide in some of the best software (e.g.  
> PrusaSlicer) is staggering.  
>  
> In our view of the future, these older Windows and Apple systems you  
> mention will eventually go down in history followed by any  
> codepoint-iterating code, and the code points will only remain as a concept  
> in the definition of the Unicode standard, but not in actual  
> machine-executed software, which will focus on (specifically UTF-8) code  
> units for program logic and grapheme clusters for visual representation. I  
> hope we can agree on this.  
>  
> Kind regards and thank you very much for contributing to the ecosystem,  
> Pavel  
>  
>  
> On Tue, Feb 20, 2024 at 12:43 AM Zach Laine ***@***.***>  
> wrote:  
>  
>>  
>> I've read this whole thing before (as a member of the Committee's SG-16);  
>> I just skimmed it this time. Could you point specifically to the part(s) of  
>> it that apply to this parser? I honestly can't find it.  
>>  
>> For my part, the rationale is this. If you want to parse a char at a  
>> time, use char as the parsed range's value type. If you want to match  
>> code points, use one of the Unicode-aware parsable range types (charN_t in  
>> C++20 and later, plus utf8_view in c++17). It is 100% up to the user  
>> which one to use.  
>>  
>> In the Unicode path, the reason matching is done only on code points is  
>> that this is what *every* Unicode algorithm does. There is not a single  
>> Unicode algorithm that operates on UTF-8 code units. For algorithms on  
>> text, the code point is the minimum unit of work, not the code unit.  
>>  
>> I understand why you want people to represent their text in UTF-8; I  
>> agree completely. I also agree that filenames should be matched char by  
>> char -- but that's not because it's the right thing to do w.r.t.  
>> Unicode, but instead because filesystems are crazy w.r.t. encodings.  
>> There's UCS-2/UTF-16 confusion on older Windows filesystems, there's  
>> normalization confusion on Apple filesystems, etc. Similar oddities occur  
>> when you deal with env. vars. Such text should be treated as an opaque bag  
>> of bytes, to be sure. Anything else is going to mess you up eventually.  
>>  
>> General-purpose text does not usually have these problems. Moreover, if  
>> it does, use the non-Unicode code path.  
>>  
>> —  
>> Reply to this email directly, view it on GitHub  
>> <https://github.com/tzlaine/parser/issues/95#issuecomment-1953241636>,  
>> or unsubscribe  
>> <https://github.com/notifications/unsubscribe-auth/ALIXCFMJOJDKI2BFXSPTS3LYUPISXAVCNFSM6AAAAABDP22AZ2VHI2DSMVQWIX3LMV43OSLTON2WKQ3PNVWWK3TUHMYTSNJTGI2DCNRTGY>  
>> .  
>> You are receiving this because you authored the thread.Message ID:  
>> ***@***.***>  
>>  
>  
  
--   
Yakov Galka  
http://stannum.io/

---

## Comment 5

> Username: ybungalobill  
> Created at: 2024-02-22 20:24:52 UTC  
> Url: https://github.com/boostorg/parser/issues/95#issuecomment-1960250441  

Oops; I'm the author of the previous message. I just replied to bugpwr's e-mail and didn't expect it to show up like that.

---

## Comment 6

> Username: tzlaine  
> Created at: 2024-02-23 00:24:39 UTC  
> Url: https://github.com/boostorg/parser/issues/95#issuecomment-1960558812  

> Hi Zach, I'd say: KISS and single-responsibility: My take is that the handling of encodings is not the responsibility of a "parsing combinator" library. It's rather the responsibility of the calling code and/or the individual parsers. E.g. if I have a char[] containing (potentially) utf-8 data, then I'd expect that, either: * individual parsers match utf-8 subsequences; if a sub-parser wants to match the U+00CC codepoint (Ì latin capital letter i with grave), then the sub-parser would be matching the utf-8 substring "\x8c\xc3" (or better to be written in code simply as "Ì"); or * the char sequence is converted to a sequence of codepoints (either as a separate step, or using some utf-8 -> utf-32 on-the-fly converting iterator) before it hits the parsing library; here the sub-parser can match the codepoint 0xCC directly; or * the char sequence is converted to some longer tokens (graphemes, words, what not), and the individual parsers operate on those (e.g. a statement parser would check for identifier token). The choice between these is in the user's hands, and personally I'd choose either the 1st or the 3rd option. I never found the 2nd one handy.  
  
Well, I think we're saying nearly the same thing.  You want the library to be encoding agnostic, and it has that -- just parse a range of `char`.  I agree that this is an important case, and it actually gives you the subsequence-matching UTF-8 behavior that you favor.  
  
However, if you do decide to parse UTF, I think the exact UTF should not matter.  This is not just my opinion, but also the opinion of the Unicode standard, which says that text in one UTF must be considered equivalent to text in another UTF.  So if I match against `char_(U'ü')`, and I match a prefix of that, that's a problem.  If I specify `char_(u8'\xc3')`, Unicode tells me that's an ill-formed UTF-8 sequence, which should be treated as U+FFFD.  If I do anything else than treat UTF as full code points to compare to other UTF code points (whether they're the same UTF or not), it's going to produce very surprising parse results.  If people have to change all their parsers to drop UTF-32 and use UTF-8 because the input changed, that's *also* a problem.  This is largely about the ergonomics of a generic programming API.  If I describe the parser once, I should be able to use it with UTF-N, regardless of the N, without rewriting my parsers.  
  
> Let me inline-reply to some of your statements though:  
> "If you want to parse a char at a time, use char as the parsed range's  
> value type. If you want to match code points, use one of the Unicode-aware parsable range types (charN_t in C++20 and later, plus utf8_view in c++17)." It seems that the library already allows doing option 1 from above, therefore it passes as "unicode compatible" for me. Any additional "unicode awareness" (not sure what it means here), shouldn't be this library's responsibility, as said earlier. As a side note, I do think that the addition of char8_t to C++ was a mistake (it should have been an alias for char; which should have been unsigned from the start; but that's another story.)  
  
What I just said above applies here.  BTW, I was one of the few Unicode people on the committee to vote against char8_t, but that's also antoher story. :)  
  
> "...reason matching is done only on code points is that this is what  
> *every* Unicode algorithm does. There is not a single Unicode algorithm that operates on UTF-8 code units..." Not quite. Unicode algorithms can work on code-units directly. This isn't even at all hypothetical. strstr is the simplest such Unicode algorithm, that does the right thing when applied to UTF-8. ICU, to the best of my knowledge, used to operate on UTF-16 codeunits instead of UTF-32 codepoints. It would make sense for lexers to work on UTF-8 bytes when matching Unicode classes if most of the input is expected to be ASCII (e.g. programming language identifiers). I don't know if anyone implemented a UTF-8-only ICU alternative yet, but I think that it's something that the industry actually does need.  
  
`strstr` is not a Unicode algorithm!  It's just some function from the C standard library.  
  
ICU added UTF-8 code paths to a bunch of their algorithms (most notable normalization) a few years ago.  However, this reasoning does not apply to Boost.Parser.  The reason ICU can get away with this is that it's just an implementation detail -- who cares if you actually transcode UTF-8 or -16 to -32, as long as you get the same result?  But when you start to use UTF-8 and UTF-32 *together*, code unit by code unit, you get a different result if you do the transcoding than you get if you don't.  
  
> "I don't think that if A is a prefix of B that, such that A ends in code  
> units that are not well-formed UTF-8, that the "incomplete"/ill-formed part should count. I think if you see what kinds of things get matched with and without this rule you'll see that this choice leads to far fewer surprises." I disagree: * If someone writes a parser that deliberately matches a prefix with an incomplete UTF-8 sequence, then there should be no extra logic in this library that catches that and fails the match. E.g. if X matches the "\x8c" prefix, and Y matches the following "\xc3", then XY should match "\x8c\xc3". * I'd actually expect that in practice it'd be rare for correctly implemented parsers to ever match partial code-points. Perhaps you can give some examples for what kinds of surprises you're referring to. Regardless, utf8everywhere is rather outdated (Windows since added support for UTF-8). HTH, Yakov  
  
Again, this behavior is available if desired.  I just think that most users will not desire this.  As an example that you asked for, here's a snippet from the Unicode Support page of the docs:  
  
```c++  
namespace bp = boost::parser;  
  
{  
    std::string str = (char const *)u8"\xcc\x80"; // encodes the code point U+0300  
    auto first = str.begin();  
  
    // Since we've done nothing to indicate that we want to do Unicode  
    // parsing, and we've passed a range of char to parse(), this will do  
    // non-Unicode parsing.  
    std::string chars;  
    assert(bp::parse(first, str.end(), *bp::char_('\xcc'), chars));  
  
    // Finds one match of the *char* 0xcc, because the value in the parser  
    // (0xcc) was matched against the two code points in the input (0xcc and  
    // 0x80), and the first one was a match.  
    assert(chars == "\xcc");  
}  
{  
    std::u8string str = u8"\xcc\x80"; // encodes the code point U+0300  
    auto first = str.begin();  
  
    // Since the input is a range of char8_t, this will do Unicode  
    // parsing.  The same thing would have happened if we passed  
    // str | boost::parser::as_utf32 or even str | boost::parser::as_utf8.  
    std::string chars;  
    assert(bp::parse(first, str.end(), *bp::char_('\xcc'), chars));  
  
    // Finds zero matches of the *code point* 0xcc, because the value in  
    // the parser (0xcc) was matched against the single code point in the  
    // input, 0x0300.  
    assert(chars == "");  
}  
```  
  
Note that the value being matched could be computed from some operation happening during the parse.  If you made a math error, and asked if `'\xcc'` matched `'◌̀'` in the Unicode path, you should get an "no", not a "yes".  If a user gets am unexpected "no", they at least know to investigate.  If they get an unexpected "yes", that's probably a silent failure producing a wrong result.  
  
The bottom line is that Unicode uses code points as the basic unit of work.  Code units exist only as encodings for code points.  There are no Unicode operations that operate directly on code units, except the ones related to encoding, decoding, and encoding validation.
