# #1147 Several fixes of errors and warnings [Merged]

> Username: vissarion  
> Created at: 2023-05-17 11:07:45 UTC  
> Updated at: 2023-06-16 14:03:01 UTC  
> Merged at: 2023-05-19 11:51:30 UTC  
> Closed at: 2023-05-19 11:51:30 UTC  
> Url: https://github.com/boostorg/geometry/pull/1147  

This PR fixes several warnings and compile errors. ~It also upgrades circleCI docker image.~

---

## Review 1 [Commented]

> Username: vissarion  
> Created_at: 2023-05-17 11:10:44 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/1147#pullrequestreview-1430396748  

📁 include/boost/geometry/index/detail/rtree/query_iterators.hpp

```diff
 295 |     }
 296 | 
 297 |-     query_iterator(query_iterator && o)
```

> Username: vissarion  
> Created_at: 2023-05-17 11:10:44 UTC  
> Url: https://github.com/boostorg/geometry/pull/1147#discussion_r1196326466  

@awulkiew this is just my quick fix to make tests passing.

> Username: awulkiew  
> Created_at: 2023-05-17 13:28:53 UTC  
> Url: https://github.com/boostorg/geometry/pull/1147#discussion_r1196525875  

@vissarion I don't think that this is necessary. Which test doesn't pass?

> Username: vissarion  
> Created_at: 2023-05-17 13:35:20 UTC  
> Updated_at: 2023-05-17 13:57:20 UTC  
> Url: https://github.com/boostorg/geometry/pull/1147#discussion_r1196535549  

I am getting the following compile error  
```  
In file included from test/algorithms/within/within_gc.cpp:10:  
In file included from test/algorithms/within/test_within.hpp:24:  
In file included from ../../boost/geometry/algorithms/covered_by.hpp:24:  
In file included from ../../boost/geometry/algorithms/detail/covered_by/implementation.hpp:26:  
In file included from ../../boost/geometry/algorithms/detail/within/implementation.hpp:30:  
In file included from ../../boost/geometry/algorithms/detail/within/multi_point.hpp:34:  
In file included from ../../boost/geometry/index/rtree.hpp:89:  
../../boost/geometry/index/detail/rtree/query_iterators.hpp:289:11: error: call to constructor of 'boost::geometry::index::detail::rtree::iterators::query_iterator<std::pair<boost::geometry::model::box<boost::geometry::model::point<double, 2, boost::geometry::cs::cartesian>>, unsigned long>, boost::geometry::index::detail::rtree::allocators<boost::container::new_allocator<std::pair<boost::geometry::model::box<boost::geometry::model::point<double, 2, boost::geometry::cs::cartesian>>, unsigned long>>, std::pair<boost::geometry::model::box<boost::geometry::model::point<double, 2, boost::geometry::cs::cartesian>>, unsigned long>, boost::geometry::index::parameters<boost::geometry::index::rstar<4, 1, 1, 32>, boost::geometry::strategies::relate::cartesian<>>, boost::geometry::model::box<boost::geometry::model::point<double, 2, boost::geometry::cs::cartesian>>, boost::geometry::index::detail::rtree::node_variant_static_tag>>::iterator_ptr' (aka 'unique_ptr<query_iterator_base<std::pair<boost::geometry::model::box<boost::geometry::model::point<double, 2, boost::geometry::cs::cartesian>>, unsigned long>, boost::geometry::index::detail::rtree::allocators<boost::container::new_allocator<std::pair<boost::geometry::model::box<boost::geometry::model::point<double, 2, boost::geometry::cs::cartesian>>, unsigned long>>, std::pair<boost::geometry::model::box<boost::geometry::model::point<double, 2, boost::geometry::cs::cartesian>>, unsigned long>, boost::geometry::index::parameters<boost::geometry::index::rstar<4, 1, 1, 32>, boost::geometry::strategies::relate::cartesian<void>>, boost::geometry::model::box<boost::geometry::model::point<double, 2, boost::geometry::cs::cartesian>>, boost::geometry::index::detail::rtree::node_variant_static_tag>>>') is ambiguous  
        : m_ptr(0)  
```

> Username: awulkiew  
> Created_at: 2023-05-17 14:16:07 UTC  
> Url: https://github.com/boostorg/geometry/pull/1147#discussion_r1196594449  

Right, I can reproduce it with gcc-6. It is caused by the replacement of `scoped_ptr` with `unique_ptr`. So direct solution would be to pass `nullptr` instead.  
  
But the solution you prosed is even better (almost). AFAIU your intention was to force the compiler to generate move ctor automatically. But since copy ctor and assignements are defined AFAIR this will not happen. Instead copy ctor will be called which will harm the performance. Instead move ctor should be declared as `default`. And since we're at it I think move assignment operator could be `default` as well, hence:  
```  
query_iterator(query_iterator&&) = default;  
query_iterator& operator=(query_iterator &&) = default;  
```

> Username: awulkiew  
> Created_at: 2023-05-17 14:23:43 UTC  
> Updated_at: 2023-05-17 14:23:44 UTC  
> Url: https://github.com/boostorg/geometry/pull/1147#discussion_r1196605723  

Btw, `0` is also passed into `unique_ptr` in copy ctor and assignment.  
Would you like me to prepare a PR with a fix?  
  
There is also another bug in rtree.hpp, in experimental function:  
```  
boost\geometry\index\rtree.hpp(1300): error C2440: '<function-style-cast>': cannot convert from 'initializer list' to 'boost::geometry::index::detail::rtree::iterators::distance_query_iterator<boost::geometry::index::rtree<Value,Parameters,I,E,A>::members_holder,Predicates>'  
```  
where line 1300 has to be replaced with:  
```  
return query_iterator_t<Predicates>(m_members, predicates);  
```

