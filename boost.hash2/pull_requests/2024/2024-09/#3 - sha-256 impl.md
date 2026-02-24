# #3 sha-256 impl [Merged]

> Username: cmazakas  
> Created at: 2024-09-25 18:16:14 UTC  
> Updated at: 2024-09-26 04:25:00 UTC  
> Merged at: 2024-09-26 00:43:28 UTC  
> Closed at: 2024-09-26 00:43:28 UTC  
> Url: https://github.com/boostorg/hash2/pull/3  

This will form the base of all the future sha-2 algorithms

---

## Review 1 [Commented]

> Username: pdimov  
> Created_at: 2024-09-25 18:36:00 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/hash2/pull/3#pullrequestreview-2329191912  

📁 include/boost/hash2/sha2.hpp

```diff
   1 |+ #ifndef SHA2_HPP_INCLUDED
```

> Username: pdimov  
> Created_at: 2024-09-25 18:36:00 UTC  
> Url: https://github.com/boostorg/hash2/pull/3#discussion_r1775769874  

Include guard should be BOOST_HASH2_SHA2_HPP_INCLUDED.


---

## Review 2 [Commented]

> Username: pdimov  
> Created_at: 2024-09-25 18:36:18 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/hash2/pull/3#pullrequestreview-2329192428  

📁 test/sha2.cpp

```diff
  17 |+   for( std::size_t i = 0; i < N; ++i )
  18 |+   {
  19 |+ 		char buffer[ 4 ];
```

> Username: pdimov  
> Created_at: 2024-09-25 18:36:17 UTC  
> Updated_at: 2024-09-25 18:36:18 UTC  
> Url: https://github.com/boostorg/hash2/pull/3#discussion_r1775770231  

Tabs.


---

## Review 3 [Commented]

> Username: pdimov  
> Created_at: 2024-09-25 18:36:48 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/hash2/pull/3#pullrequestreview-2329193289  

📁 test/sha2.cpp

```diff
  13 |+ template<std::size_t N> std::string to_string( std::array<unsigned char, N> const & v )
  14 |+ {
  15 |+   std::string r;
```

> Username: pdimov  
> Created_at: 2024-09-25 18:36:48 UTC  
> Url: https://github.com/boostorg/hash2/pull/3#discussion_r1775770747  

Indent 2?


---
