# #581 Fix C++20 clang 10 warning -Wdeprecated-anon-enum-enum-conversion in … [Merged]

> Username: Romain-Geissler-1A  
> Created at: 2020-03-19 16:50:44 UTC  
> Updated at: 2020-03-24 16:47:42 UTC  
> Merged at: 2020-03-24 16:47:42 UTC  
> Closed at: 2020-03-24 16:47:42 UTC  
> Url: https://github.com/boostorg/spirit/pull/581  

…output_iterator.hpp  
  
```  
In file included from /data/mwrep/res/osp/Boost/20-0-0-0/include/boost/spirit/include/karma.hpp:18:  
In file included from /data/mwrep/res/osp/Boost/20-0-0-0/include/boost/spirit/home/karma.hpp:15:  
In file included from /data/mwrep/res/osp/Boost/20-0-0-0/include/boost/spirit/home/karma/char.hpp:15:  
In file included from /data/mwrep/res/osp/Boost/20-0-0-0/include/boost/spirit/home/karma/char/char.hpp:25:  
In file included from /data/mwrep/res/osp/Boost/20-0-0-0/include/boost/spirit/home/karma/char/char_generator.hpp:18:  
In file included from /data/mwrep/res/osp/Boost/20-0-0-0/include/boost/spirit/home/karma/detail/generate_to.hpp:17:  
/data/mwrep/res/osp/Boost/20-0-0-0/include/boost/spirit/home/karma/detail/output_iterator.hpp:391:25: error: bitwise operation between different enumeration types ('boost::spirit::k  
arma::detail::make_output_iterator<std::back_insert_iterator<std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> > >, mpl_::int_<3>, boost::spirit::unused_  
type>::(anonymous enum at /data/mwrep/res/osp/Boost/20-0-0-0/include/boost/spirit/home/karma/detail/output_iterator.hpp:388:9)' and 'boost::spirit::karma::generator_properties::enum  
_type') is deprecated [-Werror,-Wdeprecated-anon-enum-enum-conversion]  
            (properties & generator_properties::tracking) ? true : false  
             ~~~~~~~~~~ ^ ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
```  
  
  
  
I am actually unsure "int" is the right type to use here. If for some reason you really want to keep the anonymous enum, then in all operation where you use the "&" operator static_cast<int>(properties) works too.

---

## Review 1 [Commented]

> Username: Kojoley  
> Created_at: 2020-03-22 15:00:29 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/spirit/pull/581#pullrequestreview-379013631  

📁 include/boost/spirit/home/karma/detail/output_iterator.hpp

```diff
 385 | 
 386 |-         enum { properties = Properties::value };
 386 |+         static const int properties = Properties::value;
```

> Username: Kojoley  
> Created_at: 2020-03-22 15:00:29 UTC  
> Updated_at: 2020-03-22 18:53:04 UTC  
> Url: https://github.com/boostorg/spirit/pull/581#discussion_r396103239  

Try making it `generator_properties::enum_type` instead of `int`.

> Username: Romain-Geissler-1A  
> Created_at: 2020-03-22 18:48:38 UTC  
> Updated_at: 2020-03-22 18:53:04 UTC  
> Url: https://github.com/boostorg/spirit/pull/581#discussion_r396127633  

I just did. Waiting for the CI to confirm it didn't break anything.

> Username: Romain-Geissler-1A  
> Created_at: 2020-03-22 18:52:16 UTC  
> Updated_at: 2020-03-22 18:53:04 UTC  
> Url: https://github.com/boostorg/spirit/pull/581#discussion_r396127941  

Apparently it doesn't work without a static cast, so I will add it.  
  
I am actually unsure you actually want the `enum_type` or the `std:: underlying_type_t<enum_type>` given that in `Properties::value` you won't have actual enum values, but a conjunction of enum value bitwise-and'ed together. As you wish.


---
