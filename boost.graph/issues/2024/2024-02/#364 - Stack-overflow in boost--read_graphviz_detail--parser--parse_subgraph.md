# #364 - Stack-overflow in boost::read_graphviz_detail::parser::parse_subgraph [Closed]

> Username: jeremy-murphy  
> Created at: 2024-02-18 23:00:17 UTC  
> Updated at: 2024-05-20 06:11:09 UTC  
> Closed at: 2024-05-10 06:09:03 UTC  
> Url: https://github.com/boostorg/graph/issues/364  

https://bugs.chromium.org/p/oss-fuzz/issues/detail?id=66719  
  
```  
+----------------------------------------Release Build Stacktrace----------------------------------------+  
Command: /mnt/scratch0/clusterfuzz/resources/platform/linux/unshare -c -n /mnt/scratch0/clusterfuzz/bot/builds/clusterfuzz-builds_boost_fa513528a55ae19c064fe12b99e08e0dbc98b83f/revisions/boost_graph_graphviz_fuzzer -rss_limit_mb=2560 -timeout=60 -runs=100 /mnt/scratch0/clusterfuzz/bot/inputs/fuzzer-testcases/crash-ec3a74d351f80590a35c0c6bace0c3a2fd548235  
Time ran: 0.5343647003173828  
INFO: Running with entropic power schedule (0xFF, 100).  
INFO: Seed: 49547342  
INFO: Loaded 1 modules   (10110 inline 8-bit counters): 10110 [0x7acd98, 0x7af516),  
INFO: Loaded 1 PC tables (10110 PCs): 10110 [0x70bd78,0x733558),  
/mnt/scratch0/clusterfuzz/bot/builds/clusterfuzz-builds_boost_fa513528a55ae19c064fe12b99e08e0dbc98b83f/revisions/boost_graph_graphviz_fuzzer: Running 1 inputs 100 time(s) each.  
Running: /mnt/scratch0/clusterfuzz/bot/inputs/fuzzer-testcases/crash-ec3a74d351f80590a35c0c6bace0c3a2fd548235  
AddressSanitizer:DEADLYSIGNAL  
=================================================================  
==38477==ERROR: AddressSanitizer: stack-overflow on address 0x7fff2c367df8 (pc 0x0000004c8a11 bp 0x7fff2c368640 sp 0x7fff2c367e00 T0)  
SCARINESS: 10 (stack-overflow)  
    #0 0x4c8a11 in __interceptor_memcpy /src/llvm-project/compiler-rt/lib/sanitizer_common/sanitizer_common_interceptors.inc:827:5  
    #1 0x45602f in Set /src/llvm-project/compiler-rt/lib/fuzzer/FuzzerDictionary.h:32:5  
    #2 0x45602f in FixedWord /src/llvm-project/compiler-rt/lib/fuzzer/FuzzerDictionary.h:26:43  
    #3 0x45602f in fuzzer::TracePC::AddValueForMemcmp(void*, void const*, void const*, unsigned long, bool) /src/llvm-project/compiler-rt/lib/fuzzer/FuzzerTracePC.cpp:376:28  
    #4 0x4c9222 in MemcmpInterceptorCommon(void*, int (*)(void const*, void const*, unsigned long), void const*, void const*, unsigned long) /src/llvm-project/compiler-rt/lib/sanitizer_common/sanitizer_common_interceptors.inc:0  
    #5 0x4c929b in memcmp /src/llvm-project/compiler-rt/lib/sanitizer_common/sanitizer_common_interceptors.inc:892:10  
    #6 0x622e4c in compare /usr/local/include/c++/v1/__string:404:12  
    #7 0x622e4c in compare<std::__1::basic_string_view<char, std::__1::char_traits<char> > > /usr/local/include/c++/v1/string:3798:20  
    #8 0x622e4c in compare /usr/local/include/c++/v1/string:3814:12  
    #9 0x622e4c in operator<<char, std::__1::char_traits<char>, std::__1::allocator<char> > /usr/local/include/c++/v1/string:4041:18  
    #10 0x622e4c in operator() /usr/local/include/c++/v1/__functional/operations.h:487:21  
    #11 0x622e4c in operator() /usr/local/include/c++/v1/map:574:17  
    #12 0x622e4c in __lower_bound<std::__1::basic_string<char, std::__1::char_traits<char>, std::__1::allocator<char> > > /usr/local/include/c++/v1/__tree:2539:14  
    #13 0x622e4c in std::__1::__tree_iterator<std::__1::__value_type<std::__1::basic_string<char, std::__1::char_traits<char>, std::__1::allocator<char> >, boost::read_graphviz_detail::subgraph_info>, std::__1::__tree_node<std::__1::__value_type<std::__1::basic_string<char, std::__1::char_traits<char>, std::__1::allocator<char> >, boost::read_graphviz_detail::subgraph_info>, void*>*, long> std::__1::__tree<std::__1::__value_type<std::__1::basic_string<char, std::__1::char_traits<char>, std::__1::allocator<char> >, boost::read_graphviz_detail::subgraph_info>, std::__1::__map_value_compare<std::__1::basic_string<char, std::__1::char_traits<char>, std::__1::allocator<char> >, std::__1::__value_type<std::__1::basic_string<char, std::__1::char_traits<char>, std::__1::allocator<char> >, boost::read_graphviz_detail::subgraph_info>, std::__1::less<std::__1::basic_string<char, std::__1::char_traits<char>, std::__1::allocator<char> > >, true>, std::__1::allocator<std::__1::__value_type<std::__1::basic_string<char, std::__1::char_traits<char>, std::__1::allocator<char> >, boost::read_graphviz_detail::subgraph_info> > >::find<std::__1::basic_string<char, std::__1::char_traits<char>, std::__1::allocator<char> > >(std::__1::basic_string<char, std::__1::char_traits<char>, std::__1::allocator<char> > const&) /usr/local/include/c++/v1/__tree:2468:20  
    #14 0x61b235 in find /usr/local/include/c++/v1/map:1441:68  
    #15 0x61b235 in boost::read_graphviz_detail::parser::parse_subgraph(boost::read_graphviz_detail::token const&) [boost/libs/graph/src/read_graphviz_new.cpp:787](https://github.com/boostorg/boost/blob/14203c864989f6e302be87b85ad7cb0df5d97ef4/libs/graph/src/read_graphviz_new.cpp#L787):27  
    #16 0x614b52 in boost::read_graphviz_detail::parser::parse_endpoint_rest(boost::read_graphviz_detail::token const&) [boost/libs/graph/src/read_graphviz_new.cpp:764](https://github.com/boostorg/boost/blob/14203c864989f6e302be87b85ad7cb0df5d97ef4/libs/graph/src/read_graphviz_new.cpp#L764):48  
    #17 0x6123d0 in boost::read_graphviz_detail::parser::parse_stmt() [boost/libs/graph/src/read_graphviz_new.cpp:677](https://github.com/boostorg/boost/blob/14203c864989f6e302be87b85ad7cb0df5d97ef4/libs/graph/src/read_graphviz_new.cpp#L677):40  
    #18 0x5e2d54 in boost::read_graphviz_detail::parser::parse_stmt_list() [boost/libs/graph/src/read_graphviz_new.cpp:646](https://github.com/boostorg/boost/blob/14203c864989f6e302be87b85ad7cb0df5d97ef4/libs/graph/src/read_graphviz_new.cpp#L646):17  
```  
The last four lines repeat in a cycle until the stack overflows.

