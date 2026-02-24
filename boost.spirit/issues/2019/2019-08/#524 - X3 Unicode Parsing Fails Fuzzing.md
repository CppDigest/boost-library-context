# #524 - X3 Unicode Parsing Fails Fuzzing [Closed]

> Username: cmazakas  
> Created at: 2019-08-16 14:45:34 UTC  
> Updated at: 2019-08-25 23:59:56 UTC  
> Closed at: 2019-08-25 23:59:56 UTC  
> Url: https://github.com/boostorg/spirit/issues/524  

I'm writing an HTTP helpers library. Part of this includes parsing URIs. For this task, I chose X3 and while the ASCII parser seems to stand up to fuzzing fine, the Unicode parsers do not seem to be able to.  
  
Given the fuzzing file found here:  
https://github.com/LeonineKing1199/foxy/blob/52aff49a4ff348c98efc53b195abdad46db436e3/fuzz/parse_uri.cpp  
  
and compiling with both ubsan and asan on clang-6 using OS X, I get the following errors:  
```  
Christians-MBP:build_Debug exbigboss$ ./uri-parser -max_total_time=2400  
INFO: Seed: 105625569  
INFO: Loaded 1 modules   (668 inline 8-bit counters): 668 [0x10382d490, 0x10382d72c),   
INFO: Loaded 1 PC tables (668 PCs): 668 [0x10382d730,0x1038300f0),   
INFO: -max_len is not provided; libFuzzer will not generate inputs larger than 4096 bytes  
INFO: A corpus is not provided, starting from an empty corpus  
#2	INITED cov: 58 ft: 58 corp: 1/1b exec/s: 0 rss: 46Mb  
/Users/exbigboss/boosts/71/install/include/boost/spirit/home/support/char_encoding/unicode/category_table.hpp:2212:42: runtime error: index 16777215 out of bounds for type 'const ::boost::uint8_t [4352]'  
SUMMARY: UndefinedBehaviorSanitizer: undefined-behavior /Users/exbigboss/boosts/71/install/include/boost/spirit/home/support/char_encoding/unicode/category_table.hpp:2212:42 in   
	NEW_FUNC[0/9]: 0x10316db80 in std::__1::vector<char32_t, std::__1::allocator<char32_t> >::allocate(unsigned long) vector:934  
	NEW_FUNC[1/9]: 0x1031707b0 in std::__1::vector<char32_t, std::__1::allocator<char32_t> >::__construct_at_end(unsigned long) vector:985  
#4	NEW    cov: 264 ft: 269 corp: 2/6b exec/s: 0 rss: 48Mb L: 5/5 MS: 2 ShuffleBytes-InsertRepeatedBytes-  
/Users/exbigboss/boosts/71/install/include/boost/spirit/home/support/char_encoding/unicode/category_table.hpp:2213:16: runtime error: index 28415 out of bounds for type 'const ::boost::uint16_t [25856]'  
SUMMARY: UndefinedBehaviorSanitizer: undefined-behavior /Users/exbigboss/boosts/71/install/include/boost/spirit/home/support/char_encoding/unicode/category_table.hpp:2213:16 in   
=================================================================  
==1307==ERROR: AddressSanitizer: global-buffer-overflow on address 0x0001036f1e5e at pc 0x000103340178 bp 0x7ffeeca95530 sp 0x7ffeeca95528  
READ of size 2 at 0x0001036f1e5e thread T0  
```  
  
The errors go on for quite some time after that.  
  
Is there something about X3 I should be made aware of as a user? For example, if a user is required to provide valid Unicode bytes only, that is acceptable.  
  
Update:  
  
Removing all invalid Unicode code points seem to keep ubasan happy.  
  
For example, the following patch will work fine:  
```cpp  
  auto const valid_size =  
    std::remove_if(code_points.begin(), code_points.end(),  
                   [](auto const code_point) -> bool { return code_point >= 0x10ffff; }) -  
    code_points.begin();  
  
  auto const unicode_input = boost::u32string_view(code_points.data(), valid_size);  
  foxy::parse_uri(unicode_input);  
```