> Username: vissarion  
> Created_at: 2023-05-17 15:03:11 UTC  
> Url: https://github.com/boostorg/geometry/pull/1147#discussion_r1196665540  

Sure, why not. Do we still need the current PR. Either we close it and you fix the issues there or we merge it and you create a PR with additional fixes. I am OK with both.   
  
Also there are some tests related to envelop that are failing but I want to fix them in a separate PR.

> Username: awulkiew  
> Created_at: 2023-05-17 15:06:03 UTC  
> Updated_at: 2023-05-17 15:06:04 UTC  
> Url: https://github.com/boostorg/geometry/pull/1147#discussion_r1196670525  

I propose to merge this PR.


---

## Review 2 [Commented]

> Username: vissarion  
> Created_at: 2023-05-17 11:13:50 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/1147#pullrequestreview-1430403239  

📁 .circleci/config.yml

```diff
  22 |-       # gcc-6.0, ruby-2.3, sudo, curl
  23 |-     - image: circleci/ruby:2.3
  22 |+       # ubuntu 22.04.2 LTS, build-essential, gcc-11
```

> Username: vissarion  
> Created_at: 2023-05-17 11:13:50 UTC  
> Url: https://github.com/boostorg/geometry/pull/1147#discussion_r1196331224  

the main reason for this upgrade is to fix the error   
  
```  
./boost/geometry/algorithms/detail/buffer/buffered_piece_collection.hpp:408:39: error: ‘check_linear_endpoints’ was not declared in this scope  
```  
  
that is related to a gcc-6 bug. See https://gcc.gnu.org/bugzilla/show_bug.cgi?id=71546

> Username: awulkiew  
> Created_at: 2023-05-17 13:33:51 UTC  
> Updated_at: 2023-05-17 13:33:52 UTC  
> Url: https://github.com/boostorg/geometry/pull/1147#discussion_r1196532998  

Gcc-6 does support C++14 so I don't think that bumping version of the compiler that is used for testing is the correct solution. Furthermore I'm not sure that this bug is the correct one.  
  
A workaround would be to call the member function explicitly writing `this` keyword.

> Username: vissarion  
> Created_at: 2023-05-17 13:54:16 UTC  
> Updated_at: 2023-05-17 13:54:17 UTC  
> Url: https://github.com/boostorg/geometry/pull/1147#discussion_r1196562686  

I though it was a good opportunity to also upgrade the circleCI. Anyway, I am OK with the workaround and I remove the circleCI update from this PR. BTW do we want to do that upgrade in a different PR?

> Username: awulkiew  
> Created_at: 2023-05-17 14:31:43 UTC  
> Url: https://github.com/boostorg/geometry/pull/1147#discussion_r1196617792  

Well it depends, do we want to catch such issues in the future?

> Username: vissarion  
> Created_at: 2023-05-17 15:07:01 UTC  
> Updated_at: 2023-05-17 15:07:02 UTC  
> Url: https://github.com/boostorg/geometry/pull/1147#discussion_r1196672242  

Good point. On the other hand we may miss issues that caught by newer versions. BTW, does it make sense to allow github actions run the full test-suite instead of the minimal one?

> Username: awulkiew  
> Created_at: 2023-05-18 08:59:39 UTC  
> Url: https://github.com/boostorg/geometry/pull/1147#discussion_r1197579011  

Yes, and my guess is that more users compile with newer compiler and if that's true then testing it would be more important but I don't know really.  
  
Regarding GH actions. I can't remember why have we decided to do it this way. Probably becasue running all of them would take a lot of time. But there may be some technical difficulties. I think the discussions should be in relevant PR. We should also check if there are some slots distributed across all of the Boost submodules because with long tests we'd take slots from other libraries.


---

## Comment 3

> Username: awulkiew  
> Created_at: 2023-05-17 13:27:21 UTC  
> Updated_at: 2023-05-17 14:17:46 UTC  
> Url: https://github.com/boostorg/geometry/pull/1147#issuecomment-1551398357  

Oops, it seems I fixed them without creating a PR in these commits:  
https://github.com/boostorg/geometry/commit/20fc428bf3bdf3e07f24d0b159c3701b1ba5ee6e  
https://github.com/boostorg/geometry/commit/4bae6ef471e3ef006c6709c79bc293a13ea488ff  
Sorry, it's an old habbit.

---

## Comment 4

> Username: awulkiew  
> Created_at: 2023-05-17 14:30:17 UTC  
> Url: https://github.com/boostorg/geometry/pull/1147#issuecomment-1551511464  

It seems that there are still some missing includes:  
```  
libs/geometry/test/views/reversible_closeable.cpp:130:16: error: ‘trim_copy’ is not a member of ‘boost’  
```  
I only checked algorithms, probably as you did. :)

---

## Comment 5

> Username: vissarion  
> Created_at: 2023-05-17 14:59:48 UTC  
> Url: https://github.com/boostorg/geometry/pull/1147#issuecomment-1551560200  

I tried to clean up a bit with a rebase.

---

## Comment 6

> Username: vissarion  
> Created_at: 2023-05-19 07:54:41 UTC  
> Url: https://github.com/boostorg/geometry/pull/1147#issuecomment-1554187930  

@barendgehrels are you OK with merging this PR?

---

## Review 7 [Approved]

> Username: barendgehrels  
> Created_at: 2023-05-19 11:30:42 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/geometry/pull/1147#pullrequestreview-1434263753  

Sure, OK for me. Thanks!

---
