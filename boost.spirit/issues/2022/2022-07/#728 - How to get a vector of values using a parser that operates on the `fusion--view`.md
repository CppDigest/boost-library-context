# #728 - How to get a vector of values using a parser that operates on the `fusion::view`? [Closed]

> Username: denzor200  
> Created at: 2022-07-11 06:30:05 UTC  
> Updated at: 2025-05-09 23:31:55 UTC  
> Closed at: 2025-05-09 23:31:13 UTC  
> Url: https://github.com/boostorg/spirit/issues/728  

**Example code**  
https://godbolt.org/z/W8vsTPM6f  
  
**Current behaviour**  
Won't compile because `fusion::reverse_view` has no default constructor.  
  
**Expected behaviour**  
To print:  
```  
10 3.141  
20 4.555  
```  
  
Hello. Is it possible to implement support for this functionality on the Boost Spirit side? Thanks.

---

## Comment 1

> Username: denzor200  
> Created at: 2022-07-11 06:31:45 UTC  
> Url: https://github.com/boostorg/spirit/issues/728#issuecomment-1180018520  

It works good, when i need to get one element(not a vector):  
https://godbolt.org/z/qo74dG47z

---

## Comment 2

> Username: djowel  
> Created at: 2022-07-11 06:48:33 UTC  
> Updated at: 2022-07-11 07:01:32 UTC  
> Url: https://github.com/boostorg/spirit/issues/728#issuecomment-1180029187  

I don't think so. You need a mutable sequence. Views are not.  
  
Edit: Disregard.

---

## Comment 3

> Username: djowel  
> Created at: 2022-07-11 06:59:28 UTC  
> Url: https://github.com/boostorg/spirit/issues/728#issuecomment-1180036313  

> It works good, when i need to get one element(not a vector):  
> https://godbolt.org/z/qo74dG47z  
  
I see. So it's not really about mutability, but the requirement of std::vector to be able to default construct its elements.

---

## Comment 4

> Username: denzor200  
> Created at: 2022-07-11 07:29:24 UTC  
> Url: https://github.com/boostorg/spirit/issues/728#issuecomment-1180060116  

> but the requirement of std::vector to be able to default construct its elements  
  
We just need to disable this requirement on the Spirit side. And i suppose, we can do it only for sequence of some views

---

## Comment 5

> Username: Kojoley  
> Created at: 2022-07-11 20:22:29 UTC  
> Url: https://github.com/boostorg/spirit/issues/728#issuecomment-1180831578  

What does it mean for a rule to produce a view. Where that view will point to?

---

## Comment 6

> Username: denzor200  
> Created at: 2022-07-12 05:15:42 UTC  
> Url: https://github.com/boostorg/spirit/issues/728#issuecomment-1181324116  

> What does it mean for a rule to produce a view.  
  
I can't understand. You mean that this issue not possible?  
  
> Where that view will point to?  
  
I suppose that view won't have a long lifetime, it will be created just to be passed into child parse function as Attribute, and it will be point to element of user passed container from outside as Attribute.

---

## Comment 7

> Username: djowel  
> Created at: 2022-07-12 06:35:36 UTC  
> Url: https://github.com/boostorg/spirit/issues/728#issuecomment-1181372536  

Again, the issue here is that `std::vector` requires default constructible elements. Fusion views are not. Typically, they hold a reference to a fusion container.

---

## Comment 8

> Username: denzor200  
> Created at: 2022-07-12 12:52:21 UTC  
> Url: https://github.com/boostorg/spirit/issues/728#issuecomment-1181723732  

fix for it looks like very easy  
https://godbolt.org/z/hEhP4W6jz

---

## Comment 9

> Username: denzor200  
> Created at: 2022-07-12 12:58:27 UTC  
> Url: https://github.com/boostorg/spirit/issues/728#issuecomment-1181729749  

But i don't tested:  
-How it works with another operators(for example - optional), with not only kleene star.  
-How it works with fusion::view<fusion::view<fusion::view<...>>>.  
  
I may create a PR, if you are interesting.

---

## Comment 10

> Username: djowel  
> Created at: 2022-07-12 13:28:32 UTC  
> Url: https://github.com/boostorg/spirit/issues/728#issuecomment-1181761567  

> fix for it looks like very easy  
> https://godbolt.org/z/hEhP4W6jz  
  
Interesting trick!

---

## Comment 11

> Username: Kojoley  
> Created at: 2022-07-12 17:06:22 UTC  
> Url: https://github.com/boostorg/spirit/issues/728#issuecomment-1182021740  

> > What does it mean for a rule to produce a view.  
>   
> I can't understand. You mean that this issue not possible?  
  
Look at rules like they are [functions](https://www.boost.org/doc/libs/1_79_0/libs/spirit/doc/html/spirit/abstracts/attributes/nonterminal_attributes.html#:~:text=The%20nonterminals%20in%20the%20parser%20world%20are%20very%20similar%20to%20functions%20in%20an%20imperative%20programming%20language) witch returns values. What does it mean to return a view from a function? Assuming it is a stateless function -- It means it has received a view (a reference) as an argument. It is a an implementation detail that parsers receive a reference to the attribute, it could be done in another way, by returning values. Usually, Spirit parsers create a temporary to parse value to, and after parsing is done the value is is copied to the destination, parsing directly into the destination is just an optimization.  
  
> > Where that view will point to?  
>   
> I suppose that view won't have a long lifetime, it will be created just to be passed into child parse function as Attribute, and it will be point to element of user passed container from outside as Attribute.  
  
Multiple question surfaces from this:  
1) Why we want short-lived attributes?  
2) How we can ensure it lifetime?  
  
