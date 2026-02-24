# #107 Compliant container-wide requirements. [Merged]

> Username: Flast  
> Created at: 2015-10-06 13:00:35 UTC  
> Updated at: 2015-10-17 02:09:41 UTC  
> Merged at: 2015-10-17 02:09:31 UTC  
> Closed at: 2015-10-17 02:09:31 UTC  
> Url: https://github.com/boostorg/fusion/pull/107  

As discussed in #105, drop some redundant special functions, and use universal references for generators.

---

## Comment 1

> Username: vtnerd  
> Created_at: 2015-10-07 06:42:06 UTC  
> Updated_at: 2015-10-08 13:37:32 UTC  
> Url: https://github.com/boostorg/fusion/pull/107#discussion_r41356178  

This series of expressions has happened a few times for perfect forwarding (I think). Should this be its own metafunction somewhere? Or is it already?

---

## Comment 2

> Username: vtnerd  
> Created_at: 2015-10-07 06:44:28 UTC  
> Updated_at: 2015-10-08 13:37:32 UTC  
> Url: https://github.com/boostorg/fusion/pull/107#discussion_r41356282  

`template<typename Sequence> set(Sequence&&)` is selected whenever `fusion::set` construction is given one argument, and is implicitly constructible from one argument without restrictions. The C++03 version uses `disable_if<is_convertible<Sequence, T0>>`, making it implicitly constructible only from another sequence. I'm guessing this is desired.  
  
T0 doesn't exist. I _think_ a better expression is `enable_if<is_sequence<Sequence> && sizeof...(T) == Sequence::size>`. Except, make it not error when `Sequence` isn't a fusion sequence. Its tempting to leave out the size comparison expression, but its omission would allow implicit conversions like `fusion::vector<int, float>` to `fusion::set<fusion::vector<int, float>>`. I think that would be a bug (a two element sequence implicitly converting to a 1 element sequence). However, explicit construction should be allowed in that scenario, which is still provided by the `U&&...` constructor.  
  
The `U&&` constructor should not need explicit disabling anymore (I was wrong earlier!), since it is lower precedence to the single argument constructor.

---

## Comment 3

> Username: vtnerd  
> Created_at: 2015-10-07 06:47:17 UTC  
> Updated_at: 2015-10-08 13:37:32 UTC  
> Url: https://github.com/boostorg/fusion/pull/107#discussion_r41356433  

One last thing, is the `call_param<T>::type...` constructor still needed? Its basically identical to the U&& version, and the U version is going to be selected if any implicit conversions between types are needed since its a perfect match.

---

## Comment 4

> Username: Flast  
> Created_at: 2015-10-07 08:39:13 UTC  
> Updated_at: 2015-10-08 13:37:32 UTC  
> Url: https://github.com/boostorg/fusion/pull/107#discussion_r41364653  

> Should this be its own metafunction somewhere?  
  
Something like a  
  
``` cpp  
template <typename T>  
using FUSION_DECAY = typename detail::as_fusion_element<  
                         typename remove_const< // Should we handle volatile here?  
                             typename remove_reference<T>::type  
                         >::type  
                     >::type;  
```  
  
?

---

## Comment 5

> Username: Flast  
> Created_at: 2015-10-07 08:39:16 UTC  
> Updated_at: 2015-10-08 13:37:32 UTC  
> Url: https://github.com/boostorg/fusion/pull/107#discussion_r41364660  

