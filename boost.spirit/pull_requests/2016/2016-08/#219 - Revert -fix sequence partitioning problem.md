# #219 Revert "fix sequence partitioning problem" [Closed]

> Username: peterhuene  
> Created at: 2016-08-31 18:54:33 UTC  
> Updated at: 2017-12-29 23:50:57 UTC  
> Closed at: 2017-12-29 23:50:57 UTC  
> Url: https://github.com/boostorg/spirit/pull/219  

This reverts commit a8e391bd99dddb3f9ece84bdb1bb9236b0a37cf7.  
  
Please see [my mailing list message](http://boost.2283326.n4.nabble.com/X3-compilation-errors-upgrading-from-1-60-0-from-1-61-0-td4687736.html) and the [corresponding repro gist](https://gist.github.com/peterhuene/2ce5e403bf313ffc3f9caaaac8461bef) for context and repro, respectively.  
  
**_I'm putting up this PR for discussion and not for merge consideration as I don't like reverting a commit without replacing it with what the original commit was attempting to fix.**_

---

## Comment 1

> Username: djowel  
> Created_at: 2016-08-31 22:51:57 UTC  
> Url: https://github.com/boostorg/spirit/pull/219#issuecomment-243926805  

OK, I'll have a look. Before I do, have you looked at the corresponding test that this commit fixes? Perhaps that's a good starting point to base our discussion on possible fixes.

---

## Comment 2

> Username: peterhuene  
> Created_at: 2016-08-31 22:53:40 UTC  
> Url: https://github.com/boostorg/spirit/pull/219#issuecomment-243927151  

@djowel Sorry, I didn't see a corresponding test as I only saw the contents of that single commit.  I'll take a look.  Thanks for the response!

---

## Comment 3

> Username: djowel  
> Created_at: 2016-08-31 23:18:52 UTC  
> Url: https://github.com/boostorg/spirit/pull/219#issuecomment-243931551  

I'm assuming there is one, as typical.

---

## Comment 4

> Username: peterhuene  
> Created_at: 2016-08-31 23:19:34 UTC  
> Url: https://github.com/boostorg/spirit/pull/219#issuecomment-243931663  

@djowel I couldn't seem to find a test related to the fix (sadly, I'm not at all familiar with Spirit's tests) or a related Trac ticket, but I did see a fix for Ticket 11952 that was made on the same day (although that fix seems unrelated to the sequence attribute commit) which added a missing `typedef unused_type attribute_type` to `binary_lit_parser`.

---

## Comment 5

> Username: djowel  
> Created_at: 2016-08-31 23:23:41 UTC  
> Url: https://github.com/boostorg/spirit/pull/219#issuecomment-243932360  

OK, I'll take a look.

---

## Comment 6

> Username: djowel  
> Created_at: 2016-08-31 23:27:55 UTC  
> Url: https://github.com/boostorg/spirit/pull/219#issuecomment-243933113  

How about this... try running the X3 test before and after revert and compare the test results.

---

## Comment 7

> Username: peterhuene  
> Created_at: 2016-08-31 23:28:58 UTC  
> Url: https://github.com/boostorg/spirit/pull/219#issuecomment-243933287  

Can do.

---

## Comment 8

> Username: peterhuene  
> Created_at: 2016-09-01 00:04:54 UTC  
> Url: https://github.com/boostorg/spirit/pull/219#issuecomment-243938886  

Unless I'm running the X3 tests wrong (just simply doing `bjam -a` in `test/x3`; if this isn't correct, please let me know the correct way of running the tests), the tests appear to all pass before and after the commit is reverted.

---

## Comment 9

> Username: djowel  
> Created_at: 2016-09-01 00:13:36 UTC  
> Url: https://github.com/boostorg/spirit/pull/219#issuecomment-243940080  

oh no! i must've failed to commit the test it is supposed to fix; surely there is. now i have to go and find it.

---

## Comment 10

> Username: djowel  
> Created_at: 2016-09-01 00:33:10 UTC  
> Updated_at: 2016-09-01 00:34:46 UTC  
> Url: https://github.com/boostorg/spirit/pull/219#issuecomment-243942751  

