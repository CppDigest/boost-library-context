# #196 Adapt bimaps::relation::structured_pair, container::detail::pair, and compressed_pair [Open]

> Username: CromwellEnage  
> Created at: 2018-09-30 18:37:56 UTC  
> Updated at: 2020-10-06 01:32:20 UTC  
> Url: https://github.com/boostorg/fusion/pull/196  

Recognize boost::bimaps::relation::structured_pair, boost::container::detail::pair, and boost::compressed_pair as Fusion sequences.

---

## Comment 1

> Username: djowel  
> Created_at: 2020-10-06 01:21:22 UTC  
> Url: https://github.com/boostorg/fusion/pull/196#issuecomment-703973908  

Awesome!

---

## Comment 2

> Username: djowel  
> Created_at: 2020-10-06 01:24:39 UTC  
> Url: https://github.com/boostorg/fusion/pull/196#issuecomment-703974745  

I suppose it makes sense to merge this. I have a request though: would you take care of support related issues related to these additions?

---

## Review 3 [Changes requested]

> Username: djowel  
> Created_at: 2020-10-06 01:32:20 UTC  
> State: CHANGES_REQUESTED  
> Url: https://github.com/boostorg/fusion/pull/196#pullrequestreview-502524909  

📁 include/boost/fusion/adapted.hpp

```diff
   6 |+     (See accompanying file LICENSE_1_0.txt or copy at
   7 |+     http://www.boost.org/LICENSE_1_0.txt)
   8 |+ ============================================================================*/
```

> Username: djowel  
> Created_at: 2020-10-06 01:27:23 UTC  
> Updated_at: 2020-10-06 01:32:20 UTC  
> Url: https://github.com/boostorg/fusion/pull/196#discussion_r499957138  

superfluous edit?

---

📁 include/boost/fusion/adapted.hpp

```diff
  16 | #include <boost/fusion/adapted/mpl.hpp>
  17 | #include <boost/fusion/adapted/std_pair.hpp>
  18 |+ #include <boost/fusion/adapted/boost_bimap_pair.hpp>
```

> Username: djowel  
> Created_at: 2020-10-06 01:28:39 UTC  
> Updated_at: 2020-10-06 01:32:20 UTC  
> Url: https://github.com/boostorg/fusion/pull/196#discussion_r499957468  

Thinking about it some more, perhaps it's good to have a preprocessor define for bringing these in? Perhaps initially undefined. E.g. BOOST_FUSION_ADAPT_BIMAP


---
