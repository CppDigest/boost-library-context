# #1184 - `http::basic_fields::equal_range` should return `boost::range::iterator_range` [Open]

> Username: cmazakas  
> Created at: 2018-07-11 16:53:05 UTC  
> Updated at: 2022-09-25 00:28:13 UTC  
> Url: https://github.com/boostorg/beast/issues/1184  

In terms of improving Beast's semantic meaning, using a [`std::pair`](https://www.boost.org/doc/libs/1_67_0/libs/beast/doc/html/beast/ref/boost__beast__http__basic_fields/equal_range.html) as the return type for `basic_fields::equal_range` can lead to a hard-to-use API.  
  
It's suggested that Beast instead return what's in Boost.Range for better interop with existing Boost libs.

---

## Comment 1

> Username: vinniefalco  
> Created at: 2018-08-10 18:59:08 UTC  
> Url: https://github.com/boostorg/beast/issues/1184#issuecomment-412175261  

I rather not drag in Boost.Range just for one function in all of Beast. An alternative would be to roll a small range style class.

---

## Comment 2

> Username: stale[bot]  
> Created at: 2018-09-09 18:59:24 UTC  
> Url: https://github.com/boostorg/beast/issues/1184#issuecomment-419737005  

This issue has been open for a while with no activity, has it been resolved?

---

## Comment 3

> Username: klemens-morgenstern  
> Created at: 2022-09-24 06:13:49 UTC  
> Url: https://github.com/boostorg/beast/issues/1184#issuecomment-1256877313  

I wouldn't do it - this is the common pattern in all multiset/maps even today.  https://en.cppreference.com/w/cpp/container/multiset/equal_range  
  
boost.range can adopt a pair into an iterator_range so I think consistency is better./

---

## Comment 4

> Username: vinniefalco  
> Created at: 2022-09-25 00:28:13 UTC  
> Url: https://github.com/boostorg/beast/issues/1184#issuecomment-1257090211  

I agree in principle, iterator pair is annoying. Boost.URL and Boost.Http-Proto both have `find_all`, e.g.:  
  
All query parameters with a matching key:  
```  
subrange params::find_all( pct_string_view key );  
```  
  
All HTTP field values with a matching name:  
```  
subrange fields_view_base::find_all( string_view name );  
```  
  
`subrange` is a _ForwardRange_ with custom iterators that are convertible to the base container iterator.  
  
If we do this for Beast, we should not change the current API but instead add a new one, e.g. `find_all`.  
  
However, I don't see any particular pressing need for it.