Ok, I did some digging and here's what's appears to be the test case. See " Single element attributes in X3 "still" broken?" in the list. This bad boy breaks without the fix.  
  
```  
#include <boost/fusion/include/adapt_struct.hpp>  
#include <boost/spirit/home/x3.hpp>  
  
namespace x3 = boost::spirit::x3;  
  
struct inner { int value;   };  
struct outer { inner value; };  
  
BOOST_FUSION_ADAPT_STRUCT(inner, value)  
BOOST_FUSION_ADAPT_STRUCT(outer, value)  
  
namespace sample {  
    auto const rule = x3::rule<struct return_class, outer> {}  
                    = x3::attr(inner{42}) >> ';';  
}  
  
#include <iostream>  
int main() {  
    std::string const input = ";";  
  
    outer s;  
    bool ok = parse(input.begin(), input.end(), sample::rule >> x3::eoi, s);  
    assert(ok);  
}  
```

---

## Comment 11

> Username: djowel  
> Created_at: 2016-09-01 01:10:14 UTC  
> Url: https://github.com/boostorg/spirit/pull/219#issuecomment-243948167  

I added the test. I'll commit pending resolution to your case.

---

## Comment 12

> Username: peterhuene  
> Created_at: 2016-09-01 08:25:30 UTC  
> Updated_at: 2016-09-01 08:26:02 UTC  
> Url: https://github.com/boostorg/spirit/pull/219#issuecomment-244010652  

It appears the error from the repro can be worked around by precedence: a rule of `repro_string > repro_empty > repro_string` fails to build, but a rule of `repro_string > (repro_empty > repro_string)` builds successfully.  A rule of `repro_string > (repro_string > repro_empty)` fails to build, but a rule of `repro_string > repro_string > repro_empty` builds successfully.  
  
It seems like when the "unused" attributed parser is on the RHS, it fails and the attribute type is `boost::fusion::iterator_range` instead of the expected `std::string`.  I'll try to decipher the TMP in sequence implementation to figure out what's going on.

---

## Comment 13

> Username: djowel  
> Created_at: 2016-09-01 08:31:16 UTC  
> Url: https://github.com/boostorg/spirit/pull/219#issuecomment-244011959  

I still think that the "fix" is correct. I haven't investigated on your use case, but have you considered looking at the fundamental cause why your code breaks? Are you sure you are not exploiting an undocumented behavior?

---

## Comment 14

> Username: peterhuene  
> Created_at: 2016-09-01 17:51:14 UTC  
> Updated_at: 2016-09-01 17:53:28 UTC  
> Url: https://github.com/boostorg/spirit/pull/219#issuecomment-244158040  

I certainly could be relying on undocumented behavior (although when my implementation was originally written, I don't think the X3 docs even covered writing custom parsers; I figured out what I needed from X3's implementation) or simply my custom parser's implementation was never correct in the first place (it's effectively what was presented in the repro gist in terms of definition).  
  
Given my limited understanding of how this is supposed to work and that I have an acceptable workaround, I'm more than happy to defer to your expertise in this matter.

---

## Comment 15

> Username: djowel  
> Created_at: 2016-09-01 17:59:13 UTC  
> Url: https://github.com/boostorg/spirit/pull/219#issuecomment-244160373  

It might well be that you uncovered another possibly unrelated bug. Do you think it is possible to recreate the problem without the custom parsers? Maybe some basic off the shelf parsers with std::string and unused attributes?

---

## Comment 16

> Username: peterhuene  
> Created_at: 2016-09-01 18:01:50 UTC  
> Url: https://github.com/boostorg/spirit/pull/219#issuecomment-244161142  

I'll both look into why this is failing for my use case (I'm slowly understanding the implementation here) and see if I can reproduce it with a built-in parser.  I'll get back to you; please feel free to close this PR in the meantime or if you feel there's a better place to discuss this.  
  
Thanks very much for your feedback and attention on this issue; it is very appreciated.

---

## Comment 17

> Username: djowel  
> Created_at: 2016-09-01 18:04:41 UTC  
> Url: https://github.com/boostorg/spirit/pull/219#issuecomment-244162000  

