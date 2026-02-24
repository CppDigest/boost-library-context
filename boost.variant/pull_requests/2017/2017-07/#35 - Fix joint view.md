# #35 Fix joint view [Merged]

> Username: very-cool-name  
> Created at: 2017-07-04 18:01:02 UTC  
> Updated at: 2017-07-07 07:54:45 UTC  
> Merged at: 2017-07-07 07:54:45 UTC  
> Closed at: 2017-07-07 07:54:45 UTC  
> Url: https://github.com/boostorg/variant/pull/35  

make_over_sequence works if use mpl::list instead of vector. Speeding up compilation using specification for mpl::vector<Args...> conflicts with old mpl::vector and does not work - so it was abandoned.

---

## Review 1 [Changes requested]

> Username: apolukhin  
> Created_at: 2017-07-04 19:57:53 UTC  
> State: CHANGES_REQUESTED  
> Url: https://github.com/boostorg/variant/pull/35#pullrequestreview-47919903  

Awesome!  
  
But please address the comments.

📁 include/boost/variant/variant.hpp

```diff
  88 | #include <boost/mpl/sizeof.hpp>
  89 | #include <boost/mpl/transform.hpp>
  90 |+ #include <boost/mpl/vector.hpp>
```

> Username: apolukhin  
> Created_at: 2017-07-04 19:41:24 UTC  
> Updated_at: 2017-07-06 09:39:13 UTC  
> Url: https://github.com/boostorg/variant/pull/35#discussion_r125523618  

Looks like boost/mpl/vector.hpp header is not required

---

📁 include/boost/variant/variant.hpp

```diff
2456 | 
2457 |     BOOST_STATIC_ASSERT(( ::boost::mpl::is_sequence<Types>::value ));
2458 |+     typedef typename mpl::copy<Types, boost::mpl::front_inserter<mpl::list<> > >::type copied_sequence_t;
```

> Username: apolukhin  
> Created_at: 2017-07-04 19:43:19 UTC  
> Updated_at: 2017-07-06 09:39:13 UTC  
> Url: https://github.com/boostorg/variant/pull/35#discussion_r125523782  

Include header for used boost::mpl::* metafunctions.

---

📁 include/boost/variant/variant.hpp

```diff
2456 | 
2457 |     BOOST_STATIC_ASSERT(( ::boost::mpl::is_sequence<Types>::value ));
2458 |+     typedef typename mpl::copy<Types, boost::mpl::front_inserter<mpl::list<> > >::type copied_sequence_t;
```

> Username: apolukhin  
> Created_at: 2017-07-04 19:47:40 UTC  
> Updated_at: 2017-07-06 09:39:13 UTC  
> Url: https://github.com/boostorg/variant/pull/35#discussion_r125524106  

This will reverse the sequence. Many people use `boost::get` function with indexes (not with types) and such change in behavior will break their code. Also add a test case for that case (make sure that after `typedef boost::make_variant_over<boost::mpl::joint_view<v1::types, v2::types>::type>::type v3;` the expression `get<0>` returns an `int`)  
  
Try using something like this:  
  
```  
typedef typename boost::mpl::insert_range<  
      boost::mpl::list<>  
    , boost::mpl::end< boost::mpl::list<> >::type  
    , Types  
    >::type copied_sequence_t;  
```

> Username: very-cool-name  
> Created_at: 2017-07-05 09:59:01 UTC  
> Updated_at: 2017-07-06 09:39:13 UTC  
> Url: https://github.com/boostorg/variant/pull/35#discussion_r125602384  

I see  `get<int>` for tuple, but not for variant. Where is it defined?

> Username: apolukhin  
> Created_at: 2017-07-05 20:04:18 UTC  
> Updated_at: 2017-07-06 09:39:13 UTC  
> Url: https://github.com/boostorg/variant/pull/35#discussion_r125743343  

Oops... `std::get<0>` exists only in C++17 Standard and in my plans for the `boost::variant`.  
  
However, there is a `boost::variant::which()` function that allows you to get the index of a stored value:  
```  
BOOST_CHECK(boost::get<int>(c) == 1);  
BOOST_CHECK(c.which() == 0);  
  
v3 d = b;  
BOOST_CHECK(boost::get<std::string>(d) == "2");  
BOOST_CHECK(d.which() == 1);  
```  
`which()` will report different values if `make_variant_over` reverses the sequence :(


📁 test/variant_over_joint_view_test.cpp

```diff
  24 |+         boost::get<T>(v);
  25 |+         BOOST_FAIL("Expected exception boost::bad_get, but got nothing.");
  26 |+     } catch (boost::bad_get& exc) { //okay it is expected behaviour
```

> Username: apolukhin  
> Created_at: 2017-07-04 19:49:12 UTC  
> Updated_at: 2017-07-06 09:39:13 UTC  
> Url: https://github.com/boostorg/variant/pull/35#discussion_r125524216  

Comment out `exc`. Otherwise it will produce warnings.


---
