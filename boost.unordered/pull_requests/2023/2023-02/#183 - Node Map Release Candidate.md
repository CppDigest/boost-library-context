# #183 Node Map Release Candidate [Merged]

> Username: cmazakas  
> Created at: 2023-02-14 18:59:37 UTC  
> Updated at: 2023-02-22 16:40:04 UTC  
> Merged at: 2023-02-22 16:40:00 UTC  
> Closed at: 2023-02-22 16:40:00 UTC  
> Url: https://github.com/boostorg/unordered/pull/183  



---

## Review 1 [Commented]

> Username: joaquintides  
> Created_at: 2023-02-17 18:03:46 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/unordered/pull/183#pullrequestreview-1302962718  

Besides inlined comments:  
* ~~Add your name to the copyright notices in `foa.hpp`.~~  
* ~~The description of `foa::table` starting at L1114 has to be updated to reflect changes in type policies. I can do this if you wish.~~  
* ~~`max_load_tests` does not have (C) and license.~~ Maybe you could scan all files in the project?

📁 include/boost/unordered/detail/foa/node_handle.hpp

```diff
  48 |+     node_value_type* p_=nullptr;
  49 |+     BOOST_ATTRIBUTE_NO_UNIQUE_ADDRESS opt_storage<Allocator> a_;
  50 |+ 
```

> Username: joaquintides  
> Created_at: 2023-02-17 09:07:09 UTC  
> Updated_at: 2023-02-17 18:03:46 UTC  
> Url: https://github.com/boostorg/unordered/pull/183#discussion_r1109491935  

Do we need to have `BOOST_ATTRIBUTE_NO_UNIQUE_ADDRESS` here and within `opt_storage`?

> Username: cmazakas  
> Created_at: 2023-02-17 20:04:38 UTC  
> Updated_at: 2023-02-17 20:04:39 UTC  
> Url: https://github.com/boostorg/unordered/pull/183#discussion_r1110303256  

It seems like it, yes.  
  
I've been playing around here and the sizeof() check only passes when it's in both places  
  
https://godbolt.org/z/Pah5n94Pa

---

📁 include/boost/unordered/detail/foa/node_handle.hpp

```diff
  88 |+ 
  89 |+     void clear()
  90 |+     {
```

> Username: joaquintides  
> Created_at: 2023-02-17 09:11:04 UTC  
> Updated_at: 2023-02-17 18:03:46 UTC  
> Url: https://github.com/boostorg/unordered/pull/183#discussion_r1109495807  

Nitpick: `reset` may be a better name than `clear`, as this function does not destroy the element.

---

📁 include/boost/unordered/detail/foa/node_handle.hpp

```diff
 180 |+       }
 181 |+     }
 182 |+ 
```

> Username: joaquintides  
> Created_at: 2023-02-17 10:21:09 UTC  
> Updated_at: 2023-02-17 18:03:46 UTC  
> Url: https://github.com/boostorg/unordered/pull/183#discussion_r1109579423  

`swap` implementation is correct, but a little messy, with some `if` clauses returning, some others not returning, and extra checking of empty conditions. Consider the following straightforward refactoring:  
```cpp  
    void swap(node_handle_base& nh) noexcept(  
      boost::allocator_is_always_equal<Allocator>::type::value||  
      boost::allocator_propagate_on_container_swap<Allocator>::type::value)  
    {  
      if (empty()){  
        if(nh.empty()){           /*  empty(),  nh.empty() */  
          /* nothing to do*/  
        }else{                    /*  empty(), !nh.empty() */  
          emplace(nh.p_,nh.al());  
          nh.clear();  
        }  
      }else{  
        if(nh.empty()){           /* !empty(),  nh.empty() */  
          nh.emplace(p_,al());  
          clear();  
        }else{                    /* !empty(), !nh.empty() */  
          bool const pocs=  
            boost::allocator_propagate_on_container_swap<Allocator>::type::value;  
          BOOST_ASSERT(pocs || al()==nh.al());  
  
          using std::swap;  
          swap(p_,nh.p_);  
          if(pocs) swap(al(),nh.al());  
        }  
      }  
    }  
```