---

## Comment 1

> Username: djowel  
> Created at: 2019-08-16 23:36:59 UTC  
> Url: https://github.com/boostorg/spirit/issues/524#issuecomment-522181600  

Would you care to provide a PR for this?

---

## Comment 2

> Username: djowel  
> Created at: 2019-08-17 09:27:38 UTC  
> Url: https://github.com/boostorg/spirit/issues/524#issuecomment-522221243  

> Removing all invalid Unicode code points seem to keep ubasan happy.  
  
OK, maybe I am misunderstanding what you mean by that ^

---

## Comment 3

> Username: cmazakas  
> Created at: 2019-08-17 15:59:51 UTC  
> Url: https://github.com/boostorg/spirit/issues/524#issuecomment-522249483  

Ah, I may have misspoke.  
  
In essence, I fuzzed an X3-based application using libFuzzer. I took the provided random bytes and converted them to a sequence of `char32_t`s (Unicode literals) and then attempted to parse them with `x3::unicode::*`. Naturally, this sequence would contain invalid Unicode code points.  
  
The problem is in the lookup done here: https://github.com/boostorg/spirit/blob/develop/include/boost/spirit/home/support/char_encoding/unicode/category_table.hpp#L2212  
  
This causes an out-of-bounds access for code points larger than the valid Unicode maximum.  
  
When I rewrote my fuzzing test to remove invalid Unicode literals from the supplied data, all the sanitizers reported no errors.  
  
I wasn't sure if this was an intentional design decision or not so I figured I'd make an issue. The design decision, of course, being: parsing invalid Unicode with X3 is UB.

---

## Comment 4

> Username: djowel  
> Created at: 2019-08-17 22:56:25 UTC  
> Url: https://github.com/boostorg/spirit/issues/524#issuecomment-522276510  

The question now is: and this also relates to the other char parsers, e.g. the assert on ascii > 127; should it be spirit's responsibility to check for invalid input? As a parser, one might say yes (I'd say yes), but that would also mean another check that you pay for even if your input is clean to begin with.

---

## Comment 5

> Username: cmazakas  
> Created at: 2019-08-22 02:44:24 UTC  
> Url: https://github.com/boostorg/spirit/issues/524#issuecomment-523723874  

That's a good point.  
  
We might be able to do something like bifurcate the interface: have a trusted and untrusted parser.  
  
If a consumer of X3 is wishing to parse user-supplied strings, they'd have to do input validation no matter what. I'm not sure of the number of users wanting to "just write a parser" would be into also writing their own Unicode validation schemes or finding a library that'd do it for them.  
  
In my experience/opinion, most users just want the all-in-one default safe option.  
  
In an ideal case, a user would be able to do something like:  
```c++  
auto const match = x3::parse(pos, end, x3::unicode::safe::char_);  
```  
  
This function would act as a wrapper over the original `x3::unicode::char_` parser.  
  
I also wanted to confirm that if X3 does natively parse utf-8 and utf-16, there'd be significant gains here. Validating Unicode literals is trivial as shown above. Validating utf-8 is not something I think users should be hand-rolling.

---

## Comment 6

> Username: cmazakas  
> Created at: 2019-08-22 15:21:09 UTC  
> Url: https://github.com/boostorg/spirit/issues/524#issuecomment-523952441  

On second thought, it'd probably make the most sense to put Unicode validation into the `x3::parse` function itself, no?

---

## Comment 7

> Username: teajay-fr  
> Created at: 2019-08-22 17:53:52 UTC  
> Url: https://github.com/boostorg/spirit/issues/524#issuecomment-524010658  

I would expect spirit to fail on invalid input in a proper reported manner as this is the contract respected by all the parsers. Given input which doesn't conform to what is expected the parsing must fail.   
I wouldn't try to make the validation of the input optional, even if checking the validity of the encoding might cost a bit of performance. Making assumptions on the input, and accepting that invalid memory accesses may occur is not acceptable from my point of view. I wouldn't leave any security loop holes.  
  
