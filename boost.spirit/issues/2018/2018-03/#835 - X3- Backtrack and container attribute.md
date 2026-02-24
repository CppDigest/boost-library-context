# #835 - X3: Backtrack and container attribute [Open]

> Username: Kojoley  
> Created at: 2018-03-16 16:54:35 UTC  
> Updated at: 2025-09-27 11:05:19 UTC  
> Url: https://github.com/boostorg/spirit/issues/835  

## Preface  
  
The side effect of backtracking on container type attributes is a known Spirit problem. It is not only a very surprising thing for newcomers, but also the sword of Damocles to everyone.  
  
<details>  
 <summary>Click for the backtrack side effect example</summary>  
  
```cpp  
#include <boost/detail/lightweight_test.hpp>  
#include <boost/spirit/home/x3.hpp>  
#include <string>  
#include "../spirit/test/x3/test.hpp"  
  
int main()  
{  
    using spirit_test::test_attr;  
    namespace x3 = boost::spirit::x3;  
  
    {  
        std::string s;  
        BOOST_TEST(test_attr("ac", x3::char_("a") >> x3::char_("b") | x3::string("ac"), s));  
        BOOST_TEST_EQ("ac", s);  
    }  
  
    return boost::report_errors();  
}  
```  
  
</details>  
  
## Solutions  
  
The two main suggestions for `Qi` users are:  
  1. Rewrite your parser to eliminate backtracking, or if it is impossible:  
  2. Use `hold` directive.  
  
At the current moment there is no `hold` directive in `X3` and that's a problem. The simple solution is to introduce `hold` directive and forget about the problem, however I think it is not a solution, but a dirty hack/workaround.  
  