I think it's best to keep this open for now. If you find that it is indeed an unrelated bug, then feel free to add more info here.

---

## Comment 18

> Username: peterhuene  
> Created_at: 2016-09-01 22:54:24 UTC  
> Url: https://github.com/boostorg/spirit/pull/219#issuecomment-244236821  

Here is a simplified repro without custom parsers.  
  
``` c++  
#include <iostream>  
#include <string>  
#include <cassert>  
#include <boost/spirit/home/x3.hpp>  
#include <boost/fusion/include/std_pair.hpp>  
  
using namespace std;  
namespace x3 = boost::spirit::x3;  
  
int main()  
{  
    string input = "";  
    pair<string, string> result;  
    assert(x3::parse(input.begin(), input.end(), x3::attr("foo") > x3::eps > x3::attr("bar"), result));  
  
    // Expected output: foobar  
    cout << result.first << result.second << endl;  
    return 0;  
}  
```  
  
Basically the same thing as before: this compiles in 1.60.0 but not 1.61.0.

---

## Comment 19

> Username: djowel  
> Created_at: 2016-09-01 22:55:50 UTC  
> Url: https://github.com/boostorg/spirit/pull/219#issuecomment-244237050  

There ya go. Now it's time for a bug hunt. Come join me :-)

---

## Comment 20

> Username: peterhuene  
> Created_at: 2016-09-01 22:57:55 UTC  
> Updated_at: 2016-09-01 22:59:10 UTC  
> Url: https://github.com/boostorg/spirit/pull/219#issuecomment-244237441  

I will once I wrap my head around all this metaprogramming :)  
  
Same workaround as before applies here: `x3::attr("foo") > (x3::eps > x3::attr("bar"))` compiles and produces the expected output in 1.61.0.

---

## Comment 21

> Username: djowel  
> Created_at: 2016-09-01 22:59:54 UTC  
> Url: https://github.com/boostorg/spirit/pull/219#issuecomment-244237786  

Smells like a partitioning bug alright, but we have to make _both_ use cases work.

---

## Comment 22

> Username: Kojoley  
> Created_at: 2017-12-19 22:25:45 UTC  
> Url: https://github.com/boostorg/spirit/pull/219#issuecomment-352905393  

@peterhuene #337 should solve the problem.  
  
@djowel are you sure that a8e391b was a right thing? You did not add tests for it and I can't wrap my head to come up with. IIUC what it does was already implemented by these specializations https://github.com/boostorg/spirit/blob/f5179c95bb6db23227f989558ffc7ad605a4b2a6/include/boost/spirit/home/x3/operator/detail/sequence.hpp#L120-L131  
  
I think currently it works by accident and did not break the whole thing because of this https://github.com/boostorg/spirit/blob/f5179c95bb6db23227f989558ffc7ad605a4b2a6/include/boost/spirit/home/x3/support/traits/move_to.hpp#L101-L108

---

## Comment 23

> Username: djowel  
> Created_at: 2017-12-19 23:01:03 UTC  
> Url: https://github.com/boostorg/spirit/pull/219#issuecomment-352913115  

@Kojoley It's hard to recall the essence of that code. Does this thread's test pass with that reverted? And all the tests for that matter?

---

## Comment 24

> Username: Kojoley  
> Created_at: 2017-12-19 23:05:53 UTC  
> Url: https://github.com/boostorg/spirit/pull/219#issuecomment-352914060  

> Does this thread's test pass with that reverted?  
  
Yes.  
  
> And all the tests for that matter?  
  
Let's see. I close-opened to trigger the CI.

---

## Comment 25

> Username: djowel  
> Created_at: 2017-12-19 23:08:25 UTC  
> Url: https://github.com/boostorg/spirit/pull/219#issuecomment-352914595  

OK, if that's the case, then we can revert. I might have an idea how to track the issue behind that commit after you revert.

---

## Comment 26

> Username: Kojoley  
> Created_at: 2017-12-20 23:52:03 UTC  
> Url: https://github.com/boostorg/spirit/pull/219#issuecomment-353216654  

I though that I might should leave here a description of how that change broke the things.  
  
