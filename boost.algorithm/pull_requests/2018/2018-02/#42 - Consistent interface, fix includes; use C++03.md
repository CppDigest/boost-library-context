# #42 [apply_permutation] Consistent interface, fix includes; use C++03. [Closed]

> Username: jeremy-murphy  
> Created at: 2018-02-05 13:42:22 UTC  
> Updated at: 2019-07-03 14:45:20 UTC  
> Closed at: 2019-07-03 14:45:20 UTC  
> Url: https://github.com/boostorg/algorithm/pull/42  

I love C++11 as much as everyone else but this component was mistakenly including the C++11 header `<type_traits>` when it should have included `<iterator>` (for `std::iterator_traits`). So the occasional `auto` and the `using` type alias don't appear to me to warrant requiring the newer language standard. Then, you know what it's like, you fix one thing and you can't stop.  
  
So I also made the interface and documentation consistent with using 3 iterators, not 4. That makes sense to me since the fourth iterator is not actually used in the algorithm logic anyway. (Yes, I know lots of people are excited about adding a fourth iterator to the standard algorithms, etc, etc.)  
  
I changed the unit test conventions a bit but it's no big deal to me to revert that change.  
  
Also added assertions of valid, non-overlapping iterator range inputs.

---

## Comment 1

> Username: mclow  
> Created_at: 2018-02-05 15:11:44 UTC  
> Url: https://github.com/boostorg/algorithm/pull/42#issuecomment-363113253  

> I know lots of people are excited about adding a fourth iterator to the standard algorithms, etc, etc  
  
One of those people is me.

---

## Comment 2

> Username: jeremy-murphy  
> Created_at: 2018-02-06 09:57:52 UTC  
> Url: https://github.com/boostorg/algorithm/pull/42#issuecomment-363370892  

>  
> I know lots of people are excited about adding a fourth iterator to the  
> standard algorithms, etc, etc  
>  
> One of those people is me.  
>  
  
I can imagine, I know your involved with that kind of work. My comment was  
just meant to mean that even though that work is happening, I hope it  
doesn't mean that four iterators are expected even when the fourth is  
redundant.

---

## Comment 3

> Username: mclow  
> Created_at: 2018-02-06 21:29:04 UTC  
> Url: https://github.com/boostorg/algorithm/pull/42#issuecomment-363570676  

There's always four iterators for this kind of thing.   
Sometimes it is implied, but that doesn't mean it's not there.  
  
If you have four iterators, the implementation can (for example) check to see if there's enough room in the output range. (even though we don't do that at the moment).

---

## Comment 4

> Username: jeremy-murphy  
> Created_at: 2018-02-08 13:00:44 UTC  
> Url: https://github.com/boostorg/algorithm/pull/42#issuecomment-364104634  

I get what you mean although I don't strictly agree, or I just don't agree with your terminology, but it's really tangential to this PR. Don't worry, I understand the utility that an explicit fourth iterator parameter can bring. I just hope that you agree (or can convince me otherwise), that there is no need to have it explicit for this algorithm where the two iterator range sizes must be equal.

---

## Comment 5

> Username: jeremy-murphy  
> Created_at: 2018-02-26 12:43:39 UTC  
> Url: https://github.com/boostorg/algorithm/pull/42#issuecomment-368488145  

I don't want to let a quibble about iterators get in the way; is there something you would like me to change in this PR? Or do you want me to convince you that three is actually better than four in this case?

---

## Comment 6

> Username: mclow  
> Created_at: 2018-02-27 02:10:18 UTC  
> Url: https://github.com/boostorg/algorithm/pull/42#issuecomment-368721837  

> is there something you would like me to change in this PR?  
  
If you remove the change in the interface of the algorithm - all the rest is fine.

---

## Comment 7

> Username: jeremy-murphy  
> Created_at: 2018-02-27 12:11:23 UTC  
> Url: https://github.com/boostorg/algorithm/pull/42#issuecomment-368853921  

I see. OK, let's merge the stuff we agree on and come back to the interface later.

---

## Review 8 [Commented]

> Username: jeremy-murphy  
> Created_at: 2018-02-27 12:38:22 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/algorithm/pull/42#pullrequestreview-99666227  

📁 include/boost/algorithm/apply_permutation.hpp

```diff
  49 | apply_permutation(RandomAccessIterator1 item_begin, RandomAccessIterator1 item_end,
  42 |-                   RandomAccessIterator2 ind_begin, RandomAccessIterator2 ind_end)
  50 |+                   RandomAccessIterator2 ind_begin, RandomAccessIterator2)
```

> Username: jeremy-murphy  
> Created_at: 2018-02-27 12:38:22 UTC  
> Url: https://github.com/boostorg/algorithm/pull/42#discussion_r170908068  

I have removed the fourth iterator identifier because, being unused, it causes warnings.


---

## Comment 9

> Username: mclow  
> Created_at: 2019-07-03 14:45:20 UTC  
> Url: https://github.com/boostorg/algorithm/pull/42#issuecomment-508124558  

The "de-C++11"ing of this has already been done. I committed the test changes as 0a57ec3. There's nothing else to do here.

---