What I have in my mind right now:  
  
  no | description | pseudo representation  
  -- | ----------- | ---------------------  
  1  | `hold` directive. | <code>hold[a] &#124; hold[b] &#124; hold[c] &#124; ... &#124; hold[z]</code>  
  2  | `parse_alternative` to temporary. | Same as 1  
  3  | `alternative::parse` to temporary. | <code>hold...[hold[hold[a &#124; b] &#124; c] &#124; ...] &#124; z</code>  
  4  | clear the attribute before `parse_alternative` call. | <code>clear >> a &#124; clear >> b &#124; ... &#124; clear >> z</code>  
  5  | clear the attribute after `parse_alternative` fail. | <code>a &#124; clear &#124; b &#124; clear &#124; ... &#124; z &#124; clear</code>  
  6  | clear between `parse_alternative` calls in `alternative::parse`. | <code>a &#124; clear >> b &#124; ... &#124; clear >> z</code>  
  
  
What I think about solutions above:  
  1. Easy to implement. User should know about its existence. Error prone or terrible looking because of verbosity if used in every branch. The cost is `N` construct + `N - F` move + `N` destruct calls, where `N` is number of hit `hold` directives, `F` is number of failed branches.  
  2. Easy to implement. The cost is `S` construct + `S - F` move + `S` destruct calls, where `S` is number of succeeded branches, `F` is number of failed branches.  
  3. The cost is `S - 1` construct + `S - F` move + `S - 1` destruct calls, where `S` is number of succeeded branches, `F` is number of failed branches.  
  4. Requires new trait. The cost is `S` clear trait calls, where `S` is number of succeeded branches.  
  5. Requires new trait. The cost is `F` clear trait calls, where `F` is number of failed branches.  
  6. Requires new trait. The cost is `F - 1` clear trait calls, where `F` is number of failed branches.  
  
Since all approaches have constant overhead after backtracking that had appended zero elements to the attribute the list above shows that `hold` directive has most performance impact, and being a 'non-automatic' solution makes it the worst decision by a large margin.  
  
Non-container attributes handling will not changed even on asm level for all the solutions (except may be `hold` directive, because `Qi` documentation explicitly states that it creates temporary and does not make exception for non-container attributes).  
  
**Note:** solutions 1-3 will make more allocate calls and perform worse on non reusing allocators, but 4-6 may end with more `reserved` memory (can be fixed with `shrink_to_fit` call, but I think it should be left up to a user to make such call).  
  
From the solutions list it is clear that:  
  - Solution 2 is just an automatic hold placement on every branch.  
  - Solution 3 is a waste of memory.  
  - Solutions 4-6 represent a single technique with various optimizations. The 4 is the slowest and 6 is the fastest. Solution 6 is more like 'zero cost exceptions' when you are paying only on failing.  
  
## Optimizations  
  
The main question is: can we make automatic backtrack recovering as optimized as the most optimized hand placed `hold` directive? Let's see:  
  
Solution 6 is already optimized to clear only between branches so it is equivalent (in terms of the result) of `hold[a] | hold[b] | hold[c] | ... | z`. It is nice, we do not need to clear the container if the last branch has failed.  
  
**Side note:** Actually, clear *is cheaper* than `hold`s in this case. Because it does the following `a | clear >> b | clear >> c | ... | z` there are no constructions and destructions of container type in every branch, but what most people may not realize -- memory allocations. The most used container is `std::vector`, it will call allocator on `push_back` due to growth (and may be even multiple allocations + moves or if you unclucky -- copies for its elements) on every branch (*at least* a single allocation on every branch that produces at least one element). This will not happen with solution 6 because we always work with the same container there are no moves/copies and the number of allocations will be as much as number of allocations for only the longest successful branch.  
  
The two possible manual `hold` optimization are:  
  1. `a | hold[b] | c` - hold is not used on `a` because it does not produce an attribute (example `a = lit("foo")`).  
      It is a simple optimization and will be performed automatically when `is_same<unused_type, attribute_of<decltype(a)>>`.  
  2. `lit("foo") >> hold[a] | b` - postpone `hold` so if `lit("foo")` fails there is no cost of `hold` at all.  
      For the most of containers `clear` call on an empty attribute should be very cheap so this optimization will have close to zero impact on performace.  
      However, it also can be done automatically by performing something like this transformation `lit("foo") >> (a | clear) | b`. It is a complicated optimization, but I have a clear picture of how it could be done by injecting a tag into a context.  
  
I can't say for sure there are no other possible optimizations, but it should be something very tricky.

---

## Comment 1

> Username: Kojoley  
> Created at: 2018-03-16 19:11:47 UTC  
> Updated at: 2018-03-16 19:30:25 UTC  
> Url: https://github.com/boostorg/spirit/issues/835#issuecomment-3341125414  

I do not know how `hold` was introduced into `Qi` and why that solution had been chosen. I feel like even `Qi` must lose `hold`, because it (even on C++11 I think) instead of moves performs copies. During deprecation period it will become no-op with a compile time warning of being removed in future.

---

## Comment 2

> Username: mjcaisse  
> Created at: 2018-03-16 21:41:14 UTC  
> Url: https://github.com/boostorg/spirit/issues/835#issuecomment-3341125416  

`Qi` can't loose `hold`. Please do not change `Qi`.

---

## Comment 3

> Username: Kojoley  
> Created at: 2018-03-16 21:48:11 UTC  
> Url: https://github.com/boostorg/spirit/issues/835#issuecomment-3341125417  

Since I did not even open a PR you may have misunderstood me. Or maybe you have a counterexample?

---

## Comment 4

> Username: djowel  
> Created at: 2018-03-16 22:15:23 UTC  
> Updated at: 2018-03-16 22:21:01 UTC  
> Url: https://github.com/boostorg/spirit/issues/835#issuecomment-3341125419  

Correct me if my understanding is wrong, but I think one difference between 'hold' and the alternative solutions is that with 'hold' you only pay for it when you use it. The others, you pay for the cost all the time. (edit: to be clear, i meant 2 and 3. not sure about the implications of 4, 5 and 6 yet)

---

## Comment 5

> Username: mjcaisse  
> Created at: 2018-03-16 22:38:06 UTC  
> Updated at: 2018-03-16 22:38:36 UTC  
> Url: https://github.com/boostorg/spirit/issues/835#issuecomment-3341125425  

Hi @Kojoley -  
  
Perhaps I misunderstood. You write:  
  
>  I feel like even Qi must lose hold, because it (even on C++11 I think) instead of moves performs copies. During deprecation period it will become no-op with a compile time warning of being removed in future.  
  
Which makes it sound like you are proposing that `hold` is also removed from `Qi`. That would be either a breaking change or a performance hit.  
  
I'm simply saying that `hold` should not be removed from `Qi` .  
  
As for `X3`, I'm not opposed to a `hold` directive. I personally like the explicit nature of imposing the cost.  
  
Suggestions 2 and 3 are expensive.  
  
I think boostorg/spirit#4 changes behaviour for existing code. This may be a breaking change for some people acting tricky with attribute synthesis.  
  
boostorg/spirit#5 May add some cost but might be negligible.  
  
boostorg/spirit#6 Has same behaviour changes as boostorg/spirit#4 I think.  
  
I wonder if there is a way to shift the annotation (such as `hold`) out of the grammar and into the attribute where it belongs. Like a wrapper for the attribute that indicates that it should be "reset" on failed parse (or something like that).

---

## Comment 6

> Username: Kojoley  
> Created at: 2018-03-16 22:39:50 UTC  
> Url: https://github.com/boostorg/spirit/issues/835#issuecomment-3341125426  

No, actually I have showed above that `hold` on general usage is way more expensive (even if we assume zero cost move) as it requires heap allocations. I will try to describe more precisely.  
  
On general usage `hold` not 'when you use it', because if it is not used on branch with container attribute - it is a bug in your parser.  
  
The two possible manual `hold` optimization:  
  1. `a | hold[b] | c` - we did not use hold on `a` because it does not produce an attribute (in short `a = eps`)  
  2. `lit("foo") >> hold[a] | b` - we postpone `hold` so if `lit("foo")` fails there is no cost of `hold` at all.  
  
Maybe I missed some other pros and then things may change, but currently what I see:  
  1. We will make this optimization automatically since we can test `is_same<unused_type, attribute_of<decltype(a)>>`  
  2. `clear` call on an empty attribute should very cheap. If someone really needs this kind of optimization we can leave `hold` and disable automatic `clear` call for this branch.

---

## Comment 7

> Username: djowel  
> Created at: 2018-03-16 22:46:20 UTC  
> Url: https://github.com/boostorg/spirit/issues/835#issuecomment-3341125428  

> I wonder if there is a way to shift the annotation (such as hold) out of the grammar and into the attribute where it belongs. Like a wrapper for the attribute that indicates that it should be "reset" on failed parse (or something like that).  
  
I liked that idea in essence, but I think the issue whether to 'hold' or not is a consequence of the grammar, not the attribute. A clean, non-backtracking grammar need not have to 'hold'.

---

## Comment 8

> Username: Kojoley  
> Created at: 2018-03-16 22:46:40 UTC  
> Url: https://github.com/boostorg/spirit/issues/835#issuecomment-3341125429  

> That would be a either a breaking change  
  
If it will remain a `no-op` or optimization guide it will not. Otherwise I have said a deprecation cycle so users will have time to migrate their code without a compilation errors.  
  
> or a performance hit.  
  
As I have shown above it will be performance gain.  
  
> Suggestions 2 and 3 are expensive.  
  
I just shown all the solutions I have in mind. What I really propose want to propose is 6. I do not see any further ways to optimize except the `hold` guide for partial match I have described in post above.  
  
> I wonder if there is a way to shift the annotation (such as hold) out of the grammar and into the attribute where it belongs. Like a wrapper for the attribute that indicates that it should be "reset" on failed parse (or something like that).  
  
It is complicated and I do not see examples where this will be better that solution 6. (I do not talk of downsides like `hold` has about its verbosity).

---

## Comment 9

> Username: Kojoley  
> Created at: 2018-03-16 22:47:58 UTC  
> Url: https://github.com/boostorg/spirit/issues/835#issuecomment-3341125430  

And you may also specialize empty trait so empty call on your type will be no-op.

---

## Comment 10

> Username: mjcaisse  
> Created at: 2018-03-16 22:54:29 UTC  
> Url: https://github.com/boostorg/spirit/issues/835#issuecomment-3341125434  

It is not a performance gain if you are not using `hold`. I think you are comparing always using `hold` to the new solutions. I've almost never used `hold`.

---

## Comment 11

> Username: djowel  
> Created at: 2018-03-16 23:00:08 UTC  
> Url: https://github.com/boostorg/spirit/issues/835#issuecomment-3341125435  

> I've almost never used hold.  
  
Me too. Whatever the solution is, we should not pay for something that we do not use.

---

## Comment 12

> Username: Kojoley  
> Created at: 2018-03-16 23:01:26 UTC  
> Updated at: 2018-03-16 23:02:47 UTC  
> Url: https://github.com/boostorg/spirit/issues/835#issuecomment-3341125439  

> I think you are comparing always using `hold` to the new solutions.  
  
You misunderstood me.   
The behavior of alternate parser for variant types and values of non-container types will be not changed. I have even wrote it in the OP:  
  
> Non-container attributes handling will not changed even on asm level for all the solutions  
  
  
> I've almost never used `hold`.  
  
It is expected, because `hold` is needed at places where the attribute is not of a variant, but a value of a container type.

---

## Comment 13

> Username: djowel  
> Created at: 2018-03-16 23:04:03 UTC  
> Url: https://github.com/boostorg/spirit/issues/835#issuecomment-3341125442  

> Non-container attributes handling will not changed even on asm level for all the solutions  
  
Interesting! Indeed, we might be misunderstanding. Could you expound a bit more?

---

## Comment 14

> Username: Kojoley  
> Created at: 2018-03-16 23:06:16 UTC  
> Updated at: 2018-03-16 23:10:07 UTC  
> Url: https://github.com/boostorg/spirit/issues/835#issuecomment-3341125444  

You simply do not need to `hold`/clear a non-container attribute because it will be rewritten in any other successful branch entirely. So call `empty_if_container` will become `no-op` on non-container attributes and will cost nothing for you. At compile time it will cost you an instantiation of templated function and `is_container` trait per unique attribute type (should be at max a few milliseconds).

---

## Comment 15

> Username: djowel  
> Created at: 2018-03-16 23:19:44 UTC  
> Url: https://github.com/boostorg/spirit/issues/835#issuecomment-3341125446  

> You simply do not need to hold/clear a non-container attribute because it will be rewritten in any other successful branch entirely.  
  
Not sure I understand. 'hold' is explicit. And typically, you do it with containers. If the user has 'hold' on non-container attributes, then he asked for it.

---

## Comment 16

> Username: Kojoley  
> Created at: 2018-03-16 23:26:12 UTC  
> Url: https://github.com/boostorg/spirit/issues/835#issuecomment-3341125448  

> Not sure I understand. 'hold' is explicit. And typically, you do it with containers. If the user has 'hold' on non-container attributes, then he asked for it.  
  
Yes, he may have asked this, but he actually may misused it. Without semantic actions it does not change parsing result at all. With semantic action it will produce the same result as by replacing it with a pair of parenthesis.

---

## Comment 17

> Username: Kojoley  
> Created at: 2018-03-16 23:54:23 UTC  
> Url: https://github.com/boostorg/spirit/issues/835#issuecomment-3341125453  

> Yes, he may have asked this, but he actually may misused it. Without semantic actions it does not change parsing result at all.  
  
This is also apply to a container types.  
  
> With semantic action it will produce the same result as by replacing it with a pair of parenthesis.  
  
This will change result on container types if user asked `pass = false` while using `hold` on last branch (like this `a | ... | hold[z]`, what is some really strange usage). It also may change behavior for semantic actions placed on `hold` directive that *reads* `_val` (again some strange usage, someone does something tricky here). But we still may make a runtime debug check for this during deprecation period if `Qi` is going to remove `hold`, but it is an optional thing.  
  
The main subject of the discussion is `X3`. The `Qi` currently for me is a ground of possible counterexamples and only if there are none of them I will propose a backport to `Qi`.

---

## Comment 18

> Username: djowel  
> Created at: 2018-03-17 00:06:04 UTC  
> Updated at: 2018-03-17 00:07:03 UTC  
> Url: https://github.com/boostorg/spirit/issues/835#issuecomment-3341125454  

> The main subject of the discussion is X3. The Qi currently for me is a ground of possible counterexamples and only if there are none of them I will propose a backport to Qi.  
  
Agreed. We should leave Qi as-is. Changing Qi's behavior now will cause havoc. Deprecation does not practically work for Boost, based on my experience. For example, Spirit "classic" has been deprecated for more than 10 years now, yet it persists because, people use it and boost libraries use it. removing it now will cause an uproar :-)  
  
I am 100% for a well tuned and optimized "hold" for X3. I am also open to other, possibly parallel solutions, as long as _you don't pay for it when you do not need it_ (one of the guiding principles of Spirit).

---

## Comment 19

> Username: Kojoley  
> Created at: 2018-03-17 00:19:17 UTC  
> Url: https://github.com/boostorg/spirit/issues/835#issuecomment-3341125458  

> For example, Spirit "classic" has been deprecated for more than 10 years now, yet it persists because, people use it and boost libraries use it. removing it now will cause an uproar :-)  
  
It does not produce a compiler warning. There is no mention of deprecation in the documentation, actually.  
People also are lazy, and will not touch things if they are working and producing satisfactory results. But it is not free for you, because you pay by maintaining it for them.  
  
> I am 100% for a well tuned and optimized "hold" for X3. I am also open to other, possibly parallel solutions, as long as you don't pay for it when you do not need it (one of the guiding principles of Spirit).  
  
That is core thing of C++, otherwise I would have used Haskell :-)

---

## Comment 20

> Username: djowel  
> Created at: 2018-03-17 00:28:34 UTC  
> Updated at: 2018-03-17 00:28:48 UTC  
> Url: https://github.com/boostorg/spirit/issues/835#issuecomment-3341125460  

> It does not produce a compiler warning. There is no mention of deprecation in the documentation, actually.  
> People also are lazy, and will not touch things if they are working and producing satisfactory results. But it is not free for you, because you pay by maintaining it for them.  
  
There was, actually. And it was documented very well. We had a warning going for years when it was deprecated. We removed it later, because we simply cannot remove "classic" and force people to switch to Qi.

---

## Comment 21

> Username: djowel  
> Created at: 2018-03-17 00:29:46 UTC  
> Url: https://github.com/boostorg/spirit/issues/835#issuecomment-3341125463  

> I am 100% for a well tuned and optimized "hold" for X3. I am also open to other, possibly parallel solutions, as long as you don't pay for it when you do not need it (one of the guiding principles of Spirit).  
>   
> That is core thing of C++, otherwise I would have used Haskell :-)  
  
