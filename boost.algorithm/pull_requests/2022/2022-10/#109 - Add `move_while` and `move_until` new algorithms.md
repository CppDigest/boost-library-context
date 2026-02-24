# #109 Add `move_while` and `move_until` new algorithms [Open]

> Username: denzor200  
> Created at: 2022-10-17 14:15:54 UTC  
> Updated at: 2022-10-25 11:43:04 UTC  
> Url: https://github.com/boostorg/algorithm/pull/109  

Hello, i have an interest to be contributor of this library. I decided to start with this small PR to get experience and to receive feedback and "ping response" from community.

---

## Review 1 [Commented]

> Username: denzor200  
> Created_at: 2022-10-17 14:23:18 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/algorithm/pull/109#pullrequestreview-1144259229  

📁 include/boost/algorithm/cxx11/move_if.hpp

```diff
  31 |+ /// \param p        A predicate for testing the elements of the range
  32 |+ /// \note           C++11-compatible compiler required.
  33 |+ template<typename InputIterator, typename OutputIterator, typename Predicate>
```

> Username: denzor200  
> Created_at: 2022-10-17 14:22:16 UTC  
> Updated_at: 2022-10-17 14:23:18 UTC  
> Url: https://github.com/boostorg/algorithm/pull/109#discussion_r997128110  

rename concept's name. `InputIterator` is not enough here

> Username: denzor200  
> Created_at: 2022-10-24 21:25:40 UTC  
> Url: https://github.com/boostorg/algorithm/pull/109#discussion_r1003783816  

It's not a truth, implementation of `std::move` algorithm from libstdc++ says that `InputIterator` is enough here:  
https://github.com/gcc-mirror/gcc/blob/releases/gcc-12.2.0/libstdc++-v3/include/bits/stl_algobase.h#L647

> Username: denzor200  
> Created_at: 2022-10-24 21:26:32 UTC  
> Url: https://github.com/boostorg/algorithm/pull/109#discussion_r1003784420  

Conversation was resolved without any changes


📁 doc/algorithm.qbk

```diff
 151 | 
 152 | 
 153 |+ [section:Move Variations on Move]
```

> Username: denzor200  
> Created_at: 2022-10-17 14:22:38 UTC  
> Updated_at: 2022-10-17 14:23:18 UTC  
> Url: https://github.com/boostorg/algorithm/pull/109#discussion_r997128543  

perform `chmod` on this file

> Username: denzor200  
> Created_at: 2022-10-24 21:13:58 UTC  
> Url: https://github.com/boostorg/algorithm/pull/109#discussion_r1003774983  

Fixed by `chmod 644 doc/algorithm.qbk && chmod 644 include/boost/algorithm/cxx11/move_if.hpp && chmod 644 test/Jamfile.v2 && chmod 644 test/move_if_test1.cpp`


---

## Review 2 [Commented]

> Username: denzor200  
> Created_at: 2022-10-20 14:28:40 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/algorithm/pull/109#pullrequestreview-1149355234  

📁 test/move_if_test1.cpp

```diff
 133 |+         const std::size_t mcount = std::count_if(c.begin (), c.end (), is_moved());
 134 |+         
 135 |+         std::cout << "moved: " << mcount << std::endl;
```

> Username: denzor200  
> Created_at: 2022-10-20 14:20:22 UTC  
> Updated_at: 2022-10-20 14:28:40 UTC  
> Url: https://github.com/boostorg/algorithm/pull/109#discussion_r1000700765  

`mcount` is always zero here

---

📁 test/move_if_test1.cpp

```diff
 148 |+         const std::size_t mcount = std::count_if(c.begin (), c.end (), is_moved());
 149 |+         
 150 |+         std::cout << "moved: " << mcount << std::endl;
```

> Username: denzor200  
> Created_at: 2022-10-20 14:20:32 UTC  
> Updated_at: 2022-10-20 14:28:40 UTC  
> Url: https://github.com/boostorg/algorithm/pull/109#discussion_r1000700981  

`mcount` is always zero here

---

📁 test/move_if_test1.cpp

```diff
 151 |+         
 152 |+         BOOST_CHECK ( is_even()(e.front()) ? mcount != 0 : mcount == 0 );
 153 |+         BOOST_CHECK ( v.size () == (size_t) std::distance ( c.begin (), it ));
```

> Username: denzor200  
> Created_at: 2022-10-20 14:28:36 UTC  
> Updated_at: 2022-10-20 14:28:40 UTC  
> Url: https://github.com/boostorg/algorithm/pull/109#discussion_r1000714106  

also, compare `v.size()` with `mcount` (here and also in 4 similar places)


---

## Comment 3

> Username: denzor200  
> Created_at: 2022-10-24 21:59:23 UTC  
> Url: https://github.com/boostorg/algorithm/pull/109#issuecomment-1289671387  

CI is broken, so this PR tested here: https://godbolt.org/z/b4vGMnK55

---

## Comment 4

> Username: jgopel  
> Created_at: 2022-10-24 23:02:17 UTC  
> Url: https://github.com/boostorg/algorithm/pull/109#issuecomment-1289741248  

**question:** Forgive my ignorance - why not just use a `std::move_iterator`? What does this accomplish that that doesn't already do?

---

## Comment 5

> Username: denzor200  
> Created_at: 2022-10-25 11:43:04 UTC  
> Url: https://github.com/boostorg/algorithm/pull/109#issuecomment-1290416315  

> why not just use a std::move_iterator?  
  
I had same question for `std::move`, but it exists. Look:  
```  
std::move(v.begin(), v.end(), std::back_inserter(l));  
```  
does the same result as:  
```  
std::copy(std::make_move_iterator(v.begin()),  
          std::make_move_iterator(v.end()),  
          std::back_inserter(l));  
```  
  
  
  
> What does this accomplish that that doesn't already do?  
  
just readability, and those people who use `boost::algorithm::copy_while` and `boost::algorithm::copy_until` also need for `move_while` and `move_until`.

---
