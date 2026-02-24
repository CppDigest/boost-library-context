# #262 Implement natvis for fancy pointers [Merged]

> Username: k3DW  
> Created at: 2024-07-12 04:08:20 UTC  
> Updated at: 2024-07-15 18:37:47 UTC  
> Merged at: 2024-07-15 17:42:28 UTC  
> Closed at: 2024-07-15 17:42:28 UTC  
> Url: https://github.com/boostorg/unordered/pull/262  

Partial work on #162. This will come with a corresponding PR into boostorg/interprocess for the `offset_ptr` support, after merging this PR.

---

## Comment 1

> Username: cppalliance-bot  
> Created_at: 2024-07-12 04:17:50 UTC  
> Url: https://github.com/boostorg/unordered/pull/262#issuecomment-2224515973  

An automated preview of the documentation is available at [https://262.unordered.prtest2.cppalliance.org/libs/unordered/doc/html/unordered.html](https://262.unordered.prtest2.cppalliance.org/libs/unordered/doc/html/unordered.html)

---

## Review 2 [Commented]

> Username: cmazakas  
> Created_at: 2024-07-12 14:59:10 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/unordered/pull/262#pullrequestreview-2175125259  

📁 test/natvis_tests.cpp

```diff
 180 |+   natvis_test(offset_ptr_tester);
 181 |+ 
 182 |+ #endif // defined(BOOST_MSVC)
```

> Username: cmazakas  
> Created_at: 2024-07-12 14:59:10 UTC  
> Url: https://github.com/boostorg/unordered/pull/262#discussion_r1676067653  

One thing to consider might be refactoring this to have only one `#ifdef BOOST_MSVC` and two alternate mains.  
  
The shorter main can live at the top and have something like a `BOOST_PRAGMA_MESSAGE("skipping test for unsupported platform")`


---

## Review 3 [Commented]

> Username: cmazakas  
> Created_at: 2024-07-12 15:01:17 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/unordered/pull/262#pullrequestreview-2175129595  

📁 test/natvis_tests.cpp

```diff
  43 |+   // clang-format off
  44 |+     auto fca_map      = tester.template construct_map<boost::unordered_map>();
  45 |+     auto fca_multimap = tester.template construct_map<boost::unordered_multimap>();
```

> Username: cmazakas  
> Created_at: 2024-07-12 15:01:17 UTC  
> Url: https://github.com/boostorg/unordered/pull/262#discussion_r1676070341  

Fwiw, the way the test suite is structured now, Daniel James avoided a lot of  this kind of `template` stuff by declaring `T* p = nullptr` and then passing `p`, letting functions deduce the template param from the argument.  
  
Not 100% sure if this would actually make the code nicer here but it's something to maybe consider. So, like:  
```cpp  
boost::unordered_map* fca_map = nullptr;  
// ...  
  
auto fca_map = tester.construct_map(fca_map);  
// ...  
```  
  
I thought Daniel was pretty clever for this at the time when I started working on the codebase.

> Username: k3DW  
> Created_at: 2024-07-12 18:30:17 UTC  
> Url: https://github.com/boostorg/unordered/pull/262#discussion_r1676323084  

I don't think that design works here for 2 reasons.  
  
First, I can't create a pointer to the type without a bunch of extra mess. It would have to say something like `typename Tester::template map_type<boost::unordered_map>* fca_map = nullptr;`, which I think is uglier than just passing the class template name into `construct` directly.  
  
Secondly, because of ownership and lifetime, the 2 different `Tester` types return different kinds of pointers. For `offset_ptr_tester`, the tester itself owns the memory, so it returns a raw pointer from the `construct` functions. But for `default_tester`, it needs to return a `unique_ptr` to satisfy the same API and not leak memory. Maybe I could write something like:  
```cpp  
typename Tester::template map_pointer_type<boost::unordered_map> fca_map = nullptr;  
tester.construct_map(fca_map);  
```  
but I also think this is uglier than what I have already  
```cpp  
auto fca_map = tester.template construct_map<boost::unordered_map>();  
```  
  
I don't see any other way to make it look nicer, given that I want the code to be generic across different types of `Tester`. We may want to add another type of fancy pointer into these tests later on, and I want to make this easily extendable - just create another tester type, and the `natvis_test()` function stays exactly the same.


---

## Comment 4

> Username: cppalliance-bot  
> Created_at: 2024-07-12 18:47:50 UTC  
> Url: https://github.com/boostorg/unordered/pull/262#issuecomment-2226177296  

An automated preview of the documentation is available at [https://262.unordered.prtest2.cppalliance.org/libs/unordered/doc/html/unordered.html](https://262.unordered.prtest2.cppalliance.org/libs/unordered/doc/html/unordered.html)

---

## Comment 5

> Username: cppalliance-bot  
> Created_at: 2024-07-14 03:57:46 UTC  
> Url: https://github.com/boostorg/unordered/pull/262#issuecomment-2227186326  

An automated preview of the documentation is available at [https://262.unordered.prtest2.cppalliance.org/libs/unordered/doc/html/unordered.html](https://262.unordered.prtest2.cppalliance.org/libs/unordered/doc/html/unordered.html)

---

## Comment 6

> Username: cppalliance-bot  
> Created_at: 2024-07-14 17:52:44 UTC  
> Url: https://github.com/boostorg/unordered/pull/262#issuecomment-2227427581  

An automated preview of the documentation is available at [https://262.unordered.prtest2.cppalliance.org/libs/unordered/doc/html/unordered.html](https://262.unordered.prtest2.cppalliance.org/libs/unordered/doc/html/unordered.html)

---

## Review 7 [Approved]

> Username: joaquintides  
> Created_at: 2024-07-15 08:00:43 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/unordered/pull/262#pullrequestreview-2177079280  

No further comments on my side.

---
