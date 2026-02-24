# #119 Fix all gcc-13 -Wmaybe-uninitialized warnings [Closed]

> Username: cmazakas  
> Created at: 2023-12-21 22:52:25 UTC  
> Updated at: 2025-04-08 18:01:34 UTC  
> Closed at: 2025-04-08 18:01:34 UTC  
> Url: https://github.com/boostorg/optional/pull/119  

The test suite generates quite a few `-Wmaybe-uninitialized` warnings with gcc-13.  
  
I've applied judicious uses of launder() to fix these.  
  
I had to update the union to use an explicit constructor,destructor so that I could call it from the `none` constructor overload which also silenced another uninitialized warning in the code.

---

## Comment 1

> Username: akrzemi1  
> Created_at: 2023-12-22 12:01:33 UTC  
> Url: https://github.com/boostorg/optional/pull/119#issuecomment-1867605545  

Thanks for the PR.  
It looks like it introduces a certain pessimization: it seems to require a write of a value to the trivial object that is never read in a program that uses `boost::opitonal` in contract.  
  
In the past I consistently rejected such pessimizaitons. The docs have a section on this false positive: https://www.boost.org/doc/libs/1_84_0/libs/optional/doc/html/boost_optional/tutorial/gotchas/false_positive_with__wmaybe_uninitialized.html  
  
When I look at the test matrix, which uses gcc 13, I cannot see this watning in the test suite. Is it possible to provide a small Compiler Explorer example that demonstrates the warning?

---

## Comment 2

> Username: cmazakas  
> Created_at: 2023-12-22 15:38:35 UTC  
> Url: https://github.com/boostorg/optional/pull/119#issuecomment-1867824655  

> It looks like it introduces a certain pessimization: it seems to require a write of a value to the trivial object that is never read in a program that uses boost::opitonal in contract.  
  
I'm having trouble parsing this but I'm assuming this is referring to the addition of the constructor/destructor to the `union` and its default initialization in the `none` constructor overload.  
  
The union here shouldn't be initializing the actual wrapped T, this is just communicating to the abstract machine that it's safe to copy.  
  
> When I look at the test matrix, which uses gcc 13,   
  
Which test matrix is this? I didn't see gcc-13 in the CI.  
  
To recreate locally, all I did was:  
```bash  
./b2 libs/optional/test warnings=pedantic cxxflags="-Werror=maybe-uninitialized" variant=release  
```

---

## Review 3 [Commented]

> Username: akrzemi1  
> Created_at: 2023-12-26 20:14:40 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/optional/pull/119#pullrequestreview-1796572731  

📁 include/boost/optional/detail/optional_trivially_copyable_base.hpp

```diff
  41 |       :
  42 |-       m_initialized(false) {}
  42 |+       m_initialized(false), m_storage() {}
```

> Username: akrzemi1  
> Created_at: 2023-12-26 20:14:40 UTC  
> Url: https://github.com/boostorg/optional/pull/119#discussion_r1436599919  

This adds a write that will never be read.

> Username: cmazakas  
> Created_at: 2023-12-27 15:29:35 UTC  
> Url: https://github.com/boostorg/optional/pull/119#discussion_r1437109241  

Ah, I understand now. I thought you were still using the `union` here as a form of aligned_storage.  
  
There is a write here because `m_storage` is actually a `T`, not a `union { T t_ }`  
  
You know what this means? It means that the `maybe-uninitialized` warning truly is genuine then!  
  
If an optional<tc> is constructed with none and then copied, we're technically copying uninitialized values.  
  
My first instinct would be to simply update this code to store a `union` but I'm not sure what the goals of this trivially copyable base specialization is.

> Username: cmazakas  
> Created_at: 2023-12-27 16:27:23 UTC  
> Url: https://github.com/boostorg/optional/pull/119#discussion_r1437139703  

I think I have an idea:  
```cpp  
struct zst{};  
union tc_storage { T t_; zst z_; };  
  
  private :  
  
    bool m_initialized ;  
    tc_storage m_storage ;  
```  
  
Then whenever we need default constructibility, we just do: `m_storage.z_={};`  
  
Exactly like this:  
```cpp  
tc_optional_base ( none_t )  
  :  
  m_initialized(false) {  
    m_storage.z_={};  
  }  
```


---

## Comment 4

> Username: akrzemi1  
> Created_at: 2023-12-27 22:00:41 UTC  
> Url: https://github.com/boostorg/optional/pull/119#issuecomment-1870653361  

For some background, the goal of the trivially copyable base is to make `optional<T>` trivially copyable when `T` is trivial.

---

## Comment 5

> Username: akrzemi1  
> Created_at: 2023-12-28 20:25:57 UTC  
> Updated_at: 2023-12-28 20:28:56 UTC  
> Url: https://github.com/boostorg/optional/pull/119#issuecomment-1871468905  

I am answering an earlier quesiton. I do not have gcc 13 on my machine. The only place where I can test it is either Boost test matrix:  
https://www.boost.org/development/tests/develop/developer/optional.html  
  
Or Compiler Explorer:  
https://godbolt.org/z/Eb9on46eY  
  
And they do not confirm this. A Compiler Explorer link would be helpful here. Or any link, for that matter.

---

## Comment 6

> Username: akrzemi1  
> Created_at: 2023-12-28 20:59:40 UTC  
> Url: https://github.com/boostorg/optional/pull/119#issuecomment-1871485685  

Ok, I can see similar warnings in gcc 12.

---

## Comment 7

> Username: akrzemi1  
> Created_at: 2023-12-29 00:45:59 UTC  
> Url: https://github.com/boostorg/optional/pull/119#issuecomment-1871648839  

Ok, so I pushed a commit with most of your changes: https://github.com/boostorg/optional/commit/e31cf6f2a8be437330f4547561df5ee8a62e4187  
I left behind the constructor and the destructor for the union, as this didn't seem to fix any warnings in GCC 12.

---

## Comment 8

> Username: cmazakas  
> Created_at: 2023-12-29 17:08:28 UTC  
> Url: https://github.com/boostorg/optional/pull/119#issuecomment-1872224827  

> Ok, so I pushed a commit with most of your changes: [e31cf6f](https://github.com/boostorg/optional/commit/e31cf6f2a8be437330f4547561df5ee8a62e4187) I left behind the constructor and the destructor for the union, as this didn't seem to fix any warnings in GCC 12.  
  
Awesome!  
  
I still wanna update your CI. I had most of a working prototype there but I hit issues with the install command and the package list.  
  
Out of curiosity, why do we need libpython-dev? That package in particular seemed to make the most trouble in the later containers that we'd need for latest gcc.

---

## Comment 9

> Username: akrzemi1  
> Created_at: 2023-12-29 21:51:27 UTC  
> Url: https://github.com/boostorg/optional/pull/119#issuecomment-1872354576  

> I still wanna update your CI. I had most of a working prototype there but I hit issues with the install command and the package list.  
  
I would be most grateful for that. Maybe do it via a separate PR?  
  
> Out of curiosity, why do we need libpython-dev? That package in particular seemed to make the most trouble in the later containers that we'd need for latest gcc.  
  
I am not acquainted with the new CI mechanisms (I rely on the Boost Test Matrix). Kind people, like yourself, file CI-related PRs and I usually accept them without understanding. It is likely that libpython-dev may not be needed.

---

## Comment 10

> Username: cmazakas  
> Created_at: 2025-04-08 18:01:34 UTC  
> Url: https://github.com/boostorg/optional/pull/119#issuecomment-2787261344  

can't believe I still had this open  
Closing now!

---
