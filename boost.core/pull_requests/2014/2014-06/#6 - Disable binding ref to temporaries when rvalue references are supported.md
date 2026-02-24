# #6 Disable binding ref to temporaries when rvalue references are supported [Merged]

> Username: K-ballo  
> Created at: 2014-06-09 20:12:00 UTC  
> Updated at: 2014-06-14 10:42:46 UTC  
> Merged at: 2014-06-10 00:08:35 UTC  
> Closed at: 2014-06-10 00:08:35 UTC  
> Url: https://github.com/boostorg/core/pull/6  



---

## Comment 1

> Username: pdimov  
> Created_at: 2014-06-09 20:25:34 UTC  
> Url: https://github.com/boostorg/core/pull/6#issuecomment-45539492  

I support this in principle, but, (1) the deleted ref/cref probably shouldn't be forceinline, (2) I'd rather not use the requires feature in the Jamfile until the potential problems with it are sorted out, (3) the ref/cref negative tests should just do boost::ref(2), instead of trying to store the result.

---

## Comment 2

> Username: K-ballo  
> Created_at: 2014-06-09 20:41:18 UTC  
> Url: https://github.com/boostorg/core/pull/6#issuecomment-45541323  

> (1) the deleted ref/cref probably shouldn't be forceinline,  
  
Agreed.  
  
> (2) I'd rather not use the requires feature in the Jamfile until the potential problems with it are sorted out,  
  
This is needed because the tests would fail to fail in c++98 mode. The other alternative that I considered was introducing an artificial failure in the tests if rvalue-references are not supported (is there precedent for this?). I'm fine with either doing this or putting this pull-request on hold.  
  
> (3) the ref/cref negative tests should just do boost::ref(2), instead of trying to store the result.  
  
The return type of the deleted overloads is `void`. The assignment should result in a compilation error instead of a linker error on compilers that do not support deleted functions.

---

## Comment 3

> Username: pdimov  
> Created_at: 2014-06-09 20:52:48 UTC  
> Updated_at: 2014-06-09 20:56:24 UTC  
> Url: https://github.com/boostorg/core/pull/6#issuecomment-45542625  

You're right on (3), but this raises the question of whether you need to store `ref(2)` in a `reference_wrapper<int>` instead, as this is what it returns today. On the other hand, `ref(2)` already fails, I think. So perhaps you need two tests, one with `reference_wrapper<int> r = ref(2)`, and another with `reference_wrapper<X const> r = ref(something-returning-an-X-const-rvalue)`? (There are no `int const` rvalues, so you need to use a class here.)  
  
On (2), the common practice so far has been to #ifdef the test to `int main() {}` when the feature being tested is not supported, that is, to make it artificially pass. As a practical matter, this results in a green `pass` cell instead of an empty cell (which the `requires` approach would produce), and I rather like that better.

---

## Comment 4

> Username: pdimov  
> Created_at: 2014-06-09 20:55:11 UTC  
> Url: https://github.com/boostorg/core/pull/6#issuecomment-45542911  

Ah wait, the tests are `compile-fail`, we can't `#ifdef` them out to `int main {}`. Hmm.

---

## Comment 5

> Username: pdimov  
> Created_at: 2014-06-09 21:00:34 UTC  
> Url: https://github.com/boostorg/core/pull/6#issuecomment-45543549  

How about we just `#ifdef` them out to `#error To fail, this test requires rvalue references`?

---

## Comment 6

> Username: K-ballo  
> Created_at: 2014-06-09 21:04:32 UTC  
> Url: https://github.com/boostorg/core/pull/6#issuecomment-45544022  

> So perhaps you need two tests, one with reference_wrapper<int> r = ref(2), and another with reference_wrapper<X const> r = ref(something-returning-an-X-const-rvalue)? (There are no int const rvalues, so you need to use a class here.)  
  
Makes sense, but wouldn't it be enough with the `reference_wrapper<X const>` test only?  
  
