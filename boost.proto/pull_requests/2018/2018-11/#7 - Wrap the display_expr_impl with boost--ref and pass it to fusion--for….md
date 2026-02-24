# #7 Wrap the display_expr_impl with boost::ref and pass it to fusion::for… [Merged]

> Username: Burgos  
> Created at: 2018-11-01 18:56:53 UTC  
> Updated at: 2018-11-02 16:05:59 UTC  
> Merged at: 2018-11-01 23:20:28 UTC  
> Closed at: 2018-11-01 23:20:28 UTC  
> Url: https://github.com/boostorg/proto/pull/7  

…_each  
  
Previously, boost::fusion::for_each was accepting const reference  
as a functor, but it was changed in 1.68 to accept a value type instead  
(https://github.com/boostorg/fusion/commit/102588ae4636ffb418ee678d5d71d110bfcd0eb4).  
  
This adapts proto to pass the functor as a value using  
boost::bind/boost::ref instead passing a non-copyable functor which  
would generate copy constructor call.

---

## Comment 1

> Username: Burgos  
> Created_at: 2018-11-01 23:17:57 UTC  
> Url: https://github.com/boostorg/proto/pull/7#issuecomment-435218664  

@ericniebler it would be great to get this in before the window for 1.69 closes. Right now this causes even introductory examples to be broken in 1.68.

---

## Comment 2

> Username: Kojoley  
> Created_at: 2018-11-02 15:15:09 UTC  
> Updated_at: 2018-11-02 15:16:06 UTC  
> Url: https://github.com/boostorg/proto/pull/7#issuecomment-435413109  

This change makes Spirit placeholders ambiguous (in places where earlier it was not).  
Also, please do not introduce a new dependency, it makes Spirit even slower.

---

## Comment 3

> Username: Burgos  
> Created_at: 2018-11-02 15:22:02 UTC  
> Url: https://github.com/boostorg/proto/pull/7#issuecomment-435415452  

I think `::_1` here should do the trick, I'll make a new PR and will ping you there. Not sure how to solve this without bind, though, since `boost::ref` doesn't wrap perfectly (opposite of `std::ref`). Suggestions?

---

## Comment 4

> Username: Kojoley  
> Created_at: 2018-11-02 15:27:18 UTC  
> Updated_at: 2018-11-02 15:27:28 UTC  
> Url: https://github.com/boostorg/proto/pull/7#issuecomment-435417206  

It looks like a regression in Fusion because only `for_each` takes a functor by value, so it might better to fix Fusion.

---

## Comment 5

> Username: Burgos  
> Created_at: 2018-11-02 15:30:32 UTC  
> Url: https://github.com/boostorg/proto/pull/7#issuecomment-435418335  

I've checked that, but it's not a regression, but a breaking change (documented in 1.68 release notes), and judging by other PRs, that's expected behaviour: https://github.com/boostorg/fusion/issues/187

---

## Comment 6

> Username: Burgos  
> Created_at: 2018-11-02 15:37:45 UTC  
> Url: https://github.com/boostorg/proto/pull/7#issuecomment-435420666  

Just writing a simple manual functor does the trick, though.

---

## Comment 7

> Username: Kojoley  
> Created_at: 2018-11-02 15:39:59 UTC  
> Updated_at: 2018-11-02 15:43:23 UTC  
> Url: https://github.com/boostorg/proto/pull/7#issuecomment-435421401  

I see, the documentation was always saying that `for_each` takes by value https://www.boost.org/doc/libs/1_60_0/libs/fusion/doc/html/fusion/algorithm/iteration/functions/for_each.html

---

## Comment 8

> Username: Kojoley  
> Created_at: 2018-11-02 16:01:25 UTC  
> Url: https://github.com/boostorg/proto/pull/7#issuecomment-435428314  

We can workaround this with a hack `fusion::for_each<Expr, display_expr_impl&>(expr, display);` since Fusion internally passes functor by references.  
  
> `boost::ref` doesn't wrap perfectly  
  
`boost::ref` does not have `operator()` at all...

---

## Comment 9

> Username: Burgos  
> Created_at: 2018-11-02 16:03:04 UTC  
> Url: https://github.com/boostorg/proto/pull/7#issuecomment-435428861  

Yeah, best to avoid that altogether. #8 should fix your issue.

---

## Comment 10

> Username: Burgos  
> Created_at: 2018-11-02 16:05:59 UTC  
> Url: https://github.com/boostorg/proto/pull/7#issuecomment-435429743  

Thanks a ton for reporting the issue!

---
