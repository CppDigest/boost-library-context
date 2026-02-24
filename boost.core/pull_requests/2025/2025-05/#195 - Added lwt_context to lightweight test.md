# #195 Added lwt_context to lightweight test [Open]

> Username: anarthal  
> Created at: 2025-05-25 10:59:54 UTC  
> Updated at: 2025-06-15 21:05:05 UTC  
> Url: https://github.com/boostorg/core/pull/195  

lwt_context makes the framework print the passed values on test failure

---

## Comment 1

> Username: anarthal  
> Created_at: 2025-05-25 11:00:42 UTC  
> Url: https://github.com/boostorg/core/pull/195#issuecomment-2907756339  

This still lacks tests and docs. Before I go ahead and write them, can you please confirm that this is (more or less) what you expected? Feedback is welcome.

---

## Review 2 [Commented]

> Username: Lastique  
> Created_at: 2025-05-25 12:16:34 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/core/pull/195#pullrequestreview-2866825336  

📁 include/boost/core/lightweight_test.hpp

```diff
 629 |+         frame_.values[0].set_value(value0);
 630 |+         frame_.values[0].set_value(value1);
 631 |+         frame_.values[0].set_value(value2);
```

> Username: Lastique  
> Created_at: 2025-05-25 12:16:34 UTC  
> Url: https://github.com/boostorg/core/pull/195#discussion_r2106183382  

`[0]`, `[1]`, `[2]`?

> Username: anarthal  
> Created_at: 2025-05-25 12:32:27 UTC  
> Url: https://github.com/boostorg/core/pull/195#discussion_r2106187628  

Completely. Will add unit tests to detect these.  
  
Does defining BOOST_LIGHTWEIGHT_TEST_OSTREAM to a global std::stringstream value sound a good approach for tests?

> Username: pdimov  
> Created_at: 2025-05-25 14:15:22 UTC  
> Url: https://github.com/boostorg/core/pull/195#discussion_r2106215077  

Sounds fine to me.


---

## Review 3 [Commented]

> Username: Lastique  
> Created_at: 2025-05-25 12:19:28 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/core/pull/195#pullrequestreview-2866825977  

📁 include/boost/core/lightweight_test.hpp

```diff
 103 |+ struct lwt_context_frame
 104 |+ {
 105 |+     static const int max_values = 3;
```

> Username: Lastique  
> Created_at: 2025-05-25 12:19:27 UTC  
> Updated_at: 2025-05-25 12:19:28 UTC  
> Url: https://github.com/boostorg/core/pull/195#discussion_r2106184092  

Is there a reason for the particular value of 3? Why have multiple values anyway, since you already maintain a list of frames?

> Username: pdimov  
> Created_at: 2025-05-25 12:27:37 UTC  
> Url: https://github.com/boostorg/core/pull/195#discussion_r2106186401  

You'd use multiple nested contexts when the test is reached via several nested functions, whereas multiple values per single context is when your current iteration has more that one value, e.g. `&p.x, &p.y` in case `p` itself doesn't have `op<<`.


---

## Comment 4

> Username: pdimov  
> Created_at: 2025-05-25 12:25:35 UTC  
> Url: https://github.com/boostorg/core/pull/195#issuecomment-2907796192  

That is what I had in mind, yes. Please enclose the printed context values in `'` for consistency with TEST_EQ.

---

## Review 5 [Commented]

> Username: pdimov  
> Created_at: 2025-05-25 14:14:35 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/core/pull/195#pullrequestreview-2866856602  

📁 include/boost/core/lightweight_test.hpp

```diff
 603 |+     // Disallow copy and movement
 604 |+     lwt_context(const lwt_context&) {};
 605 |+     lwt_context& operator=(const lwt_context&) { return *this; }
```

> Username: pdimov  
> Created_at: 2025-05-25 14:14:35 UTC  
> Updated_at: 2025-05-25 14:14:36 UTC  
> Url: https://github.com/boostorg/core/pull/195#discussion_r2106214902  

Please remove the bodies here; the C++03 idiomatic way to disable copy and assign is to declare the members as private and not define them.

> Username: Lastique  
> Created_at: 2025-05-25 15:15:47 UTC  
> Url: https://github.com/boostorg/core/pull/195#discussion_r2106230492  

Better use `BOOST_DELETED_FUNCTION`, if we intend to keep C++03 compatibility.


---