> On (2), the common practice so far has been to #ifdef the test to int main() {} when the feature being tested is not supported, that is, to make it artificially pass. As a practical matter, this results in a green pass cell instead of an empty cell (which the requires approach would produce), and I rather like that better.  
> How about we just `#ifdef` them out to `#error To fail, this test requires rvalue references`?  
  
Sounds perfect to me.  
  
Additionally, I just noticed that I missed the standard overloads taking `reference_wrapper<T>`. Without them, cases like `boost::ref(boost::ref(lvalue))` fail to compile. I will add those too as well as a test for them.

---

## Comment 7

> Username: pdimov  
> Created_at: 2014-06-09 21:12:13 UTC  
> Url: https://github.com/boostorg/core/pull/6#issuecomment-45544872  

I also noticed that we're missing those overloads, but please put them into a separate pull request, if that won't be a problem. (So that if they cause a problem with the upcoming release we can revert just the problematic change and keep the other.)  
  
You might also wish to rename the tests here to something like `ref_rv_fail1` so that we know that they've something to do with rvalues.

---

## Comment 8

> Username: pdimov  
> Created_at: 2014-06-09 21:22:50 UTC  
> Url: https://github.com/boostorg/core/pull/6#issuecomment-45546071  

Regarding the `ref(2)` test - let's keep it. It doesn't even need to be `#ifdef`-ed out. You can never be too sure. :-)

---

## Comment 9

> Username: K-ballo  
> Created_at: 2014-06-09 21:38:56 UTC  
> Url: https://github.com/boostorg/core/pull/6#issuecomment-45547808  

> I also noticed that we're missing those overloads, but please put them into a separate pull request, if that won't be a problem. (So that if they cause a problem with the upcoming release we can revert just the problematic change and keep the other.)  
  
I wonder if the explicit converting constructor from `T&` (implicit in the standard) is going to interact badly with the implicit converting constructor from `reference_wrapper<T> const&`. Thoughts?  
  
Also, would it be ok to keep a single pull request with two commits in it? Considering that the disabled overloads depend on the flattening overloads being there already.

---

## Comment 10

> Username: pdimov  
> Created_at: 2014-06-09 21:47:06 UTC  
> Url: https://github.com/boostorg/core/pull/6#issuecomment-45548626  

Yes, it would be fine. But I'm interested in your second sentence. Why do the disabled overloads depend on the flattening overloads?

---

## Comment 11

> Username: pdimov  
> Created_at: 2014-06-09 21:50:48 UTC  
> Updated_at: 2014-06-09 21:51:13 UTC  
> Url: https://github.com/boostorg/core/pull/6#issuecomment-45549022  

We should probably keep the `reference_wrapper<T>`-taking constructor explicit as well in the no-rvalue-reference case; if we have rvalue references, we can make them both implicit. I think that the danger of implicitly converting an rvalue to a `reference_wrapper` is the motivation of our explicit constructor, and we're no longer allowing that. One more test, I suppose.

---

## Comment 12

> Username: K-ballo  
> Created_at: 2014-06-09 22:10:47 UTC  
> Url: https://github.com/boostorg/core/pull/6#issuecomment-45550867  

> Why do the disabled overloads depend on the flattening overloads?  
  
I was thinking of a situation like this:  
  
```  
  int lvalue = 0;  
  boost::reference<int> r = boost::ref(boost::ref(lvalue));  
```  
  
This works with the current implementation, although probably just by chance as the outermost call to `boost::ref` is binding to a temporary. With `std::ref`, it works because of the folding overloads.  
  
> We should probably keep the reference_wrapper<T> taking constructor explicit as well in the no-rvalue-reference case;  
  
Sorry for the confusion, that constructor is not a converting constructor but a copy constructor. Making it explicit would break the following trivial use case:  
  
```  
  boost::reference_wrapper<int> r1 = boost::ref(i); // error, ctor is explicit  
```

---

## Comment 13

> Username: pdimov  
> Created_at: 2014-06-09 22:20:09 UTC  
> Url: https://github.com/boostorg/core/pull/6#issuecomment-45551738  

