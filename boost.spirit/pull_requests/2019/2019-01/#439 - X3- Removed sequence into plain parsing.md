# #439 X3:  Removed sequence into plain parsing [Merged]

> Username: Kojoley  
> Created at: 2019-01-15 20:24:43 UTC  
> Updated at: 2019-01-21 11:23:54 UTC  
> Merged at: 2019-01-21 11:23:50 UTC  
> Closed at: 2019-01-21 11:23:50 UTC  
> Url: https://github.com/boostorg/spirit/pull/439  

Closes #438.

---

## Comment 1

> Username: djowel  
> Created_at: 2019-01-15 23:55:47 UTC  
> Url: https://github.com/boostorg/spirit/pull/439#issuecomment-454597455  

I assume all tests pass? Just be sure this does not affect rules. I think you are in the right direction, but I'm somewhat worried.

---

## Review 2 [Commented]

> Username: cppljevans  
> Created_at: 2019-01-16 00:18:28 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/spirit/pull/439#pullrequestreview-192926201  

pass_sequence_container_attribute should be renamed to disable_into_container since,  
judging from:  
https://github.com/boostorg/spirit/blob/a6132ae361b5f4d3480d23200ea8d10b8911ff55/include/boost/spirit/home/x3/operator/detail/sequence.hpp#L329-L349  
that name more accurately expresses its meaning.  
  
Yes, that's more changes without any semantics difference; however,  
it improves the readability of the code.

---

## Comment 3

> Username: Kojoley  
> Created_at: 2019-01-16 00:54:03 UTC  
> Updated_at: 2019-01-16 00:57:07 UTC  
> Url: https://github.com/boostorg/spirit/pull/439#issuecomment-454609530  

> I assume all tests pass?  I think you are in the right direction, but I'm somewhat worried.  
  
Yes, and examples compile too. We also have CI set up for this purpose.  
  
> Just be sure this does not affect rules.  
  
The commit where the code come 235359a45c937daf2c96ce96e869d7e57604eb01 seems to be about tuple elements pass-through, and rules itself is just a parser for sequences.  
  
> I think you are in the right direction, but I'm somewhat worried.  
  
I expect this change to break the code that is already silently broken (like #434).  
  
  
@cppljevans I prefer not to make unrelated changes. I agree that my naming may be confusing, you can open a PR for this, but the `pass` means something like pass-through or forwarding, it was named after the `pass_sequence_attribute` trait.

---

## Review 4 [Commented]

> Username: cppljevans  
> Created_at: 2019-01-17 13:22:14 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/spirit/pull/439#pullrequestreview-193619690  

Shouldn't there be a test verifying the statement:  
  
  The `eps >> eps` parser will still parse into `tuple<>`.  
  
?

---

## Comment 5

> Username: Kojoley  
> Created_at: 2019-01-17 13:27:05 UTC  
> Url: https://github.com/boostorg/spirit/pull/439#issuecomment-455171320  

I would like to add a test case that ensures `eps >> eps` does NOT compile with `tuple<>` attribute, but I do not want to make the current code more complex just to disable it and compile fail tests are too slow (end requires a separate file for every case).

---

## Comment 6

> Username: cppljevans  
> Created_at: 2019-01-17 13:55:56 UTC  
> Url: https://github.com/boostorg/spirit/pull/439#issuecomment-455180022  

> I would like to add a test case that ensures `eps >> eps` does NOT compile with `tuple<>` attribute, but I do not want to make the current code more complex just to disable it and compile fail tests are too slow (end requires a separate file for every case).  
  
If it does NOT compile, then I'm puzzled because I'm getting it to compile.  With:  
  
````cpp  
#include <boost/spirit/home/x3.hpp>  
#include <boost/fusion/container/vector.hpp>  
int main()  
{  
    {  
        using boost::spirit::x3::eps;  
    //Copy&past from:  
    //  https://github.com/boostorg/spirit/pull/439/commits/2235da2416b1ffbaa167aa2e9ebc5aeb98eab11b  
        char const* const s = "";  
        boost::fusion::vector<> a;  
        parse(s, s, eps >> eps, a);  
    }  
    return 0;  
}  
````  
It compiles with your patch:  
  
````  
/usr/bin/clang++ -c -O0 -gdwarf-2  -std=c++17 -ftemplate-backtrace-limit=0 -fdiagnostics-show-template-tree -fno-elide-type -fmacro-backtrace-limit=0     -I../../x3-Removed_sequence_into_plain_parsing-zip/include -I/home/evansl/prog_dev/boost/releases/ro/boost_1_69_0    -ftemplate-depth=200  test-sequence-eps2.cpp -MMD -o /tmp/build/clangxx6_0_pkg/boost/releases/ro/boost_1_69_0/sandbox/kojoley/test/x3-Removed_sequence_into_plain_parsing/test-sequence-eps2.o   
/usr/bin/clang++    /tmp/build/clangxx6_0_pkg/boost/releases/ro/boost_1_69_0/sandbox/kojoley/test/x3-Removed_sequence_into_plain_parsing/test-sequence-eps2.o   -o /tmp/build/clangxx6_0_pkg/boost/releases/ro/boost_1_69_0/sandbox/kojoley/test/x3-Removed_sequence_into_plain_parsing/test-sequence-eps2.exe  
/tmp/build/clangxx6_0_pkg/boost/releases/ro/boost_1_69_0/sandbox/kojoley/test/x3-Removed_sequence_into_plain_parsing/test-sequence-eps2.exe   
  
Compilation finished at Thu Jan 17 07:41:17  
````  
Your patch is in the:  
  
  ../../x3-Removed_sequence_into_plain_parsing-zip/include  
  
directory.  
  
The -zip directory was downloaded early yesterday from:  
  
https://github.com/boostorg/spirit/tree/a6132ae361b5f4d3480d23200ea8d10b8911ff55  
  
What might I be doing wrong?  
  
TIA.  
  
-regards,  
Larry

---

## Comment 7

> Username: Kojoley  
> Created_at: 2019-01-17 14:23:37 UTC  
> Url: https://github.com/boostorg/spirit/pull/439#issuecomment-455188805  

> What might I be doing wrong?  
  
Possibly I was not clear enough, I try to put emphasis on my previous message  
  
> **I would like** to add a test case that ensures `eps >> eps` does NOT compile with `tuple<>` attribute, **but I do not want to make the current code more complex just to disable it** ...  
  
I consider it as a bug and it is out of this PR scope. I have created #440 about it.

---

## Comment 8

> Username: Kojoley  
> Created_at: 2019-01-20 00:22:53 UTC  
> Url: https://github.com/boostorg/spirit/pull/439#issuecomment-455826512  

I think I figured out why sequence handling was done like this. The current fix was impossible without a8e391bd99dddb3f9ece84bdb1bb9236b0a37cf7/#340, before it sequence would try to unwrap its sequence-iterator even if there is a nested sequence and if unwrap was done the nested sequence receives a plain attribute which was handled by `parse_sequence_plain`. After a8e391bd99dddb3f9ece84bdb1bb9236b0a37cf7/#340 a sequence-iterator will always pass-through to nested sequences and unwrapped attribute never gets into the sequence parser, so `parse_sequence_plain` became obsolete.

---

## Comment 9

> Username: djowel  
> Created_at: 2019-01-20 00:36:17 UTC  
> Url: https://github.com/boostorg/spirit/pull/439#issuecomment-455827154  

Wonderful!

---