The commit introduced a next behavior: sequence parser will not unwrapping a single size container if one of attribute types is unused. So underlying parser now always get a fusion sequence.   
  
Example: the sequence `attr(123) >> eps >> attr(456)` instead of passing the first `int` attribute to underlying parser currently passes `boost::fusion::iterator_range<struct boost::fusion::vector_iterator<struct boost::fusion::vector<int,int>,0>,struct boost::fusion::vector_iterator<struct boost::fusion::vector<int,int>,1> >` (the second attribute is of `int` type because it was unwrapped in split phase where attribute pass through is not forced).  
Ridiculous thing, but it works for ints because `move_to` have a specialization for unwrapping single item tuples, which will unwrap that single item fusion sequence (fusion::iterator_range of size 1). https://github.com/boostorg/spirit/blob/f5179c95bb6db23227f989558ffc7ad605a4b2a6/include/boost/spirit/home/x3/support/traits/move_to.hpp#L101-L108   
  
String parsers (or `attr(string_var)`) use other `move_to` signature that was lacking the same specialization (I had added it in #337 and it workarounds the problem of the thread).  
  
My point of view: the behavior should be consistent, either it must never unwrap a single item sequence in all the cases or always (there is an exception, if underlying is a sequence parser, but that case is already handled in the code).

---

## Comment 27

> Username: djowel  
> Created_at: 2017-12-20 23:58:38 UTC  
> Url: https://github.com/boostorg/spirit/pull/219#issuecomment-353217648  

👍 I'm all for consistency. Whether to always or never unwrap, I am not sure.  
  
Thanks for looking deeper into this, @Kojoley.

---

## Comment 28

> Username: Kojoley  
> Created_at: 2017-12-21 00:14:15 UTC  
> Updated_at: 2017-12-21 15:48:31 UTC  
> Url: https://github.com/boostorg/spirit/pull/219#issuecomment-353220232  

I see a benefit of not unwrapping for a single item fusion sequence attribute. But I'm not sure about fusion magic, will it move from a single size iterator to a single size sequence? (the iterator that actually wraps a generated sequence of size 1)  
  
Also: This specialization seems odd to me:  
https://github.com/boostorg/spirit/blob/f5179c95bb6db23227f989558ffc7ad605a4b2a6/include/boost/spirit/home/x3/support/traits/move_to.hpp#L90-L99

---

## Comment 29

> Username: djowel  
> Created_at: 2017-12-21 01:09:45 UTC  
> Url: https://github.com/boostorg/spirit/pull/219#issuecomment-353228640  

I think I agree with you about not unwrapping. We can always deal with Fusion if needed. Let's do it and see how it goes. I think it's best to work on this in a special branch.

---

## Comment 30

> Username: Kojoley  
> Created_at: 2017-12-22 00:19:11 UTC  
> Updated_at: 2017-12-22 12:05:06 UTC  
> Url: https://github.com/boostorg/spirit/pull/219#issuecomment-353490589  

I did not say I am for not unwrapping, but I am inclined to it.  
  
My argument above is mostly is invalid, I was thinking that x3 like qi wraps attribute to fusion vector (I though I saw the same part in x3) but it is not, so sequence parser will never generate a single item sequence attribute (except `x3::eps >> x3::attr(boost::fusion::vector<int>{123})`)  
  
Anyway current behavior is something I am totally do not support. It is strange to see an attribute of type `boost::fusion::iterator_range<struct boost::fusion::vector_iterator<struct boost::fusion::vector<int,int>,0>,struct boost::fusion::vector_iterator<struct boost::fusion::vector<int,int>,1> >` in attribute-generating parsers. I think a8e391b should be reverted + instead of unwrapping a single item sequence it should deference a single item fusion iterator.

---

## Comment 31

> Username: djowel  
> Created_at: 2017-12-22 05:01:34 UTC  
> Url: https://github.com/boostorg/spirit/pull/219#issuecomment-353522403  

OK, seems tests are good. Let us proceed.

---

## Comment 32

> Username: Kojoley  
> Created_at: 2017-12-29 23:50:57 UTC  
> Url: https://github.com/boostorg/spirit/pull/219#issuecomment-354513807  

Fixed in #340

---