Ah. I'm not sure then what interactions you have in mind. We already have the copy constructor.

---

## Comment 14

> Username: K-ballo  
> Created_at: 2014-06-09 22:56:49 UTC  
> Url: https://github.com/boostorg/core/pull/6#issuecomment-45554789  

Updated pull request.

---

## Comment 15

> Username: pdimov  
> Created_at: 2014-06-09 23:05:49 UTC  
> Url: https://github.com/boostorg/core/pull/6#issuecomment-45555506  

I don't understand the addition of the copy constructor.

---

## Comment 16

> Username: K-ballo  
> Created_at: 2014-06-09 23:08:28 UTC  
> Updated_at: 2014-06-09 23:08:38 UTC  
> Url: https://github.com/boostorg/core/pull/6#issuecomment-45555686  

> I don't understand the addition of the copy constructor.  
  
The presence of a (deleted or undefined) move constructor causes the implicit copy constructor to be defined as deleted.

---

## Comment 17

> Username: pdimov  
> Created_at: 2014-06-09 23:13:24 UTC  
> Url: https://github.com/boostorg/core/pull/6#issuecomment-45556065  

But we don't have one.

---

## Comment 18

> Username: K-ballo  
> Created_at: 2014-06-09 23:20:35 UTC  
> Url: https://github.com/boostorg/core/pull/6#issuecomment-45556700  

You are right, of course. I added one because the standard has one, but the implicit one might be fine. Let me think about this.

---

## Comment 19

> Username: K-ballo  
> Created_at: 2014-06-09 23:42:19 UTC  
> Url: https://github.com/boostorg/core/pull/6#issuecomment-45558218  

Removed copy constructor.

---

## Comment 20

> Username: pdimov  
> Created_at: 2014-06-10 00:10:16 UTC  
> Url: https://github.com/boostorg/core/pull/6#issuecomment-45560137  

Thanks for the patience. I'm going to remove the "Copyright 2002-2004 David Abrahams and Aleksey Gurtovoy" from the test however. I can see no copyrightable elements that could conceivably be construed as having been written by them.

---

## Comment 21

> Username: K-ballo  
> Created_at: 2014-06-10 00:12:29 UTC  
> Url: https://github.com/boostorg/core/pull/6#issuecomment-45560253  

It's been a pleasure collaborating with you on this!

---

## Comment 22

> Username: pdimov  
> Created_at: 2014-06-10 02:01:20 UTC  
> Updated_at: 2014-06-10 15:01:58 UTC  
> Url: https://github.com/boostorg/core/pull/6#issuecomment-45566155  

Thanks. `ref_fn_test` (which I had forgotten to copy from `libs/bind/test`) broke on msvc-10.0, as apparently the compiler considers functions to be rvalues, but there's always something.

---

## Comment 23

> Username: K-ballo  
> Created_at: 2014-06-10 14:50:53 UTC  
> Url: https://github.com/boostorg/core/pull/6#issuecomment-45623613  

Any suggestion on how to deal with that? I guess by disabling the new code on the deficient compilers.  
  
As a side note, the original ref tests are still present in utility. Shouldn't those be removed now?

---

## Comment 24

> Username: pdimov  
> Created_at: 2014-06-10 15:03:43 UTC  
> Url: https://github.com/boostorg/core/pull/6#issuecomment-45625532  

I can't think of anything at the moment.  
  
I've left the tests at their original places as a precaution. It's possible for the core tests to diverge from the originals which could potentially mask a regression. Just to be safe.

---

## Comment 25

> Username: Lastique  
> Created_at: 2014-06-14 10:07:41 UTC  
> Url: https://github.com/boostorg/core/pull/6#issuecomment-46083874  

Peter, do you plan to merge this change to master? We're close to freeze deadline.

---

## Comment 26

> Username: pdimov  
> Created_at: 2014-06-14 10:42:46 UTC  
> Url: https://github.com/boostorg/core/pull/6#issuecomment-46084534  

Looks good to go. Merged, thanks for the reminder.

---