---

## Comment 1

> Username: sehe  
> Created at: 2024-04-15 02:39:32 UTC  
> Url: https://github.com/boostorg/graph/issues/364#issuecomment-2054482946  

Without a reproducer I had to guess, but likely they test something like  
  
```c++  
#include <boost/graph/adjacency_list.hpp>  
#include <boost/graph/graphviz.hpp>  
int main() {  
    boost::adjacency_list<> g;  
    boost::dynamic_properties dp(boost::ignore_other_properties);  
    read_graphviz(std::cin, g, dp);  
}  
```  
  
With input like:  
  
```  
echo "strict digraph { 1 -> { $(printf '%1.1s' \{{1..1000000}) { 2; 3; } }" | ./a.out  
```  
  
Which does exhibit the stackoverflow:  
  
![image](https://github.com/boostorg/graph/assets/324097/4bca44a6-974d-4b47-bf0d-ffb67a276dc6)  
  
## Analysis  
  
First, let's observe that subgraph parsing is fundamentally broken.   
  
 - `digraph { 1 -> { 2 } }` is not accepted as it should. Instead `digraph { 1 -> { { 2 } }` is **erronously** accepted  
 - adding more `{` makes it increasingly erratic (eating the `2` token, among other things)  
 - adding insane amounts of `{` (on my system, adding 3872 extra { tokens saturates the stack in a debug build, 5514 in a release build)  
  
Now, this state of affairs is somewhat "known", in that many aspects of graphviz reading have been broken for a while. See this commented block of code https://github.com/boostorg/graph/blob/develop/include/boost/graph/graphviz.hpp#L452-L491   
  
In my StackOverflow answers I have, on more than one occasion, chosen to write a whole custom Graphviz parser to work around things:  
  
 - https://stackoverflow.com/questions/70970545/how-to-read-a-graph-given-in-a-dot-file-containing-subgraphs-with-boostread-gr/70979559#70979559   
 - https://stackoverflow.com/questions/75616068/boost-graphviz-read-all-properties/75621656#75621656  
  
Somewhere along the way I studied the graphviz grammar exhaustively and wrote a ditto Spirit parser to accomodate all the grammar¹  https://github.com/sehe/spirit-graphviz. My main take-away would be mostly that graphviz grammar is "okayish" but its semantics are incredibly idiosyncratic and basically "black box" - only the reference implementation `libcgraph` is a source of truth there.  
  
Why do I tell you this?  
  
## Solution Direction  
  
BGL doesn't document the subset of graphviz grammar they promise/wish to support. What's more, they do not document which semantics they give the result of a succesful parse. Add to this the mildly surprising fact that even the simplest of subgraphs does not parse correctly with current mainline I want to  
  
 - work out whether subgraphs were ever supported and/or working (if so, which variations: named, unnamed, in-place endpoint definitions and so on)  
 - whether the once-working feature(?) could be salvaged  
  
If the answer to the above is overwhelmingly negative, I'd suggest to scrap the feature unless we can find sponsor to fix the implementation. It will be crucial to come up with the minimal useful set of grammar and semantics to support, because as sketched out above the graphviz feature set is vast and intransparent. Good thing is, likely nobody uses that?  
  
## Risk Assessment  
  
The risk only applies to applications that allow BGL to parse graphviz input from untrusted sources. I would assume that the number of applications that this applies to is small, and likely to be found in academia or other research environments, where the resulting crash would not disrupt critical services. The state of the parser features makes it ostensibly hard to rely on for production code, which hopefully further reduces the number of applications exposed.  
  
I wouldn't go as far as to say that the risk is "low". To me it makes it a "medium" risk. I'd certainly discourage use of the read_graphviz feature in production code until mitigations are in place.  
  
## Mitigation  
  
 - Document the subset of graphviz grammar that is supported  
 - Document the semantics of the resulting graph  
 - Document the limitations of the parser  
 - Drop code related to non-supported features   
 - Document the known bugs and limitations of the parser  
  
In any other context I'd summarize it as regular "paying off technical debt" (perhaps more like "cutting losses" in his context?)  
  
¹ (except treating HTML attributes)

---

## Comment 2

> Username: jeremy-murphy  
> Created at: 2024-04-17 00:26:56 UTC  
> Url: https://github.com/boostorg/graph/issues/364#issuecomment-2060112683  

I note that you did not include replacing the existing implementation with your Spirit-based parser; why not? If there are existing unit tests for `read_graphviz` then I would be happy to consider that path.  
  
Otherwise, I would prefer to remove the broken code so that we get reports of missing features rather than reports of security vulnerabilities and crashing programs.

---

## Comment 3

> Username: jeremy-murphy  
> Created at: 2024-04-17 00:27:40 UTC  
> Url: https://github.com/boostorg/graph/issues/364#issuecomment-2060113225  

And btw, thank you for the excellent and timely report.

---

## Comment 4

> Username: sehe  
> Created at: 2024-04-19 16:05:31 UTC  
> Url: https://github.com/boostorg/graph/issues/364#issuecomment-2066873571  

So... Apart from being a bit sick, I spent inordinate amounts of time to try to  
answer the first question:  
  
> - work out whether subgraphs were ever supported and/or working (if so, which  
>   variations: named, unnamed, in-place endpoint definitions and so on)  
  
It being almost intractable due to all things changing since 1.40.0. I ended up  
doing a dirty trick by substituting ONLY `src/read_graphviz_new.cpp` to each  
older version, which revealed that no version of that ever accepted inline sub  
graphs without the keyword:  
[new-parser.zip](https://github.com/boostorg/graph/files/15042709/new-parser.zip)  
  
```  
     14 Failed: digraph { 1 -> {} }  
     14 Failed: digraph { 1 -> {2} }  
     14 Failed: digraph { 1; { 2; 3; } }  
     14 Passed: digraph {}  
     14 Passed: digraph { 1 -> subgraph { 2; 3; } }  
     14 Passed: digraph { 1; subgraph { 2; 3; } }  
     14 Passed: digraph { 1 -> subgraph clust_Hello { 2; 3; } }  
     14 Passed: digraph { 1 -> subgraph "hello" { 2; 3; } }  
     14 Passed: digraph { 1 -> subgraph hello { 2; 3; } }  
```  
  
14 is the number of revisions between 1.40.0 and now. Ironically (?) when defining   
  
    #define BOOST_GRAPH_USE_SPIRIT_PARSER  
  
They all passed in all revisions...: [old-spirit.zip](https://github.com/boostorg/graph/files/15042704/old-spirit.zip)  
  
  
```  
     14 Passed: digraph {}  
     14 Passed: digraph { 1 -> {} }  
     14 Passed: digraph { 1 -> {2} }  
     14 Passed: digraph { 1; { 2; 3; } }  
     14 Passed: digraph { 1 -> subgraph { 2; 3; } }  
     14 Passed: digraph { 1; subgraph { 2; 3; } }  
     14 Passed: digraph { 1 -> subgraph clust_Hello { 2; 3; } }  
     14 Passed: digraph { 1 -> subgraph "hello" { 2; 3; } }  
     14 Passed: digraph { 1 -> subgraph hello { 2; 3; } }  
```  
  
So the good news is, nothing seems to have regressed. The only people RELYING  
on the keywordless subgraph feature of graphviz MUST have been using  
`BOOST_GRAPH_USE_SPIRIT_PARSER`.  
  
All this is starting to beg the question why the parser was replaced. I'd  
predict it was "for efficiency" or "for compilation time". Going by the tests,  
subgraphs aren't considered a feature, full-stop (because not a single  
`read_graphviz` test exercises that). There's a pretty big irony gap between  
that and the very first commit on it:  
  
```git  
commit 8dd497a9f10ca9d2da483d171fde2573d6896987  
Author: Jeremiah Willcock <jewillco@osl.iu.edu>  
Date:   Mon May 25 05:38:56 2009 +0000  
  
    Changed new GraphViz parser to be less generic (so it can be built as a  
    binary); fixed subgraph issues by doing a lot of tests on GraphViz itself  
    and restructuring a lot of the parser; made docs only point to new parser  
    and made old one not build by default (although it is not removed)  
      
    [SVN r53237]  
```  
  
I have not delved into issue history to see the "why's" but if you're so inclined, I imagine there will be something relevant around the time of this commit:  
  
```git  
commit 1ba64f82ec5c164c68f68cc1a9d7e60eeea57006  
Author: Jeremiah Willcock <jewillco@osl.iu.edu>  
Date:   Mon Mar 22 19:16:38 2010 +0000  
  
    Re-enabled Spirit-based parser (but not by default) and cleaned up Graphviz reader implementation  
      
    [SVN r60770]  
```  
  
## Side-Topic: Why Not Replace It Again?  
  
Re: "Why don't we use your Spirit implementation":   
  
 - we can invert the reasons I imagined above: it will likely not be as  
   efficient as the current one, and it will certainly increase compilation  
   times.  
  
   The latter seems to me to be a non-issue. But the former is real: I  
   made my parser to adhere as closely as possible to Graphviz semantics, not  
   to efficiently build BGL graph models.  
 - the big-picture is that [Hyrum's Law always applies](https://www.hyrumslaw.com/#). Even if we replace the  
   parser with one that is semantically more correct, without a doubt it will  
   break someone's code that relied on the old behaviour.  
  
   > - As a random point, it appears that `read_graphviz_new` has *some* level of  
   support for HTML id's that I already mentioned my parser omits.  
  
Simply put, I don't want to push my pet project. To me it looks that this is  
how we got in this place to begin with.  
  
## Next Steps  
  
Now that we've found that keyword-less subgraphs never worked, and people  
relying on it MUST have been using `BOOST_GRAPH_USE_SPIRIT_PARSER`, we have two  
options to remove the vulnerability:  
  
 - fix keywordless subgraphs in the new parser  
 - dropping support keywordless subgraphs in the new parser

---

## Comment 5

> Username: sehe  
> Created at: 2024-04-20 21:52:42 UTC  
> Updated at: 2024-04-20 21:55:40 UTC  
> Url: https://github.com/boostorg/graph/issues/364#issuecomment-2067790673  

In case you want you can follow my work/progress on here https://github.com/sehe/graph/tree/sehe-364 - assuming you agree with my plans unless you object here :)   
  
(and thank you for contributing 8dd8cd945e1b341 the .clang-format; it really puts my mind at ease trying to figure out the law of the land!)

---

## Comment 6

> Username: sehe  
> Created at: 2024-04-20 22:36:11 UTC  
> Url: https://github.com/boostorg/graph/issues/364#issuecomment-2067805031  

Oh look what I found spelunking the history at 8dd497a9  
  
![image](https://github.com/boostorg/graph/assets/324097/bc92e6e2-6ad5-40e7-a197-e4642d99865e)

---

## Comment 7

> Username: jeremy-murphy  
> Created at: 2024-04-20 23:33:41 UTC  
> Url: https://github.com/boostorg/graph/issues/364#issuecomment-2067814170  

Thanks for your work. I'm in the mountains this weekend, so little time to look. My feeling is to drop support for keyword-less subgraphs first, to fix the bad behaviour? Then we can decide what to do next at leisure.

---

## Comment 8

> Username: jeremy-murphy  
> Created at: 2024-04-23 23:52:29 UTC  
> Url: https://github.com/boostorg/graph/issues/364#issuecomment-2073673143  

> Thanks for your work. I'm in the mountains this weekend, so little time to look. My feeling is to drop support for keyword-less subgraphs first, to fix the bad behaviour? Then we can decide what to do next at leisure.  
  
On second thought, if the new parser works _with_ the subgraph keyword, then it might not be any more difficult to make it work for implicit subgraphs too than it is to remove the support altogether.  
  
I had a quick look at your working branch and just want to make one request: please keep refactoring changes and behavioural changes in different pull requests. I value both, but they don't belong together.

---

## Comment 9

> Username: sehe  
> Created at: 2024-04-24 00:41:29 UTC  
> Url: https://github.com/boostorg/graph/issues/364#issuecomment-2073729874  

On Wed, Apr 24, 2024, at 1:52 AM, Jeremy W. Murphy wrote:  
> On second thought, if the new parser works *with* the subgraph keyword, then it might not be any more difficult to make it work for implicit subgraphs too than it is to remove the support altogether.  
>   
  
You absolutely read my mind! I was going to say the same thing, but probably only after I found the fix, because I can be a bit pig-headed like that.  
  
> I had a quick look at your working branch and just want to make one request: please keep refactoring changes and behavioural changes in different pull requests. I value both, but they don't belong together.  
>   
That's the reason it's not a PR. I admit naming the branch that can raise the wrong expectation :) In all honesty, the whole unit test refactor might be the "[build] one to throw away". Oh, and of course we'll need a separate PR for the actual fix. You know, once the parser does what it was supposed to, we **need** to have limits - because even if you remove the recursion (just iterative descent parser, I guess? lol) you will still want to guard against malicious payloads eating up precious resources.  
  
Thanks for chiming in with the comment, and I'll show my work when I reach a next step!  
  
>   
> —  
> Reply to this email directly, view it on GitHub <https://github.com/boostorg/graph/issues/364#issuecomment-2073673143>, or unsubscribe <https://github.com/notifications/unsubscribe-auth/AACPEALNHSHXBLTO6ETBTLLY63X5DAVCNFSM6AAAAABDOQHUJGVHI2DSMVQWIX3LMV43OSLTON2WKQ3PNVWWK3TUHMZDANZTGY3TGMJUGM>.  
> You are receiving this because you commented.Message ID: ***@***.***>  
>

---

## Comment 10

> Username: sehe  
> Created at: 2024-04-24 00:43:41 UTC  
> Url: https://github.com/boostorg/graph/issues/364#issuecomment-2073735164  

PS. I'll create a new issue for the CSR graph problem as well, but no hurry I guess. It's not a defect [the docs don't actually promise validity of the bundle propertymaps after graph mutation and people with an understanding of CSR models will probably intuit the (lack of) guarantees, much like I did]. I consider this a QoL side-catch if I find a way to improve it beyond the current kludge to fix the unit test.

---

## Comment 11

> Username: jeremy-murphy  
> Created at: 2024-04-28 07:52:22 UTC  
> Url: https://github.com/boostorg/graph/issues/364#issuecomment-2081378708  

Wait, are we both missing an obvious temporary solution to the security problem, which is to simply switch the default implementation back to the original Spirit-based one?

---

## Comment 12

> Username: sehe  
> Created at: 2024-04-28 10:18:11 UTC  
> Updated at: 2024-04-29 00:09:45 UTC  
> Url: https://github.com/boostorg/graph/issues/364#issuecomment-2081423699  

On Sun, Apr 28, 2024, at 9:52 AM, Jeremy W. Murphy wrote:  
>   
>   
> Wait, are we both missing an obvious temporary solution to the security problem, which is to simply switch the default implementation back to the original Spirit-based one?  
>   
>   
That would potentially break everyone. The feature sets are not the same.   
  
Side question: what is the official c++03 support status for BGL these days?

---

## Comment 13

> Username: jeremy-murphy  
> Created at: 2024-04-29 00:07:05 UTC  
> Url: https://github.com/boostorg/graph/issues/364#issuecomment-2081709213  

Oh, OK.  
  
C++03: Not supported. Thanks for reminding me to update the metadata.

---

## Comment 14

> Username: sehe  
> Created at: 2024-04-29 00:11:40 UTC  
> Url: https://github.com/boostorg/graph/issues/364#issuecomment-2081711162  

> C++03: Not supported. Thanks for reminding me to update the metadata.  
>   
Great. That simplifies and might actually give me some reason to consider contributing parser features currently missing (in the further future).  
  
(Oh and sorry for the mess of typo's. I type my previous reply on a phone and I did not notice how badly that went :|)

---

## Comment 15

> Username: sehe  
> Created at: 2024-05-02 03:38:00 UTC  
> Updated at: 2024-05-02 03:45:54 UTC  
> Url: https://github.com/boostorg/graph/issues/364#issuecomment-2089472838  

The fix is in: https://github.com/sehe/graph/commit/1636d00562fe10bd97e8ff64d2e7c3ded900ec04  
  
Note that the other commits contain the accompanying tests. I made them on top of the refactored `graphviz_test.cpp`. I'm not particularly motivated to rewrite the tests without the refactoring, so I'm hoping you're interested in the refactored test source as well.  
  
If that's okay, I'll PR it  
  
PS. I still have to make a general recursion limiter of course, to actually fix the current issue, let's not forget about that

---

## Comment 16

> Username: sehe  
> Created at: 2024-05-02 04:07:13 UTC  
> Url: https://github.com/boostorg/graph/issues/364#issuecomment-2089493302  

A simple sketch of how a `max_subgraph_nesting_level` would look is in my latest commit: https://github.com/sehe/graph/commit/92ee0c4bbacf44deb7ffe48c42eef3d9c53cc9cc  
  
I'm not a big fan of making the cut-off point configurable. After all, if you bake it into the compiled library using a preprocessor define, you effectively defer the value to some distribution packager, which might lead to programs having different limits depending on the system they run on, or even breaking programs when the packaged library changes their configured limit.  
  
On the other hand making the limit fully dynamic risks reintroducing the attack vector when programs don't clamp the value to some safe range. I'm open to your suggestions here.

---

## Comment 17

> Username: jeremy-murphy  
> Created at: 2024-05-02 20:20:46 UTC  
> Url: https://github.com/boostorg/graph/issues/364#issuecomment-2091488678  

Wonderful, thank you. Looks fine exactly as is. I do prefer making things configurable, but I'm fine with making it hard-coded on a first pass.  
Refactored tests are fine, but yeah, I would just really prefer a PR for refactoring and a PR for new features/tests/changes.

---

## Comment 18

> Username: sehe  
> Created at: 2024-05-02 20:41:07 UTC  
> Url: https://github.com/boostorg/graph/issues/364#issuecomment-2091517946  

> Refactored tests are fine, but yeah, I would just really prefer a PR for refactoring and a PR for new features/tests/changes  
  
Oh that's easily done. There will just be a dependency because the new tests use the refactored driver. I'll go make some PRs and also file the issue analysis on the CSR problem that i worked around in the previously disabled test.

---

## Comment 19

> Username: sehe  
> Created at: 2024-05-03 03:39:19 UTC  
> Url: https://github.com/boostorg/graph/issues/364#issuecomment-2092068100  

I broke it down into these PRs (in order):  
  
 - #374   
 - #375   
 - #376

---

## Comment 20

> Username: jeremy-murphy  
> Created at: 2024-05-08 02:55:18 UTC  
> Url: https://github.com/boostorg/graph/issues/364#issuecomment-2099644552  

Thank you and apologies for not responding sooner.  
  
I would prefer that, instead of leaving `#ifdef` blocks with your name attached to them, create a GitHub issue for anything that you want to remember to address in the future. The code gets messy really quickly if everyone leaves their thoughts behind about what could be or should be. And it might be someone else entirely that actually actions it in the future.  
  
With regard to `<boost/regex.hpp>`, if it appears to be redundant, then just delete it and see what the CI system says.

---

## Comment 21

> Username: jeremy-murphy  
> Created at: 2024-05-08 03:00:25 UTC  
> Url: https://github.com/boostorg/graph/issues/364#issuecomment-2099650418  

I'll make some of these basic changes in situ now so that I can merge them, just in case you get held up. So you'll have to pull these branches before pushing again (if you need to).

---

## Comment 22

> Username: sehe  
> Created at: 2024-05-08 12:49:56 UTC  
> Updated at: 2024-05-08 12:50:52 UTC  
> Url: https://github.com/boostorg/graph/issues/364#issuecomment-2100506381  

> With regard to `<boost/regex.hpp>`, if it appears to be redundant, then just delete it and see what the CI system says.  
  
The comment actually applied to the entire block of includes following that comment.  
  
> instead of leaving #ifdef blocks with your name attached to the  
  
I agree about the username. The comment actually links to the issue that I *did* already create? I vote to keep warning block of "naive" code, and my commit message already gave the rationale for the importance.  
  
I'll address these. Removing unused headers belongs with the "refactoring graphviz_test.cpp" PR, so I'll do it there.  
I've also updated the dependent PR's because they're logically in sequence.

---

## Comment 23

> Username: jeremy-murphy  
> Created at: 2024-05-20 06:11:09 UTC  
> Url: https://github.com/boostorg/graph/issues/364#issuecomment-2119737904  

Sorry for the delay, I've been so busy at work, but I just wanted to say a huge THANK YOU again to you @sehe for your fantastic work.  
  
The fix will go into `master` when I merge this one: https://github.com/boostorg/graph/pull/377