---

📁 include/boost/unordered/detail/foa/node_handle.hpp

```diff
 134 |+       return *this;
 135 |+     }
 136 |+ 
```

> Username: joaquintides  
> Created_at: 2023-02-17 10:26:30 UTC  
> Updated_at: 2023-02-17 18:05:08 UTC  
> Url: https://github.com/boostorg/unordered/pull/183#discussion_r1109586883  

To increase legibility, consider refactoring `operator=` in the same way as my proposal for `swap` above.


📁 include/boost/unordered/detail/foa.hpp

```diff
 927 |   {
 924 |-     using alloc_traits=boost::allocator_traits<Allocator>;
 928 |+     // using alloc_traits=boost::allocator_traits<Allocator>;
```

> Username: joaquintides  
> Created_at: 2023-02-17 10:30:13 UTC  
> Updated_at: 2023-02-17 18:03:46 UTC  
> Url: https://github.com/boostorg/unordered/pull/183#discussion_r1109592213  

Remove commented-out code.

---

📁 include/boost/unordered/detail/foa.hpp

```diff
 959 |-     using alloc_traits=boost::allocator_traits<Allocator>;
 967 |+     using storage_alloc=typename boost::allocator_rebind<Allocator,Value>::type;
 968 |+     using alloc_traits=boost::allocator_traits<storage_alloc>;
```

> Username: joaquintides  
> Created_at: 2023-02-17 10:33:11 UTC  
> Updated_at: 2023-02-17 18:03:46 UTC  
> Url: https://github.com/boostorg/unordered/pull/183#discussion_r1109596335  

Name this `storage_traits` as in `new_` (or the other way around).

---

📁 include/boost/unordered/detail/foa.hpp

```diff
1607 |+     table&   x;
1608 |+     iterator it;
1609 |+     bool     rollback_=false;
```

> Username: joaquintides  
> Created_at: 2023-02-17 10:40:20 UTC  
> Updated_at: 2023-02-17 18:03:46 UTC  
> Url: https://github.com/boostorg/unordered/pull/183#discussion_r1109605855  

Consider defining `it` as a `const_iterator` so that we don't have to const cast in `extract`.

---

📁 include/boost/unordered/detail/foa.hpp

```diff
1393 |+      * inserted. For immovable types, we instead dispatch to the routine that
1394 |+      * unconditionally allocates via `type_policy::construct()`.
1395 |+      */
```

> Username: joaquintides  
> Created_at: 2023-02-17 10:52:45 UTC  
> Updated_at: 2023-02-17 18:03:46 UTC  
> Url: https://github.com/boostorg/unordered/pull/183#discussion_r1109622053  

Typos: refernce, csae

---

📁 include/boost/unordered/detail/foa.hpp

```diff
1385 |-     return emplace_impl(emplace_type(std::forward<Args>(args)...));
1398 |+         value_type,
1399 |+         emplace_type<Args...>&&>{},
```

> Username: joaquintides  
> Created_at: 2023-02-17 10:53:12 UTC  
> Updated_at: 2023-02-17 18:03:46 UTC  
> Url: https://github.com/boostorg/unordered/pull/183#discussion_r1109622672  

I think this should be `emplace_type<Args&&...>&&`.

> Username: cmazakas  
> Created_at: 2023-02-20 03:49:46 UTC  
> Url: https://github.com/boostorg/unordered/pull/183#discussion_r1111430677  

I'm not 100% sure on this.   
  
I think we're covered because `is_constructible` is required to check `std::declval<Arg>()` so we passively should have rvalue references builtin.  
  
If we can narrow down a problematic case here, that'd be great.

