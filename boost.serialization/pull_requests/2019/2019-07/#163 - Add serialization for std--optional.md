# #163 Add serialization for std::optional [Merged]

> Username: sdebionne  
> Created at: 2019-07-05 07:39:47 UTC  
> Updated at: 2023-09-18 19:51:28 UTC  
> Merged at: 2023-09-03 21:19:57 UTC  
> Closed at: 2023-09-03 21:19:57 UTC  
> Url: https://github.com/boostorg/serialization/pull/163  

Fixes #114 together with #148.

---

## Comment 1

> Username: robertramey  
> Created_at: 2022-09-15 20:47:46 UTC  
> Url: https://github.com/boostorg/serialization/pull/163#issuecomment-1248612965  

I don't see any tests for this.  That every "built-in" type which includes serialization code must be tested.  That is must have a test program written for it.  It's not hard - look at the rest of them.  But it is a requirement.  Looking forward to this.

---

## Comment 2

> Username: sdebionne  
> Created_at: 2022-09-21 11:42:30 UTC  
> Url: https://github.com/boostorg/serialization/pull/163#issuecomment-1253586961  

Speaking of the tests, the CI is broken since Dec 16, 2020.  The std::variant has tests but was never merged. So why should I bother?

---

## Comment 3

> Username: Ulysses1337  
> Created_at: 2023-03-30 14:48:54 UTC  
> Url: https://github.com/boostorg/serialization/pull/163#issuecomment-1490440731  

What I hope to do one day is replace all my Boost optionals with std. Can we name the bool field "initialized" for compatibility?

---

## Comment 4

> Username: robertramey  
> Created_at: 2023-09-03 21:36:55 UTC  
> Url: https://github.com/boostorg/serialization/pull/163#issuecomment-1704410864  

I merged this and pulled into my local repo.  I was considering writing a test for it.  Upon closer examination I found there is an implementation for boost::optional serialization which is almost identical to your implementation of serialization for std::optional. So it seems to me that we should include serialization for both boost::optional and std::option in the same file.  And the test for optional should be extended to include std::optional as well.  This will diminish the code which needs to be maintained in the future.  
  
As an aside, I think you should have credited the original author of the serialization for boost::optional in your header.

---

## Comment 5

> Username: sdebionne  
> Created_at: 2023-09-04 07:05:29 UTC  
> Url: https://github.com/boostorg/serialization/pull/163#issuecomment-1704724855  

Of course it is almost identical as `boost` and `std`  `optionals` have the same interfaces.  
  
> So it seems to me that we should include serialization for both boost::optional and std::option in the same file  
  
Why force people that use `std::optional` to depend on `boost::optional` via this shared header?  
  
> the test for optional should be extended to include std::optional as well  
  
Agreed but given that `std::variant` did not get merged, I did not want to spend time on adding the test here.  
  
> As an aside, I think you should have credited the original author of the serialization for boost::optional in your header.  
  
OK. I would create a PR for that but since it has been reverted in #299, I won't.

---

## Comment 6

> Username: robertramey  
> Created_at: 2023-09-18 19:51:28 UTC  
> Url: https://github.com/boostorg/serialization/pull/163#issuecomment-1724273187  

I've incorporated your working boost/serialization/optional and boost/serialization/variant.  I think you'll find this satisfactory.  Feel free to review it and ask me about it.

---
