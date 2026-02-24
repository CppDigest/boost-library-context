# #210 - punct may be missing punctuation characters [Closed]

> Username: hfinkel  
> Created at: 2024-12-24 22:30:46 UTC  
> Updated at: 2024-12-29 09:08:40 UTC  
> Closed at: 2024-12-27 01:45:08 UTC  
> Url: https://github.com/boostorg/parser/issues/210  

Whether this is a bug or a feature request might be a matter of opinion. In short, if you expect punct to return results consistent with std::ispunct, it does not. For that, it is missing characters for which ispunct should return true, such as 0x7c '|', and 0x3d '=' (referring to the chart on [https://en.cppreference.com/w/cpp/string/byte/ispunct](https://en.cppreference.com/w/cpp/string/byte/ispunct) seems to match the behavior of std::ispunct on my Linux system). At least the start of this array should likely be equivalent to:  
  
```cpp  
    char_(0x21, 0x2F) | char_(0x3A, 0x40) | char_(0x5B, 0x60) | char_(0x7B, 0x7E)  
```  
  
The array of punct characters explains that punct only captures Unicode punctuation characters:  
  
```cpp  
    // Produced from https://util.unicode.org/UnicodeJsps/list-unicodeset.jsp,  
    // using "[:Pc:][:Pd:][:Pe:][:Pf:][:Pi:][:Ps:][:Po:]" for the Input field,  
    // using the categories found at  
    // https://www.fileformat.info/info/unicode/category/index.htm  
    template<>  
    struct char_set<punct_chars>  
    {  
        static constexpr uint32_t chars[] = {  
            0x21,    0x22,    0x23,    0x25,    0x26,    0x27,    0x28,  
            0x29,    0x2a,    0x2c,    0x2d,    0x2e,    0x2f,    0x3a,  
            0x3b,    0x3f,    0x40,    0x5b,    0x5c,    0x5d,    0x5f,  
            0x7b,    0x7d,    0xa1,    0xa7,    0xab,    0xb6,    0xb7,  
            0xbb,    0xbf,    0x37e,   0x387,   0x55a,   0x55b,   0x55c,  
```  
  
There is a mismatch between the definition of punctuation used by ispunct and the Unicode definition. In Unicode, '|' is not punctuation, but rather a math symbol (in Sm). So is '='. Nevertheless, these ranges are included in [https://www.open-std.org/JTC1/SC35/WG5/docs/30112d10.pdf](https://www.open-std.org/JTC1/SC35/WG5/docs/30112d10.pdf) under punct:  
  
```  
punct /  
 <U0021>..<U002F>;<U003A>..<U0040>;<U005B>..<U0060>;<U007B>..<U007E>;/  
 <U00A0>..<U00A9>;<U00AB>..<U00B4>;<U00B6>..<U00B9>;<U00BB>..<U00BF>;/  
...  
```  
I also note that ispunct in the C specification is defined to:  
  
```  
The ispunct function tests for any printing character that is one of a locale-specific set of punctuation  
characters for which neither isspace nor isalnum is true. In the "C" locale, ispunct returns true  
for every printing character for which neither isspace nor isalnum is true.  
```  
  
In short, my sense is that either the set of punctuation characters should be expanded, or a separate set of symbol characters should be added. However, I found it surprising that a punct did not include all of the characters for which ispunct would return true.

---

## Comment 1

> Username: tzlaine  
> Created at: 2024-12-27 01:45:08 UTC  
> Updated at: 2024-12-29 08:58:39 UTC  
> Url: https://github.com/boostorg/parser/issues/210#issuecomment-2563225197  

I specifically no not want to match `ispunct`'s behavior.  In particular, SG-16 Unicode (and I) consider it to be hopelessly broken.  In particular, it's locale-dependency makes it not work *even for chars in the ASCII range*, depending on your locale.  
  
If you want to make something that uses `ispunct`, you can do that using a rule containing a semantic action, like (warning, untested):  
  
```c++  
bp::rule<struct punct_tag, char> punct = "punct";  
auto const is_punct = [](auto ctx) {  
    _val(ctx) = _attr(ctx);  
    _pass(ctx) = std::ispunct(_attr(ctx));  
};  
auto punct_def = bp::char_[is_punct];  
BOOST_PARSER_DEFINE_RULES(punct);  
```

---

## Comment 2

> Username: hfinkel  
> Created at: 2024-12-28 14:05:55 UTC  
> Url: https://github.com/boostorg/parser/issues/210#issuecomment-2564333368  

Thanks, Zach. That example is helpful. Actually, what I'm looking for is to match word breaks in a Unicode-safe way. It's not clear how to do that in a canonical way right now. You have parsers to match ws, control, punct, and subclasses of letters and numbers (upper, lower, digit, etc.), but no obvious way to match all letters, numbers, symbols, etc., or their complement.

---

## Comment 3

> Username: tzlaine  
> Created at: 2024-12-29 08:58:07 UTC  
> Updated at: 2024-12-29 09:08:40 UTC  
> Url: https://github.com/boostorg/parser/issues/210#issuecomment-2564657811  

Ah, I see.  That's indeed something different.  If you're trying to do fully correct word breaking with Boost.Parser, it's going to be somewhat difficult.  
  
First off, none of the character classes like upper, lower, digit, etc., matches the character classes used by the Unicode word breaking algorithm.  This is a weird thing about Unicode, IMO.  The notion of certain character classes (e.g. "digit") differs among the general (not-for-algorithm-use) character classes like the ones in Boost.Parser and the specific ones used within the various Unicode algorithms.  A certain code point may be a digit for the purposes of word breaking, but not for purposes of the "digit" character class, or vice versa.  This is just an example -- I'm not sure there are differences for digits in particular, but you get the idea.  
  
Second, the word breaking algorithm has many more classes that it considers when determining word breaks than the few classes Boot.Parser provides.  
  
The Unicode word breaking algorithm uses the character classes for up to two previous code points when looking at the current code point, in order to determine a word boundary.  I have an implementation in Boost.Text (a proposed Boost lib, not accepted).  I suggest you use that instead if you're only wanting to use Boost.Parser for word breaking.  See:  
  
https://github.com/tzlaine/text  
https://tzlaine.github.io/text/doc/html/boost_text__proposed_/the_unicode_layer/text_segmentation.html  
  
However, if you want to do word boundary discovery as part of a larger parse, it would very much depend on the particulars of at what point in the parse you need to find the boundary.  
  
If it's part of a larger parse, it might be useful to do the larger parse, and then use a separate boundary detector, like the one in Boost.Text in a semantic action attached to the larger parse.  
  
You could also write a parse predicate like `at_boundary`:  
  
```c++  
auto at_boundary = [](auto & ctx) {  
  // read current code point, and up to two code points prior;  
  // return true iff this is a boundary point  
  auto it = std::get<0>(_where(ctx));  
  return boost::parser::prev_word_break(_begin(ctx), it, _end(ctx)) == it;  
};  
auto rest_of_word = *(bp::char_ - bp::if_(at_boundary));  
auto word = bp::if_(at_boundary) >> rest_of_word;  
```  
  
So, `rest_of_word` would match the rest of the current word, and `word` would match an entire word (it must begin *and* end on a word boundary).  
  
Checking each code point using `boost::parser::prev_word_break` is a bit wasteful, since it classifies each code point each time.  The only way to remove that inefficiency is to write your own custom parser.  That's pretty involved; I don't recommend it unless you must, because you've found that the perf is too awful.  Doing it that way would allow you simply to use `boost::parser::prev_word_break` and `boost::parser::next_word_break` directly on the input range, in such a way that each need only be called once.