Because SFINAE yields hard error in some combination with constexpr (see https://llvm.org/bugs/show_bug.cgi?id=23135), I don't want to use SFINAE as much as possible until serious regression is reported. If we decide dropping implicit sequence conversion ctor, we will be able to write maximize simpler, clearer and effective variadic containers. (IMO, most of the `implicit` often confuse users and to be complex gratuitously...)  
  
> One last thing, is the call_param<T>::type... constructor still needed?  
  
I ran regression and confirmed that can be dropped.

---

## Comment 6

> Username: vtnerd  
> Created_at: 2015-10-07 17:23:18 UTC  
> Updated_at: 2015-10-08 13:37:32 UTC  
> Url: https://github.com/boostorg/fusion/pull/107#discussion_r41418991  

The implicit conversion constructor does not currently have the `constexpr` label. Is that intentional?  
  
And I don't see how the referenced constexpr evaluation case applies to SFINAE in this situation. There is no reason to use a constexpr function in the SFINAE expression I proposed. Maybe I'm missing something.  
  
That constructor (`template<typename Sequence> set(Sequence&&)`) should likely be marked explicit if SFINAE cannot be used for some reason. I think it would be nice to keep existing behavior, which is implicitly convertible from any fusion sequence type and explicitly constructible from a matching list of items, but this obviously isn't my decision.

---

## Comment 7

> Username: Flast  
> Created_at: 2015-10-08 01:19:43 UTC  
> Updated_at: 2015-10-08 13:37:32 UTC  
> Url: https://github.com/boostorg/fusion/pull/107#discussion_r41467713  

> The implicit conversion constructor does not currently have the constexpr label. Is that intentional?  
  
Yes. During constructing (or copying, moving, ...) nested container, outer container's constexpr ctor tries to instantiate inner container's ctor including implicit conversion ctor whcih has SFINAE, like `enable_if<is_convertible<U0, T0>>::type`, even if such ctor is not eligible.  
  
[ticket 11267](https://svn.boost.org/trac/boost/ticket/11267) and #79 were affected with this.  
  
> That constructor (template<typename Sequence> set(Sequence&&)) should likely be marked explicit if SFINAE cannot be used for some reason.  
  
Ah, I might mislead you. I meant,  
- _implicit conversion_ ctor  
  
``` cpp  
SEQUENCE<int> src;  
OTHER_SEQUENCE<int> dst = src; // *explicit ctor* will reject this line  
// or   
OTHER_SEQUENCE<int> dst{src};  
```  
- _explicit conversion_  
  
``` cpp  
SEQUENCE<int> src;  
OTHER_SEQUENCE<int> dst = as_OTHER_SEQUENCE(src);  
// or  
OTHER_SEQUENCE<int> dst = convert<OTHER_SEQUENCE_tag>(src); // Oh, I realized that there is no documents for fusion::convert!  
```

---

## Comment 8

> Username: vtnerd  
> Created_at: 2015-10-08 03:07:38 UTC  
> Updated_at: 2015-10-08 13:37:32 UTC  
> Url: https://github.com/boostorg/fusion/pull/107#discussion_r41472226  

The situation I've been describing:  
  
``` c++  
/* C++03 versions */  
void foo(fusion::set<int> const&);  
int main() {  
    foo(10); // does not compile; implicit constructor is SFINAE disabled  
}  
```  
  
This wasn't [always the case with the C++03 `fusion::set`](https://github.com/boostorg/fusion/commit/f872d132), but is likely the correct behavior. I think regression should be avoided here. The C++11 fusion list currently in master has the same implicit conversion issue.  
  
> Yes. During constructing (or copying, moving, ...) nested container, outer container's constexpr ctor tries to instantiate inner container's ctor including implicit conversion ctor whcih has SFINAE, like enable_if<is_convertible<U0, T0>>::type, even if such ctor is not eligible.  
  
I believe this was due to the implementation of `is_convertible`, and not specifically caused by the mixture of SFINAE and constexpr. If you remove `is_convertible` from the expression in the [prior bug report](https://svn.boost.org/trac/boost/ticket/11267), the code compiles. This does make SFINAE difficult, because the expression cannot invoke anything that might cause an unexpected evaluation/instantiation. `enable_if<is_sequence<Sequence>::value && sizeof...(T) == Sequence::size>` should work if `Sequence::size` is properly lazily evaluated, because the expression should never attempt to use any methods in `Sequence` or the target fusion object for evaluation.

---

## Comment 9

> Username: vtnerd  
> Created_at: 2015-10-08 03:08:42 UTC  
> Updated_at: 2015-10-08 13:37:32 UTC  
> Url: https://github.com/boostorg/fusion/pull/107#discussion_r41472263  

Also, maybe the is_convertible check is necessary for proper SFINAE?

---

## Comment 10

> Username: K-ballo  
> Created_at: 2015-10-08 03:22:29 UTC  
> Updated_at: 2015-10-08 13:37:32 UTC  
> Url: https://github.com/boostorg/fusion/pull/107#discussion_r41472710  

I wouldn't be so sure that's the correct behavior. This works:  
  
```  
void foo(std::tuple<int> const&) {}  
int main() {  
    foo(10); // this works  
}  
```  
  
And more interestingly so does this:  
  
```  
std::tuple<int, int> bar() { return {1, 2}; }  
```  
  
It was enabled via a proposal dedicated to it, not just by mistake or oversight.

---

## Comment 11

> Username: Flast  
> Created_at: 2015-10-08 05:10:26 UTC  
> Updated_at: 2015-10-08 13:37:32 UTC  
> Url: https://github.com/boostorg/fusion/pull/107#discussion_r41476710  

> > Yes. During constructing (or copying, moving, ...) nested container, outer container's constexpr ctor tries to instantiate inner container's ctor including implicit conversion ctor whcih has SFINAE, like enable_if>::type, even if such ctor is not eligible.  
>   
> I believe this was due to the implementation of is_convertible, and not specifically caused by the mixture of SFINAE and constexpr. If you remove is_convertible from the expression in the prior bug report, the code compiles. This does make SFINAE difficult, because the expression cannot invoke anything that might cause an unexpected evaluation/instantiation.  
  
Ahhhhh! Yes! You're right. I misunderstood. OK, I'll add `is_sequence` checking.

---

## Comment 12

> Username: Flast  
> Created_at: 2015-10-15 01:18:57 UTC  
> Url: https://github.com/boostorg/fusion/pull/107#discussion_r42074400  

Done by ce4ccb9.  
I'll apply the checking into all other containers after variadics merged.

---

## Comment 13

> Username: djowel  
> Created_at: 2015-10-15 22:56:58 UTC  
> Url: https://github.com/boostorg/fusion/pull/107#issuecomment-148546225  

Are we all good on this? Are there still pending revisions?

---

## Comment 14

> Username: Flast  
> Created_at: 2015-10-17 02:08:20 UTC  
> Url: https://github.com/boostorg/fusion/pull/107#issuecomment-148877848  

> Are we all good on this?  
  
I think so.

---