> Username: joaquintides  
> Created_at: 2023-02-20 11:06:26 UTC  
> Updated_at: 2023-02-20 11:06:27 UTC  
> Url: https://github.com/boostorg/unordered/pull/183#discussion_r1111796974  

Can't narrow down (and I tried) so I guess we're good.

---

📁 include/boost/unordered/detail/foa.hpp

```diff
1874 | 
1875 |+   template<typename... Args>
1876 |+   BOOST_FORCEINLINE std::pair<iterator,bool> emplace_dispatch(
```

> Username: joaquintides  
> Created_at: 2023-02-17 10:55:40 UTC  
> Updated_at: 2023-02-17 18:03:46 UTC  
> Url: https://github.com/boostorg/unordered/pull/183#discussion_r1109626037  

I don't particular like the name `emplace_dispatch`, as it conveys no meaning. Maybe `emplace_value`?

---

📁 include/boost/unordered/detail/foa.hpp

```diff
1875 |+   template<typename... Args>
1876 |+   BOOST_FORCEINLINE std::pair<iterator,bool> emplace_dispatch(
1877 |+     std::true_type,Args&&... args
```

> Username: joaquintides  
> Created_at: 2023-02-17 10:59:53 UTC  
> Updated_at: 2023-02-17 18:03:46 UTC  
> Url: https://github.com/boostorg/unordered/pull/183#discussion_r1109631527  

Please annotate what `std::true_type` means here, for instance:  
```cpp  
std::true_type /* movable value_type */,Args&&... args  
```

---

📁 include/boost/unordered/detail/foa.hpp

```diff
1883 |+   template<typename... Args>
1884 |+   BOOST_FORCEINLINE std::pair<iterator,bool> emplace_dispatch(
1885 |+     std::false_type,Args&&... args
```

> Username: joaquintides  
> Created_at: 2023-02-17 11:01:46 UTC  
> Updated_at: 2023-02-17 18:03:47 UTC  
> Url: https://github.com/boostorg/unordered/pull/183#discussion_r1109634054  

Same as above:  
```cpp  
std::false_type /* inmovable value_type */,Args&&... args  
```


📁 include/boost/unordered/unordered_flat_map.hpp

```diff
  62 |+ 
  63 |+         template <class A>
  64 |+         static void construct(A& al, element_type* p, moved_type&& x)
```

> Username: joaquintides  
> Created_at: 2023-02-17 11:29:19 UTC  
> Updated_at: 2023-02-17 18:03:47 UTC  
> Url: https://github.com/boostorg/unordered/pull/183#discussion_r1109668703  

Do we need this `construct` overload? The general one below seems to fit the bill.

> Username: cmazakas  
> Created_at: 2023-02-20 04:04:04 UTC  
> Url: https://github.com/boostorg/unordered/pull/183#discussion_r1111435621  

Ha, good catch. I guess these were leftover from failed refactoring attempts. These shouldn't have been included.


📁 include/boost/unordered/unordered_flat_set.hpp

```diff
  53 |+ 
  54 |+         template <class A>
  55 |+         static void construct(A& al, element_type* p, Key&& x)
```

> Username: joaquintides  
> Created_at: 2023-02-17 11:30:13 UTC  
> Updated_at: 2023-02-17 18:03:47 UTC  
> Url: https://github.com/boostorg/unordered/pull/183#discussion_r1109669817  

Do we need these two `construct` overloads? The general one below seems to fit the bill.


📁 include/boost/unordered/unordered_node_map.hpp

```diff
  55 |+            * optimizations when copying the tables
  56 |+            */
  57 |+           element_type() = default;
```

> Username: joaquintides  
> Created_at: 2023-02-17 16:32:33 UTC  
> Updated_at: 2023-02-17 18:03:47 UTC  
> Url: https://github.com/boostorg/unordered/pull/183#discussion_r1110048661  