> fix for it looks like very easy [godbolt.org/z/hEhP4W6jz](https://godbolt.org/z/hEhP4W6jz)  
  
See, I understand there might be uses for producing views from rules, but your question looks like [xyproblem](https://xyproblem.info/).  
If your intention is to be able to reorder/slice fields, I think, it is better to find ways to express it inside the grammar, something like `slice(&TwoFields::second, &TwoFields::first)[double_ >> int_]` or even `double_ = &TwoFields::second >> int_ = &TwoFields::first`.  
  
> But i don't tested: -How it works with another operators(for example - optional), with not only kleene star.  
  
Or parsing into an optional.

---

## Comment 12

> Username: denzor200  
> Created at: 2022-07-12 18:34:36 UTC  
> Url: https://github.com/boostorg/spirit/issues/728#issuecomment-1182221337  

> If your intention is to be able to reorder/slice fields  
  
No, it's not a my intention. I answered this question not for reorder/slice fields, I do it for https://github.com/boostorg/fusion/pull/241, and real examples will have `fusion::pfr_fields_view` instead of `fusion::reverse_view`.

---

## Comment 13

> Username: denzor200  
> Created at: 2022-07-12 18:43:25 UTC  
> Url: https://github.com/boostorg/spirit/issues/728#issuecomment-1182242701  

Another words, answer for this question need for me to get rid of `BOOST_FUSION_ADAPT_STRUCT` macro. You can see details in my PRs into Fusion.

---

## Comment 14

> Username: Kojoley  
> Created at: 2022-07-12 19:02:52 UTC  
> Url: https://github.com/boostorg/spirit/issues/728#issuecomment-1182293639  

> > If your intention is to be able to reorder/slice fields  
>   
> No, it's not a my intention. I answered this question not for reorder/slice fields, I do it for [boostorg/fusion#241](https://github.com/boostorg/fusion/pull/241), and real examples will have `fusion::pfr_fields_view` instead of `fusion::reverse_view`.  
  
> Another words, answer for this question need for me to get rid of `BOOST_FUSION_ADAPT_STRUCT` macro. You can see details in my PRs into Fusion.  
  
Sequence parser internally converts a tuple into a fusion view, it should not require anything from Spirit to enable that. I would expect that adding PRF support to Fusion means that [`fusion::begin(attribute)`](https://www.boost.org/doc/libs/1_79_0/libs/fusion/doc/html/fusion/sequence/intrinsic/functions/begin.html) is working out-of-the box (without `BOOST_FUSION_ADAPT_STRUCT`, maybe by including some enabling header).

---

## Comment 15

> Username: denzor200  
> Created at: 2022-07-13 03:57:59 UTC  
> Url: https://github.com/boostorg/spirit/issues/728#issuecomment-1182737459  

> Sequence parser internally converts a tuple into a fusion view, it should not require anything from Spirit to enable that. I would expect that adding PRF support to Fusion means that [fusion::begin(attribute)](https://www.boost.org/doc/libs/1_79_0/libs/fusion/doc/html/fusion/sequence/intrinsic/functions/begin.html) is working out-of-the box (without BOOST_FUSION_ADAPT_STRUCT, maybe by including some enabling header).  
  
Totally agree with you. But `decltype(attribute)` in most cases will be `fusion::pfr_fields_view<AnUserDefinedStructure>`, that's why i raised this issue.

---

## Comment 16

> Username: denzor200  
> Created at: 2022-07-13 18:25:30 UTC  
> Url: https://github.com/boostorg/spirit/issues/728#issuecomment-1183542056  

> Why we want short-lived attributes?  
  
We have no choice when user need to parse into a container of `AnUserDefinedStructure`, he can't wrap it(or it's nonexistent elements) into view, but we can wrap each new element inside `x3::parse` call(by create our short-lived attribute), and then put new element into container.  
   
  
We don't want short-lived attribute when user need to parse into one `AnUserDefinedStructure`, he just can do wrapping into view manually, like here: https://godbolt.org/z/qo74dG47z

---

## Comment 17

> Username: denzor200  
> Created at: 2022-07-13 18:30:01 UTC  
> Url: https://github.com/boostorg/spirit/issues/728#issuecomment-1183546041  

> How we can ensure it lifetime?  
  
It was create inside an 'x3::parse' call and it will die here. There is no links to it outside the call

---

## Comment 18

> Username: Kojoley  
> Created at: 2022-07-13 20:25:52 UTC  
> Updated at: 2022-07-13 20:38:10 UTC  
> Url: https://github.com/boostorg/spirit/issues/728#issuecomment-1183644011  

You just going the wrong way. I see no point for rules to have a view attribute, it also does not make logical sense to me. The only reason you want them is to workaround something that you are not telling here (UPD: I think I have found which https://github.com/boostorg/fusion/pull/241#issuecomment-1183653223). If you want to make Fusion interop with PRF -- you should make Fusion functions on views to work with that new Fusion view type. Spirit should work out-of-the box when you implement all necessary Fusion stuff (and if it does not - file an issue about that).

---

## Comment 19

> Username: djowel  
> Created at: 2022-07-13 22:47:07 UTC  
> Url: https://github.com/boostorg/spirit/issues/728#issuecomment-1183756184  

After thinking about it, I think I am with @Kojoley here. Special case should be avoided as much as possible. There ought to be another solution that does not require special handling for a particular type/attribute without any workarounds?

---

## Comment 20

> Username: saki7  
> Created at: 2025-05-09 23:31:13 UTC  
> Url: https://github.com/boostorg/spirit/issues/728#issuecomment-2868090323  

Closing as stale. I agree with other maintainers' opinions.