Since the aspect of checking encoding validity is part of the decoding of unicode, I would place the check in the area of the decoding : inside the the x3::unicode::char_ parser.

---

## Comment 8

> Username: djowel  
> Created at: 2019-08-22 21:55:58 UTC  
> Url: https://github.com/boostorg/spirit/issues/524#issuecomment-524093227  

Agreed. Would anyone venture on a PR?

---

## Comment 9

> Username: djowel  
> Created at: 2019-08-22 22:47:44 UTC  
> Url: https://github.com/boostorg/spirit/issues/524#issuecomment-524106299  

> Agreed. Would anyone venture on a PR?  
  
cough cough :-)

---

## Comment 10

> Username: cmazakas  
> Created at: 2019-08-23 15:38:17 UTC  
> Url: https://github.com/boostorg/spirit/issues/524#issuecomment-524362349  

I can do that.  
  
But I have some questions about potential design.  
  
I've examined how X3 does some of its parsing. I think the most straight-forward and easiest thing to do is to modify `parse_main` to do two traversals over the given input range. The first pass would be to deduce the encoding type and then validate the correctness of all the bytes in the range.  
  
The second pass would be dispatching to the `.parse(...)` call as is now.  
  
To me, this is the easiest way to retrofit this behavior for _all_ parsers in X3 but it comes at the cost of multiple traversals which can be prohibitive for sufficiently large input sizes.  
  
By that same token, it's easier to preserve the initial state of the user-supplied Attribute. If we detect an invalid character encoding, we can simply return early and the user's out-param will be no worse for the wear.

---

## Comment 11

> Username: djowel  
> Created at: 2019-08-23 23:37:33 UTC  
> Url: https://github.com/boostorg/spirit/issues/524#issuecomment-524493158  

Ehm, I'm probably not understanding your explanation, but we should limit the fix to the unicode char parsers only. I don't think there's a need to change all parsers.

---

## Comment 12

> Username: cmazakas  
> Created at: 2019-08-24 02:38:34 UTC  
> Url: https://github.com/boostorg/spirit/issues/524#issuecomment-524509731  

Can do! I'll just update the `any_char` parser in that case!

---

## Comment 13

> Username: djowel  
> Created at: 2019-08-24 06:21:34 UTC  
> Updated at: 2019-08-24 06:21:53 UTC  
> Url: https://github.com/boostorg/spirit/issues/524#issuecomment-524524883  

Actually, unicode x3 support already has this: https://github.com/boostorg/spirit/blob/develop/include/boost/spirit/home/support/char_encoding/unicode.hpp#L37

---

## Comment 14

> Username: cmazakas  
> Created at: 2019-08-24 17:09:02 UTC  
> Url: https://github.com/boostorg/spirit/issues/524#issuecomment-524566384  

That's a really good point. This actually lead me down a few different paths. But I arrived here:  
https://github.com/boostorg/spirit/blob/develop/include/boost/spirit/home/x3/char/unicode.hpp#L414-L419  
  
I think there's a logic bug here. Instead of `||` we want `&&`. I tested this patch locally and now fuzzing is fine.

---

## Comment 15

> Username: djowel  
> Created at: 2019-08-24 22:47:25 UTC  
> Url: https://github.com/boostorg/spirit/issues/524#issuecomment-524586119  

> That's a really good point. This actually lead me down a few different paths. But I arrived here:  
> https://github.com/boostorg/spirit/blob/develop/include/boost/spirit/home/x3/char/unicode.hpp#L414-L419  
>   
> I think there's a logic bug here. Instead of `||` we want `&&`. I tested this patch locally and now fuzzing is fine.  
  
Hmmm... a latent bug? Feel free to issue a PR. While you are at it, there might be a similar issue with ascii.