👍

---

## Comment 22

> Username: Kojoley  
> Created at: 2018-03-17 15:02:16 UTC  
> Updated at: 2018-03-17 15:33:38 UTC  
> Url: https://github.com/boostorg/spirit/issues/835#issuecomment-3341125466  

Ok, it looks like `hold` manual optimization 2 could be done automatically, so I do not see not a single advantage of manual `hold` placing.  
  
Update to first post:  
  - Added pseudo representations to solutions  
  - Added optimizations section  
  
However, there is a thing that makes me wonder:  
  
```cpp  
#include <boost/detail/lightweight_test.hpp>  
#include <boost/spirit/home/qi.hpp>  
#include <string>  
#include "../spirit/test/qi/test.hpp"  
  
int main()  
{  
    using spirit_test::test_attr;  
    namespace qi = boost::spirit::qi;  
  
    {  
        std::string s = "z";  
        BOOST_TEST(test_attr("ac", qi::hold[qi::char_("a") >> qi::char_("b")] | qi::char_("a") >> qi::char_("c"), s));  
        BOOST_TEST_EQ("ac", s);  
    }  
  
    return boost::report_errors();  
}  
```  
```  
main.cpp(101): test '"ac" == s' failed in function 'int __cdecl main(void)': '000000013FBEAD64' != 'zac'  
1 error detected.  
```  
  