Why do we need it `element_type` default ctor?  
Edit: I just saw it's needed in `extract`, so it's fine.

> Username: cmazakas  
> Created_at: 2023-02-20 04:07:56 UTC  
> Url: https://github.com/boostorg/unordered/pull/183#discussion_r1111436814  

I believe from previous conversations we've had that it's to keep the type trivially default-constructible as well. Though I can't seem to recall the specifics of why.  
  
Maybe a self-documenting static_assert() is in order?

> Username: joaquintides  
> Created_at: 2023-02-20 11:20:19 UTC  
> Updated_at: 2023-02-20 11:20:20 UTC  
> Url: https://github.com/boostorg/unordered/pull/183#discussion_r1111813232  

Well, we use the default constructor in, for instance, [here](https://github.com/boostorg/unordered/blob/unordered_node_map_rc/include/boost/unordered/unordered_node_map.hpp#L405), so we're good.

---

📁 include/boost/unordered/unordered_node_map.hpp

```diff
  78 |+ 
  79 |+         static element_type&& move(element_type& x) { return std::move(x); }
  80 |+         static moved_type move(value_type& x)
```

> Username: joaquintides  
> Created_at: 2023-02-17 16:33:50 UTC  
> Updated_at: 2023-02-17 18:10:36 UTC  
> Url: https://github.com/boostorg/unordered/pull/183#discussion_r1110050416  

Do we need this `move` overload?  
Edit: oh, I think it's needed for move assignment under unequal allocators, care confirm?

> Username: cmazakas  
> Created_at: 2023-02-20 04:09:55 UTC  
> Updated_at: 2023-02-20 04:10:20 UTC  
> Url: https://github.com/boostorg/unordered/pull/183#discussion_r1111437533  

Correct. Allocator-based move construction is also included in this.  
  
We need it everywhere:  
```cpp  
type_policy::move(type_policy::value_from(*p))  
```  
appears.

---

📁 include/boost/unordered/unordered_node_map.hpp

```diff
 116 |+         }
 117 |+ 
 118 |+         template <class A> static void destroy(A& al, value_type* p) noexcept
```

> Username: joaquintides  
> Created_at: 2023-02-17 16:34:56 UTC  
> Updated_at: 2023-02-17 18:03:47 UTC  
> Url: https://github.com/boostorg/unordered/pull/183#discussion_r1110051527  

Do we need this `destroy` overload other than the fact it's called from `destroy(A& al, element_type* p)`? If not needed, consider removing it and moving its code into the other oveload.

> Username: cmazakas  
> Created_at: 2023-02-20 04:20:14 UTC  
> Url: https://github.com/boostorg/unordered/pull/183#discussion_r1111441155  

Hmm, seems like we do for the implementation of the `node_handle`.  
```cpp  
~node_handle_base()  
{  
  if(!empty()){  
    type_policy::destroy(al(),p_);  
    a_.t_.~Allocator();  
  }  
}  
```  
If the handle is non-empty, we use the policy to properly deallocate the object and its storage.

> Username: joaquintides  
> Created_at: 2023-02-20 11:22:04 UTC  
> Url: https://github.com/boostorg/unordered/pull/183#discussion_r1111816067  

Ok

---

📁 include/boost/unordered/unordered_node_map.hpp

```diff
 133 |+       };
 134 |+ 
 135 |+       template <class NodeMapTypes, class Allocator>
```

> Username: joaquintides  
> Created_at: 2023-02-17 16:55:26 UTC  
> Updated_at: 2023-02-17 18:31:39 UTC  
> Url: https://github.com/boostorg/unordered/pull/183#discussion_r1110075884  

I don't have a strong opinion on this, but calling `NodeMapTypes` to what's called `TypePolicy` in other places may be confusing to the reader. On the other hand, not every type policy can be fed to `node_map_handle`, so...

> Username: cmazakas  
> Created_at: 2023-02-20 04:24:03 UTC  
> Url: https://github.com/boostorg/unordered/pull/183#discussion_r1111442416  

It's a good idea to get the naming in line here. We'll just go with `TypePolicy`.

---

📁 include/boost/unordered/unordered_node_map.hpp

```diff
 423 |+         BOOST_ASSERT(get_allocator() == nh.get_allocator());
 424 |+ 
 425 |+         auto itp = table_.insert(map_types::move(nh.element()));
```

> Username: joaquintides  
> Created_at: 2023-02-17 17:16:40 UTC  
> Updated_at: 2023-02-17 18:03:47 UTC  
> Url: https://github.com/boostorg/unordered/pull/183#discussion_r1110103130  

If insertion is successful, `nh` will be empty yet its allocator won't be destroyed (I think).

> Username: cmazakas  
> Created_at: 2023-02-20 05:07:14 UTC  
> Url: https://github.com/boostorg/unordered/pull/183#discussion_r1111459439  

Actually, what's worse is that here we wind up allocating a new node!  
  
`nh.element()` returns a `value_type&`:  
```cpp  
private:  
  using node_value_type=typename type_policy::value_type;  
  
protected:  
  node_value_type& element()noexcept  
  {  
    BOOST_ASSERT(!empty());  
    return *p_;  
  }  
```  
I've updated the test suite to catch this.


📁 include/boost/unordered/unordered_node_set.hpp

```diff
  98 |+         }
  99 |+ 
 100 |+         template <class A> static void destroy(A& al, value_type* p) noexcept
```

> Username: joaquintides  
> Created_at: 2023-02-17 17:52:28 UTC  
> Updated_at: 2023-02-17 18:03:47 UTC  
> Url: https://github.com/boostorg/unordered/pull/183#discussion_r1110148671  

Same as with `unordered_node_map`.

---

📁 include/boost/unordered/unordered_node_set.hpp

```diff
 115 |+       };
 116 |+ 
 117 |+       template <class NodeSetTypes, class Allocator>
```

> Username: joaquintides  
> Created_at: 2023-02-17 17:52:46 UTC  
> Updated_at: 2023-02-17 18:03:47 UTC  
> Url: https://github.com/boostorg/unordered/pull/183#discussion_r1110149018  

Same as with `unordered_node_map`.

---

📁 include/boost/unordered/unordered_node_set.hpp

```diff
 411 |+         BOOST_ASSERT(get_allocator() == nh.get_allocator());
 412 |+ 
 413 |+         auto itp = table_.insert(set_types::move(nh.element()));
```

> Username: joaquintides  
> Created_at: 2023-02-17 17:55:20 UTC  
> Updated_at: 2023-02-17 18:03:47 UTC  
> Url: https://github.com/boostorg/unordered/pull/183#discussion_r1110151908  

Same as with `unordered_node_map`.


---

## Review 2 [Commented]

> Username: joaquintides  
> Created_at: 2023-02-20 11:14:44 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/unordered/pull/183#pullrequestreview-1305566678  

📁 include/boost/unordered/unordered_flat_set.hpp

```diff
  47 |+ 
  48 |+         template <class A>
  49 |+         static void construct(A& al, element_type* p, element_type const& copy)
```

> Username: joaquintides  
> Created_at: 2023-02-20 11:14:44 UTC  
> Url: https://github.com/boostorg/unordered/pull/183#discussion_r1111805194  

I think this overload is also not needed.


---

## Comment 3

> Username: codecov[bot]  
> Created_at: 2023-02-22 03:24:40 UTC  
> Url: https://github.com/boostorg/unordered/pull/183#issuecomment-1439385862  

# [Codecov](https://codecov.io/gh/boostorg/unordered/pull/183?src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
> Merging [#183](https://codecov.io/gh/boostorg/unordered/pull/183?src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (63a564e) into [develop](https://codecov.io/gh/boostorg/unordered/commit/0597463902a4d1ea85045e754facc3f1318ce269?el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (0597463) will **increase** coverage by `0.17%`.  
> The diff coverage is `99.04%`.  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/unordered/pull/183/graphs/tree.svg?width=650&height=150&src=pr&token=ZqRPZlJZ5N&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://codecov.io/gh/boostorg/unordered/pull/183?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@             Coverage Diff             @@  
##           develop     #183      +/-   ##  
===========================================  
+ Coverage    97.72%   97.90%   +0.17%       
===========================================  
  Files           85       89       +4       
  Lines        12720    13777    +1057       
===========================================  
+ Hits         12431    13488    +1057       
  Misses         289      289                
```  
  
  
| [Impacted Files](https://codecov.io/gh/boostorg/unordered/pull/183?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [test/exception/swap\_exception\_tests.cpp](https://codecov.io/gh/boostorg/unordered/pull/183?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-dGVzdC9leGNlcHRpb24vc3dhcF9leGNlcHRpb25fdGVzdHMuY3Bw) | `79.48% <ø> (ø)` | |  
| [test/unordered/copy\_tests.cpp](https://codecov.io/gh/boostorg/unordered/pull/183?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-dGVzdC91bm9yZGVyZWQvY29weV90ZXN0cy5jcHA=) | `100.00% <ø> (ø)` | |  
| [test/unordered/erase\_tests.cpp](https://codecov.io/gh/boostorg/unordered/pull/183?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-dGVzdC91bm9yZGVyZWQvZXJhc2VfdGVzdHMuY3Bw) | `97.54% <ø> (ø)` | |  
| [test/unordered/find\_tests.cpp](https://codecov.io/gh/boostorg/unordered/pull/183?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-dGVzdC91bm9yZGVyZWQvZmluZF90ZXN0cy5jcHA=) | `100.00% <ø> (ø)` | |  
| [test/unordered/incomplete\_test.cpp](https://codecov.io/gh/boostorg/unordered/pull/183?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-dGVzdC91bm9yZGVyZWQvaW5jb21wbGV0ZV90ZXN0LmNwcA==) | `58.62% <ø> (ø)` | |  
| [test/unordered/max\_load\_tests.cpp](https://codecov.io/gh/boostorg/unordered/pull/183?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-dGVzdC91bm9yZGVyZWQvbWF4X2xvYWRfdGVzdHMuY3Bw) | `100.00% <ø> (ø)` | |  
| [test/unordered/move\_tests.cpp](https://codecov.io/gh/boostorg/unordered/pull/183?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-dGVzdC91bm9yZGVyZWQvbW92ZV90ZXN0cy5jcHA=) | `100.00% <ø> (ø)` | |  
| [test/unordered/post\_move\_tests.cpp](https://codecov.io/gh/boostorg/unordered/pull/183?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-dGVzdC91bm9yZGVyZWQvcG9zdF9tb3ZlX3Rlc3RzLmNwcA==) | `100.00% <ø> (ø)` | |  
| [include/boost/unordered/detail/foa/node\_handle.hpp](https://codecov.io/gh/boostorg/unordered/pull/183?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC91bm9yZGVyZWQvZGV0YWlsL2ZvYS9ub2RlX2hhbmRsZS5ocHA=) | `93.15% <93.15%> (ø)` | |  
| [test/unordered/rehash\_tests.cpp](https://codecov.io/gh/boostorg/unordered/pull/183?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-dGVzdC91bm9yZGVyZWQvcmVoYXNoX3Rlc3RzLmNwcA==) | `99.12% <95.83%> (-0.27%)` | :arrow_down: |  
| ... and [46 more](https://codecov.io/gh/boostorg/unordered/pull/183?src=pr&el=tree-more&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/unordered/pull/183?src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/unordered/pull/183?src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [0597463...63a564e](https://codecov.io/gh/boostorg/unordered/pull/183?src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>

---