Why `hold` appends its items? From the documentation I see there was no such intention  
  
> It instantiates a new attribute instance for the embedded parser. The value of that attribute instance is copied to the outer attribute if the embedded parser succeeds and it is discarded otherwise.   
  
The code not just `instantiates a new attribute`, but make a copy of the current attribute:  
https://github.com/boostorg/spirit/blob/9a8cded55355644c749d6511504b59c048d64de3/include/boost/spirit/home/qi/directive/hold.hpp#L61

---

## Comment 23

> Username: Kojoley  
> Created at: 2018-03-17 15:32:55 UTC  
> Url: https://github.com/boostorg/spirit/issues/835#issuecomment-3341125470  

Well, I have no comments here...  
  
```cpp  
#include <boost/detail/lightweight_test.hpp>  
#include <boost/spirit/home/x3.hpp>  
#include <string>  
#include "../spirit/test/x3/test.hpp"  
  
int main()  
{  
    using spirit_test::test_attr;  
    namespace x3 = boost::spirit::x3;  
  
    {  
        std::string s = "z";  
        auto a = x3::rule<struct _a, std::string>{} = x3::char_("a") >> x3::char_("b");  
        auto b = x3::rule<struct _b, std::string>{} = x3::char_("a") >> x3::char_("c");  
        BOOST_TEST(test_attr("ac", a | b, s));  
        BOOST_TEST_EQ("ac", s);  
    }  
  
    return boost::report_errors();  
}  
```  
```  
main.cpp(145): test '"ac" == s' failed in function 'int __cdecl main(void)': '000000013F487724' != 'zac'  
1 error detected.  
```

---

## Comment 24

> Username: Xeverous  
> Created at: 2022-11-14 17:48:05 UTC  
> Url: https://github.com/boostorg/spirit/issues/835#issuecomment-3341125472  

Is this problem resolved? Based on the fact that all linked issues are closed and the last comment by @Kojoley shows the cleanest implementation in practice (attr copy but clear on failed alternative) I assume everything has been fixed. Correct?

---

## Comment 25

> Username: Kojoley  
> Created at: 2022-11-14 19:44:29 UTC  
> Url: https://github.com/boostorg/spirit/issues/835#issuecomment-3341125474  

> Is this problem resolved? Based on the fact that all linked issues are closed and the last comment by @Kojoley shows the cleanest implementation in practice (attr copy but clear on failed alternative) I assume everything has been fixed. Correct?  
  
No :-( Nothing changed here, Spirit still does not rollback container attributes.  
Though, I had an idea how to solve it simply and cheaply: introduce a container rollback trait which for a vector-like containers just stores current size and upon a rollback request resizes it to the saved size. The solution adds a new requirement for parsers and semantic actions to not remove elements past the savepoint (or simply to only add elements, never remove) which does not seem like it will break a lot of users, but no one can say for sure.

---

## Comment 26

> Username: Xeverous  
> Created at: 2022-11-16 00:01:35 UTC  
> Url: https://github.com/boostorg/spirit/issues/835#issuecomment-3341125478  

> No :-( Nothing changed here, Spirit still does not rollback container attributes.  
  
Why? Wasn't solution 6 (clear on alternative failure when parsing into a container) the best choice?  
  
> The solution adds a new requirement for parsers and semantic actions to not remove elements past the savepoint  
  
IMO semantic actions have no place in X3. The library already offers a mechanism for hooking code into attributes (`annotate_on_success` in examples) and by their design, semantic actions are prone to breaking something. The docs already discourage their use in X3.

---

## Comment 27

> Username: saki7  
> Created at: 2025-09-26 11:34:14 UTC  
> Url: https://github.com/boostorg/spirit/issues/835#issuecomment-3341125480  

We can't change this behavior in X3 because it would result in altering the entire semantics of the `operator|`, specifically for the side effect on the failed branch.  
  
The fact that this issue has been unresolved for 7 years is enough for us to assume there are certain amount of people who would accuse us for changing this behavior. (I'm not referring to someone who commented on this issue; there is some people in the wild who leaves surprising arguments, especially for semantic issues like this).  
  
I'm working on a fix for X4.

---

## Comment 28

> Username: Xeverous  
> Created at: 2025-09-26 14:17:16 UTC  
> Url: https://github.com/boostorg/spirit/issues/835#issuecomment-3341125482  

> The fact that this issue has been unresolved for 7 years is enough for us to assume there are certain amount of people who would accuse us for changing this behavior.  
  
Definitely true. My project relies on this. I have a lot of code like `a | b | c` and if any of a/b/c has a subgrammar that has a container attribute, I absolutely expect the container to be cleared before testing further alternatives. I would never expect a container attribute to contain data that came from prior failed alternatives.  
  
Is X4 going to behave differently for `a | b` grammars (e.g. require `hold` or `clear`) or is X4 issue only how to optimize container clear/copy/move when implementing `|`?

---

## Comment 29

> Username: Xeverous  
> Created at: 2025-09-26 14:22:03 UTC  
> Url: https://github.com/boostorg/spirit/issues/835#issuecomment-3341125483  

> Is X4 going to behave differently for `a | b` grammars (e.g. require `hold` or `clear`) or is X4 issue only how to optimize container clear/copy/move when implementing `|`?  
  
Nevermind, I see in boostorg/spirit_x4#50 it fixes the problem of this leftover data after a failed alternative.

---

## Comment 30

> Username: saki7  
> Created at: 2025-09-26 14:38:59 UTC  
> Url: https://github.com/boostorg/spirit/issues/835#issuecomment-3341125485  

> My project relies on this. I have a lot of code like `a | b | c` and if any of a/b/c has a subgrammar that has a container attribute, I absolutely expect the container to be cleared before testing further alternatives. I would never expect a container attribute to contain data that came from prior failed alternatives.  
  
If you rely on that assumption, then you might still be seeing the leftover data from the failed alternatives in X3.   
  
My PR fixed the `x4::alternative`'s implementation to clear the container on such scenario in X4, but X3 is unchanged. I think your grammar indeed suffers from the leftover data, but I'm suspecting that you're not seeing it because some ASTs can effectively clear the attributes in some situations.   
  
To clarify, the code below should still yield `"aac"` in X3, whereas the _correct_ output should be `"ac"`.  
After boostorg/spirit_x4#50, X4 produces `"ac"` in this case.  
  
```cpp  
std::string str;  
std::string_view input = "ac";  
auto first = input.begin();  
auto last = input.end();  
x3::parse(first, last, x3::char_('a') >> x3::char_('b') | x3::string("ac"), str);  
```  
  
I know this looks weird, but it _could_ have some legitimate outcome on more complex container class, not `std::string`. That's why I said I can't change this behavior in X3, because there might be some people relying on this (wrong) version of the output. In other words, your interpretation for the expected behavior is correct, but it's "wontfix" in X3.

---

## Comment 31

> Username: Xeverous  
> Created at: 2025-09-26 14:59:39 UTC  
> Url: https://github.com/boostorg/spirit/issues/835#issuecomment-3341125488  

> My PR fixed the `x4::alternative`'s implementation to clear the container on such scenario in X4, but X3 is unchanged. I think your grammar indeed suffers from the leftover data, but I'm suspecting that you're not seeing it because some ASTs can effectively clear the attributes in some situations.  
  
I remember I did report some container-related bugs in X3 (e.g. lack of support for moveable-non-copyable types) but I don't remember any case of surprising leftover data. Maybe I'm just lucky. I use `std::string` only for 2 grammars and everything else has custom attribute type. But thanks for this information. If I encounter `aac` type of thing in the output, I will know why.  
  
I just tested your example. `str` is indeed `aac` on X3. I don't think there is a legitimate case for this though. If someone wanted leftover data, I would expect it to require semantic actions. Very undesirable for me, but I understand why it is "wontfix".

---

## Comment 32

> Username: Kojoley  
> Created at: 2025-09-26 23:58:48 UTC  
> Url: https://github.com/boostorg/spirit/issues/835#issuecomment-3341125489  

What is happening? Why this issue is moved to another repository and after has been closed?!  
  
While the idea I've described in the first post is complex to implement the issue of no container attribute rollback is still one of the major Spirit deficiencies people run into. There are also other ways to fix it (I had an idea of inexpensive with `.resize()` calls). If I don't have time and/or motivation to implement these changes myself it doesn't mean the issues can't be worked on by anyone else and need to be closed.  
  
I hate with a passion when bots close tickets with `The issue wasn't active in past 30 days, it's marked as stale. The issue wasn't active in past 30 days, it's now closed`. Don't be like that.

---

## Comment 33

> Username: saki7  
> Created at: 2025-09-27 01:44:00 UTC  
> Url: https://github.com/boostorg/spirit/issues/835#issuecomment-3341125491  

@Kojoley  **7 years.** It's not 30 days. 7 years is enough for me to determine that the status quo is already exposed for too long period. [Hyrum's Law](https://github.com/boostorg/spirit/issues/833#issuecomment-3336173582).  
  
Spirit.X3 is feature-frozen under organization-wide consensus. The fact that breaking changes in X3 makes too many people unhappy is the reason that the core devs (including Joel) suggested creating X4, and I agreed on it.   
  
I am sure that I notified your account so many times during when the above discussion was taking place. I opened more than 40 PRs that mentions modernization. I even asked you to provide your email address for updating the library's `meta/` info. There was a serious thread discussing these matter on the Boost Developers Mailing List.  
  
You never replied to them. You can't revert the status quo after you've been inactive as the maintainer for so long time, and after all the organization-wide discussion was actually concluded.  
  
And also, please read the above comments more carefully. There even is an outside collabolator who saw my comment and agreed that there's good reason to make this wontfix in X3.

---

## Comment 34

> Username: Kojoley  
> Created at: 2025-09-27 02:13:26 UTC  
> Url: https://github.com/boostorg/spirit/issues/835#issuecomment-3341125494  

> [@Kojoley](https://github.com/Kojoley) **7 years.** It's not 30 days. 7 years is enough for me to determine that the status quo is already exposed for too long period. [Hyrum's Law](https://github.com/boostorg/spirit/issues/833#issuecomment-3336173582).  
  
This is a strawman argument. I've fixed issues opened for 20 years in Spirit Classic and 10 years opened in Spirit Qi.  
  
> Spirit.X3 is feature-frozen under organization-wide consensus. The fact that breaking changes in X3 makes too many people unhappy is the reason that the core devs (including Joel) suggested creating X4, and I agreed on it.  
  
I think if we would do a poll anyone would universally agree that the status quo on container state after backtracking is wrong. People actively workaround the issue to have a behavior that differs from the status quo.  
  
> I am sure that I notified your account so many times during when the above discussion was taking place. I opened more than 40 PRs that mentions modernization. I even asked you to provide your email address for updating the library's `meta/` info. There was a serious thread discussing these matter on the Boost Developers Mailing List.  
>   
> You never replied to them. You can't revert the status quo after you've been inactive as the maintainer for so long time, and after all the organization-wide discussion was actually concluded.  
  
  
Precisely because my mailbox is full of stuff, I can't participate in every discussion. I also don't participate in the mail list because I can't answer things there without letting my mailbox be blown up with discussions I have no interest in, I've brought up the issue multiple times, and stopped looking at boost mail list after boost organization takeover by third entity discussions.  
  
> And also, please read the above comments more carefully. There even is an outside collabolator who saw my comment and agreed that there's good reason to make this wontfix in X3.  
  
Where?!  
  
It seem the opposite:  
  
> I absolutely expect the container to be cleared before testing further alternatives. I would never expect a container attribute to contain data that came from prior failed alternatives.

---

## Comment 35

> Username: djowel  
> Created at: 2025-09-27 02:24:17 UTC  
> Url: https://github.com/boostorg/spirit/issues/835#issuecomment-3341125495  

Peace. While it’s true that we are now in a feature freeze, there’s also a clear consensus that feature freeze does not mean unmaintained. I would place bugs like this under maintenance—still within the bounds of feature freeze—as long as it can be shown that they do not introduce disruptions to the codebase.  
  
Let’s keep in mind that disruption was the main reason for the backlash: the code move to C++23 and feature changes were too drastic, which ultimately led X4 to compartmentalize X3. That said, I still want to keep @Kojoley as maintainer. If there’s a reasonable path forward, I’d prefer not to discard his hard work.

---

## Comment 36

> Username: saki7  
> Created at: 2025-09-27 02:28:26 UTC  
> Url: https://github.com/boostorg/spirit/issues/835#issuecomment-3341125496  

Submit a PR for X3 please. If the PR seems reasonable, I always make it an exception for the feature-freeze and will look into it. Policy is policy and there are always exceptions in practice. I have to admit that I actually have basic flexibility.

---

## Comment 37

> Username: Kojoley  
> Created at: 2025-09-27 02:34:38 UTC  
> Url: https://github.com/boostorg/spirit/issues/835#issuecomment-3341125499  

> Submit a PR for X3 please. If the PR seems reasonable, I always make it an exception for the feature-freeze and will look into it. Policy is policy and there are always exceptions in practice. I have to admit that I actually have basic flexibility.  
  
You really sound like "I have time and you don't so shut up or show the code" while I only asked for why the issue has moved repositories and have been closed.

---

## Comment 38

> Username: saki7  
> Created at: 2025-09-27 02:41:08 UTC  
> Url: https://github.com/boostorg/spirit/issues/835#issuecomment-3341125502  

> You really sound like "I have time and you don't so shut up or show the code" while I only asked for why the issue has moved repositories and have been closed.  
  
The issue was moved to X4 because I effectively resolved it for X4 in boostorg/spirit_x4#50. I also added the label "wontfix-X3" to indicate that is the current decision. Labels can be unlabeled in the future if there's reasonable PR.  
  
We simply can't have duplicate issues in two repositories, and I must keep the information gathered on the *fixed* repository, which is X4. No offense, but this is purely a maintenance work.

---

## Comment 39

> Username: Kojoley  
> Created at: 2025-09-27 02:56:26 UTC  
> Url: https://github.com/boostorg/spirit/issues/835#issuecomment-3341125504  

> > You really sound like "I have time and you don't so shut up or show the code" while I only asked for why the issue has moved repositories and have been closed.  
>   
> The issue was moved to X4 because I effectively resolved it for X4 in [#50](https://github.com/boostorg/spirit_x4/pull/50). I also added the label "wontfix-X3" to indicate that is the current decision. Labels can be unlabeled in the future if there's reasonable PR.  
>   
> We simply can't have duplicate issues in two repositories, and I must keep the information gathered on the _fixed_ repository, which is X4. No offense, but this is purely a maintenance work.  
  
You could've just opened an issue in this repository and stop touching X3 repository. But for some reason you now have an urge to kill X3 like you wanted to kill Classic and Qi to force users migrate? It seemed like you've got a lesson that things don't work that way, but you still insist on your views, you didn't learn.

---

## Comment 40

> Username: saki7  
> Created at: 2025-09-27 03:06:49 UTC  
> Updated at: 2025-09-27 03:09:32 UTC  
> Url: https://github.com/boostorg/spirit/issues/835#issuecomment-3341125507  

If we keep updating X3 forever, then there's zero incentive for the users to migrate to X4. If there's no active "feature-freeze", the creation of X4 would have no meaning in practice.  
  
> But for some reason you now have an urge to kill X3   
  
If I aim to "kill" X3 then I would have deleted the X3 repository immediately. I had never intended to plan such things for X3, and instead I participated on the mailing list to build the consensus to find the right path. The consensus we have is that we can't do breaking changes in X3, which makes too many people unhappy.

---

## Comment 41

> Username: saki7  
> Created at: 2025-09-27 03:16:56 UTC  
> Url: https://github.com/boostorg/spirit/issues/835#issuecomment-3341125510  

To clarify: I actally think that fixing this issue would involve serious breaking changes. It literally changes the semantics of the `operator|`, in a library-wide scope. That's _huge_. I described it in https://github.com/boostorg/spirit/issues/835, thus I added the "wontfix-X3" label.

---

## Comment 42

> Username: djowel  
> Created at: 2025-09-27 03:30:07 UTC  
> Url: https://github.com/boostorg/spirit/issues/835#issuecomment-3341125512  

> To clarify: I actally think that fixing this issue would involve serious breaking changes. It literally changes the semantics of the `operator|`, in a library-wide scope. That's _huge_. I described it in [#49 (comment)](https://github.com/boostorg/spirit/issues/835), thus I added the "wontfix-X3" label.  
  
Not necessarily. There can be ways to make the code non-breaking. E.g. using #defines. I think it is draconian to force everyone to switch to X4. There are those with established code bases using X3 that may be willing to switch, but may not have the time yet to do so in one full step, and a more pragmatic approach is to keep X3 for old code, but use X4 for fresh code.

---

## Comment 43

> Username: saki7  
> Created at: 2025-09-27 03:37:33 UTC  
> Url: https://github.com/boostorg/spirit/issues/835#issuecomment-3341125514  

> > To clarify: I actally think that fixing this issue would involve serious breaking changes. It literally changes the semantics of the `operator|`, in a library-wide scope. That's _huge_. I described it in [#49 (comment)](https://github.com/boostorg/spirit/issues/835), thus I added the "wontfix-X3" label.  
>   
> Not necessarily. There can be ways to make the code non-breaking. E.g. using #defines. I think it is draconian to force everyone to switch to X4. There are those with established code bases using X3 that may be willing to switch, but may not have the time yet to do so in one full step, and a more pragmatic approach is to keep X3 for old code, but use X4 for fresh code.  
  
I know. The above comment from me is merely a response to the demand to clarify why I "touched" the X3 repo and added the "wontfix-X3" label. It is important to explain the reasoning of "wontfix". It doesn't mean "wontfix and I'm not accepting any arguments".  
  
Again, I need to see a X3 PR, if someone insists this can be resolved in a benign way.

---

## Comment 44

> Username: saki7  
> Created at: 2025-09-27 03:41:55 UTC  
> Url: https://github.com/boostorg/spirit/issues/835#issuecomment-3341125516  

OK from now on, I only transfer issues that clearly requires adding new features. I think that's enough for us to avoid further drama.

---

## Comment 45

> Username: Kojoley  
> Created at: 2025-09-27 03:42:18 UTC  
> Url: https://github.com/boostorg/spirit/issues/835#issuecomment-3341125521  

> If we keep updating X3 forever, then there's zero incentive for the users to migrate to X4. If there's no active "feature-freeze", the creation of X4 would have no meaning in practice.  
>   
> > But for some reason you now have an urge to kill X3  
>   
> If I aim to "kill" X3 then I would have deleted the X3 repository immediately. I had never intended to plan such things for X3, and instead I participated on the mailing list to build the consensus to find the right path. The consensus we have is that we can't do breaking changes in X3, which makes too many people unhappy.  
  
You have learned the wrong lesson. It's not that you can't do breaking changes, it's that you can't do changes that piss off people with no clear benefits and no path forward. I've found the maillist thread you've mentioned, and people are clearly pissed of because you were forcing changes on them without a warning and without clear benefits to them. Anyway, I spend more time on this that I should've really and also got PTSD, I'm going to walk away.

---

## Comment 46

> Username: saki7  
> Created at: 2025-09-27 03:57:32 UTC  
> Url: https://github.com/boostorg/spirit/issues/835#issuecomment-3341127596  

Reopening this to reflect the status quo. For this issue, I only accept the PR that does not involve breaking changes. If no solutions emerge, this issue is "wontfix-X3", as per our maintenance policy.

---

## Comment 47

> Username: saki7  
> Created at: 2025-09-27 04:02:31 UTC  
> Url: https://github.com/boostorg/spirit/issues/835#issuecomment-3341133565  

I'm unsubscribing this issue since the discussion is going in a very undesirable direction. For this issue, I will only respond to technical comments. Feel free to mention @saki7 if someone  wants to have technical discussion with me. Thank you for your understanding.

---

## Comment 48

> Username: Xeverous  
> Created at: 2025-09-27 10:51:00 UTC  
> Url: https://github.com/boostorg/spirit/issues/835#issuecomment-3341501275  

@saki7 since you removed the wontfix label it means you want X3 to be changed.  
  
I like @djowel 's idea to introduce a `#define` to be able to switch this behavior (if it doesn't complicate the implementation significantly). What do you think about it?  
  
I can not speak for all users of X3 but as someone who learned all the theory about parsers while learning the library, seeing `x3::char_('a') >> x3::char_('b') | x3::string("ac")` parsing `"ac"` and producing `"aac"` would still be a clear bug to me. If someone relies on this behavior, I think they are relying on very undesirable implementation bug. Plus for such users, there should be an easy workaround with semantic actions if they do want leftover data from prior failed alternatives.  
  
Another reason to repair this behavior is consistency: My project has plenty of grammars like `a >> b | c >> d` and also many containers but I have never experienced the "aac" bug. This puts me into suspicion that the bug happens only when parsing into `std::string`.  
  
@saki7 I understand your Hyrum's Law argument but IMO this is a clear bug. At most we can add a `#define` if someone really wants old behavior. IMO Hyrum's Law does not apply to clear bugs. If someone wrote C++ code that is technically undefined behavior and the segfault happens only under later standards, it is the programmer's fault, not the standard's fault.  
  
@saki7 How hard would it be to port X4's solution to X3?

---

## Comment 49

> Username: saki7  
> Created at: 2025-09-27 10:57:00 UTC  
> Url: https://github.com/boostorg/spirit/issues/835#issuecomment-3341508890  

> @saki7 How hard would it be to port X4's solution to X3?  
  
That's the PR author's responsibility to investigate and explain those facts in the PR description. If you ask me for such information then it's essentially going to be _my_ work.

---

## Comment 50

> Username: saki7  
> Created at: 2025-09-27 11:05:19 UTC  
> Url: https://github.com/boostorg/spirit/issues/835#issuecomment-3341519110  

At the least, I have submitted my PR boostorg/spirit_x4#50 so there's actually the code. You can ask the code author (me) for some technical details about the specific code, but I'd suggest not touching the implementation details if you don't understand what the code is doing. I'm truly sorry, but I can't provide line-by-line description for practical reasons.
