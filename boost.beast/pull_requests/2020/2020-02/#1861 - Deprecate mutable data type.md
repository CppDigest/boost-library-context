# #1861 Deprecate mutable data type [Closed]

> Username: madmongo1  
> Created at: 2020-02-26 10:12:51 UTC  
> Updated at: 2020-02-29 22:32:23 UTC  
> Closed at: 2020-02-29 22:32:23 UTC  
> Url: https://github.com/boostorg/beast/pull/1861  

Refactor dynamic buffers to ensure conformance with DynamicBuffers_v1 interface

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2020-02-26 10:25:58 UTC  
> Updated_at: 2020-02-28 18:13:29 UTC  
> Url: https://github.com/boostorg/beast/pull/1861#issuecomment-591353140  

# [Codecov](https://codecov.io/gh/boostorg/beast/pull/1861?src=pr&el=h1) Report  
> Merging [#1861](https://codecov.io/gh/boostorg/beast/pull/1861?src=pr&el=desc) into [develop](https://codecov.io/gh/boostorg/beast/commit/c058567ec1b6b767343706cdc58f45e69d491dda&el=desc) will **decrease** coverage by `0.05%`.  
> The diff coverage is `n/a`.  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/beast/pull/1861/graphs/tree.svg?width=650&height=150&src=pr&token=Gq6MFA9JRF)](https://codecov.io/gh/boostorg/beast/pull/1861?src=pr&el=tree)  
  
```diff  
@@             Coverage Diff             @@  
##           develop    #1861      +/-   ##  
===========================================  
- Coverage    95.17%   95.11%   -0.06%       
===========================================  
  Files          156      156                
  Lines        11942    12004      +62       
===========================================  
+ Hits         11366    11418      +52       
- Misses         576      586      +10       
```  
  
  
| [Impacted Files](https://codecov.io/gh/boostorg/beast/pull/1861?src=pr&el=tree) | Coverage Δ | |  
|---|---|---|  
| [include/boost/beast/core/buffers\_adaptor.hpp](https://codecov.io/gh/boostorg/beast/pull/1861/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC9jb3JlL2J1ZmZlcnNfYWRhcHRvci5ocHA=) | `100.00% <0.00%> (ø)` | :arrow_up: |  
| [include/boost/beast/core/impl/multi\_buffer.hpp](https://codecov.io/gh/boostorg/beast/pull/1861/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC9jb3JlL2ltcGwvbXVsdGlfYnVmZmVyLmhwcA==) | `98.13% <0.00%> (-1.87%)` | :arrow_down: |  
| [include/boost/beast/core/impl/buffers\_adaptor.hpp](https://codecov.io/gh/boostorg/beast/pull/1861/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC9jb3JlL2ltcGwvYnVmZmVyc19hZGFwdG9yLmhwcA==) | `98.60% <0.00%> (-1.40%)` | :arrow_down: |  
| [include/boost/beast/zlib/detail/deflate\_stream.ipp](https://codecov.io/gh/boostorg/beast/pull/1861/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC96bGliL2RldGFpbC9kZWZsYXRlX3N0cmVhbS5pcHA=) | `88.60% <0.00%> (+0.10%)` | :arrow_up: |  
| [include/boost/beast/websocket/impl/ping.hpp](https://codecov.io/gh/boostorg/beast/pull/1861/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC93ZWJzb2NrZXQvaW1wbC9waW5nLmhwcA==) | `97.32% <0.00%> (+0.89%)` | :arrow_up: |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/beast/pull/1861?src=pr&el=continue).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/beast/pull/1861?src=pr&el=footer). Last update [c058567...5d7be2f](https://codecov.io/gh/boostorg/beast/pull/1861?src=pr&el=lastupdated). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments).

---

## Review 2 [Commented]

> Username: vinniefalco  
> Created_at: 2020-02-27 18:05:36 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/1861#pullrequestreview-365881385  

📁 include/boost/beast/core/detail/buffer_utils.hpp

```diff
  49 |+                     (std::min)(max_len, target.size()));
  50 |+     return target;
  51 |+ }
```

> Username: vinniefalco  
> Created_at: 2020-02-27 18:05:35 UTC  
> Updated_at: 2020-02-28 17:17:52 UTC  
> Url: https://github.com/boostorg/beast/pull/1861#discussion_r385279755  

This goes in `boost/beast/core/detail/buffer.hpp` and it should be two inline function overloads, no templates


---

## Review 3 [Commented]

> Username: vinniefalco  
> Created_at: 2020-02-27 18:07:10 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/1861#pullrequestreview-365882358  

📁 include/boost/beast/core/impl/buffers_adaptor.hpp

```diff
 253 |-         : b_(&b)
 148 |+     struct iterator_category
 149 |+         : std::bidirectional_iterator_tag
```

> Username: vinniefalco  
> Created_at: 2020-02-27 18:07:10 UTC  
> Updated_at: 2020-02-28 17:17:52 UTC  
> Url: https://github.com/boostorg/beast/pull/1861#discussion_r385280513  

why not  
```  
using iterator_category = std::bidirectional_iterator_tag;  
```  
?


---

## Review 4 [Commented]

> Username: vinniefalco  
> Created_at: 2020-02-27 18:08:44 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/1861#pullrequestreview-365883275  

📁 include/boost/beast/core/impl/buffers_adaptor.hpp

```diff
 442 |+ buffers_adaptor<MutableBufferSequence>::
 443 |+ make_subrange(std::size_t pos, std::size_t n)
 444 |+ -> subrange<true>
```

> Username: vinniefalco  
> Created_at: 2020-02-27 18:08:43 UTC  
> Updated_at: 2020-02-28 17:17:52 UTC  
> Url: https://github.com/boostorg/beast/pull/1861#discussion_r385281264  

Continuation lines shuold should be indented...  
```  
template<class MutableBufferSequence>  
auto  
buffers_adaptor<MutableBufferSequence>::  
make_subrange(  
    std::size_t pos, std::size_t n) ->  
        subrange<true>  
```


---

## Review 5 [Commented]

> Username: vinniefalco  
> Created_at: 2020-02-27 18:13:30 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/1861#pullrequestreview-365886435  

📁 include/boost/beast/core/impl/buffers_adaptor.hpp

```diff
  76 |-         : b_(other.b_)
  82 |+     // allow conversion from mutable to const
  83 |+     template<bool thisIsMutable = isMutable,
```

> Username: vinniefalco  
> Created_at: 2020-02-27 18:13:29 UTC  
> Updated_at: 2020-02-28 17:17:52 UTC  
> Url: https://github.com/boostorg/beast/pull/1861#discussion_r385283683  

I usually just throw an underscore in the mix (or remove it), e.g. `isMutable_`


---

## Review 6 [Commented]

> Username: vinniefalco  
> Created_at: 2020-02-27 18:14:12 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/1861#pullrequestreview-365886888  

📁 include/boost/beast/core/impl/buffers_adaptor.hpp

```diff
  82 |+     // allow conversion from mutable to const
  83 |+     template<bool thisIsMutable = isMutable,
  84 |+         typename std::enable_if<!thisIsMutable>::type * = nullptr>
```

> Username: vinniefalco  
> Created_at: 2020-02-27 18:14:12 UTC  
> Updated_at: 2020-02-28 17:17:52 UTC  
> Url: https://github.com/boostorg/beast/pull/1861#discussion_r385284034  

I would put `typename` on the previous line, since it adds less than one bit of information. Not a showstopper though.


---

## Review 7 [Commented]

> Username: vinniefalco  
> Created_at: 2020-02-27 18:15:43 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/1861#pullrequestreview-365887897  

📁 include/boost/beast/core/impl/buffers_adaptor.hpp

```diff
 144 |-     }
 104 |+     iter_type
 105 |+     first() const;
```

> Username: vinniefalco  
> Created_at: 2020-02-27 18:15:43 UTC  
> Updated_at: 2020-02-28 17:17:52 UTC  
> Url: https://github.com/boostorg/beast/pull/1861#discussion_r385284832  

`get_buffer`, `first`, `last`, `first_offset`, and `last_size` should not be public, since they are not public interfaces.


---

## Review 8 [Commented]

> Username: vinniefalco  
> Created_at: 2020-02-27 18:16:57 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/1861#pullrequestreview-365888706  

📁 include/boost/beast/core/impl/buffers_adaptor.hpp

```diff
 144 |+ struct buffers_adaptor<MutableBufferSequence>::
 145 |+ subrange<isMutable>::
 146 |+ iterator
```

> Username: vinniefalco  
> Created_at: 2020-02-27 18:16:57 UTC  
> Updated_at: 2020-02-28 17:17:52 UTC  
> Url: https://github.com/boostorg/beast/pull/1861#discussion_r385285492  

Formatting  
```  
template<bool isMutable>  
struct buffers_adaptor<MutableBufferSequence>::  
    subrange<isMutable>::  
    iterator  
```


---

## Review 9 [Commented]

> Username: vinniefalco  
> Created_at: 2020-02-27 18:17:34 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/1861#pullrequestreview-365889082  

📁 include/boost/beast/core/impl/buffers_adaptor.hpp

```diff
 152 |+     using value_type = typename buffers_adaptor<MutableBufferSequence>::
 153 |+     template subrange<isMutable>::
 154 |+     value_type;
```

> Username: vinniefalco  
> Created_at: 2020-02-27 18:17:34 UTC  
> Updated_at: 2020-02-28 17:17:52 UTC  
> Url: https://github.com/boostorg/beast/pull/1861#discussion_r385285798  

Continuation lines have to be indented:  
```  
    using value_type = typename  
        buffers_adaptor<MutableBufferSequence>:: template  
        subrange<isMutable>::value_type;  
```


---

## Review 10 [Commented]

> Username: vinniefalco  
> Created_at: 2020-02-27 18:18:14 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/1861#pullrequestreview-365889505  

📁 include/boost/beast/core/impl/buffers_adaptor.hpp

```diff
 154 |+     value_type;
 155 |+     using reference = typename std::add_lvalue_reference<value_type>::type;
 156 |+     using pointer = typename std::add_pointer<value_type>::type;
```

> Username: vinniefalco  
> Created_at: 2020-02-27 18:18:14 UTC  
> Updated_at: 2020-02-28 17:17:52 UTC  
> Url: https://github.com/boostorg/beast/pull/1861#discussion_r385286131  

There's no need for using `add_pointer` or `add_lvalue_reference` why did you do that?


---

## Review 11 [Commented]

> Username: vinniefalco  
> Created_at: 2020-02-27 18:19:10 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/1861#pullrequestreview-365890097  

📁 include/boost/beast/core/impl/buffers_adaptor.hpp

```diff
 159 |+     iterator(
 160 |+         typename buffers_adaptor<MutableBufferSequence>::
 161 |+         template subrange<isMutable> const *parent,
```

> Username: vinniefalco  
> Created_at: 2020-02-27 18:19:10 UTC  
> Updated_at: 2020-02-28 17:17:52 UTC  
> Url: https://github.com/boostorg/beast/pull/1861#discussion_r385286603  

Continuation lines should be indented  
```  
    iterator(typename  
        buffers_adaptor<MutableBufferSequence>::template  
            subrange<isMutable> const *parent,  
        iter_type it);  
```


---

## Review 12 [Commented]

> Username: vinniefalco  
> Created_at: 2020-02-27 18:19:46 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/1861#pullrequestreview-365890490  

📁 include/boost/beast/core/impl/buffers_adaptor.hpp

```diff
 171 | 
 299 |-     pointer
 300 |-     operator->() const = delete;
```

> Username: vinniefalco  
> Created_at: 2020-02-27 18:19:45 UTC  
> Updated_at: 2020-02-28 17:17:52 UTC  
> Url: https://github.com/boostorg/beast/pull/1861#discussion_r385286913  

Why did you remove this?

> Username: madmongo1  
> Created_at: 2020-02-27 22:30:25 UTC  
> Updated_at: 2020-02-28 17:17:52 UTC  
> Url: https://github.com/boostorg/beast/pull/1861#discussion_r385409386  

the iterator is now a subtype of subrange. It wasn't that I deleted this specifically, I just didn't write it. What is the purpose of this deleted function?

> Username: vinniefalco  
> Created_at: 2020-02-27 23:48:42 UTC  
> Updated_at: 2020-02-28 17:17:52 UTC  
> Url: https://github.com/boostorg/beast/pull/1861#discussion_r385435943  

to inform the user that the omission is intended (`operator->` is unimplementable)


---

## Review 13 [Commented]

> Username: vinniefalco  
> Created_at: 2020-02-27 18:22:13 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/1861#pullrequestreview-365892164  

📁 include/boost/beast/core/impl/buffers_adaptor.hpp

```diff
 161 |-     {
 162 |-         ++it_;
 163 |-         return *this;
```

> Username: vinniefalco  
> Created_at: 2020-02-27 18:22:12 UTC  
> Updated_at: 2020-02-28 17:17:52 UTC  
> Url: https://github.com/boostorg/beast/pull/1861#discussion_r385288201  

Why are the function definitions moved?

> Username: madmongo1  
> Created_at: 2020-02-27 22:32:55 UTC  
> Updated_at: 2020-02-28 17:17:52 UTC  
> Url: https://github.com/boostorg/beast/pull/1861#discussion_r385410438  

the enture class has been replaced.


---

## Review 14 [Commented]

> Username: vinniefalco  
> Created_at: 2020-02-27 18:22:37 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/1861#pullrequestreview-365892433  

📁 include/boost/beast/core/impl/buffers_adaptor.hpp

```diff
 682 |+ -> value_type
 683 |+ {
 684 |+     return parent_->get_buffer(it_);
```

> Username: vinniefalco  
> Created_at: 2020-02-27 18:22:36 UTC  
> Updated_at: 2020-02-28 17:17:52 UTC  
> Url: https://github.com/boostorg/beast/pull/1861#discussion_r385288398  

There's no need for `get_buffer` since it is only called from this place, might as well just put the code here.

> Username: madmongo1  
> Created_at: 2020-02-27 22:38:04 UTC  
> Updated_at: 2020-02-28 17:17:52 UTC  
> Url: https://github.com/boostorg/beast/pull/1861#discussion_r385412424  

This boils down to our fundamentally different views on nested classes. I disagree, but will change it.


---

## Review 15 [Commented]

> Username: vinniefalco  
> Created_at: 2020-02-27 18:23:10 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/1861#pullrequestreview-365892844  

📁 include/boost/beast/core/impl/buffers_adaptor.hpp

```diff
 601 |+ 
 602 |+     if (std::next(it) == last_)
 603 |+         detail::truncate(result, last_size_);
```

> Username: vinniefalco  
> Created_at: 2020-02-27 18:23:10 UTC  
> Updated_at: 2020-02-28 17:17:52 UTC  
> Url: https://github.com/boostorg/beast/pull/1861#discussion_r385288758  

There's no need to have a separate `truncate` function, since it is only called from here. And even if it wasn't, the operation is so trivial that it is not deserving of being elevated to its own function.

> Username: madmongo1  
> Created_at: 2020-02-27 22:40:24 UTC  
> Updated_at: 2020-02-28 17:17:52 UTC  
> Url: https://github.com/boostorg/beast/pull/1861#discussion_r385413327  

strongly disagree.  
My reasoning is that one logical operation deserves its own function and name as this aids higher level reasoning about intent.  
Again, will change.


---

## Review 16 [Commented]

> Username: vinniefalco  
> Created_at: 2020-02-27 18:24:28 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/1861#pullrequestreview-365893749  

📁 include/boost/beast/core/impl/buffers_adaptor.hpp

```diff
  87 |+         , last_(other.last())
  88 |+         , first_offset_(other.first_offset())
  89 |+         , last_size_(other.last_size())
```

> Username: vinniefalco  
> Created_at: 2020-02-27 18:24:28 UTC  
> Updated_at: 2020-02-28 17:17:52 UTC  
> Url: https://github.com/boostorg/beast/pull/1861#discussion_r385289500  

These member functions `first()`, `last()`, `first_offset()`, and `last_size()` seem unnecessary, you could just access the data members. You will have to make the class template a friend first of course.


---

## Review 17 [Commented]

> Username: vinniefalco  
> Created_at: 2020-02-27 18:25:31 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/1861#pullrequestreview-365894449  

📁 include/boost/beast/core/impl/buffers_adaptor.hpp

```diff
 340 |-     }
 189 |+     buffers_adaptor<MutableBufferSequence>::
 190 |+     subrange<isMutable> const *parent_;
```

> Username: vinniefalco  
> Created_at: 2020-02-27 18:25:31 UTC  
> Updated_at: 2020-02-28 17:17:52 UTC  
> Url: https://github.com/boostorg/beast/pull/1861#discussion_r385290043  

continuation lines should be indented  
```  
    buffers_adaptor<MutableBufferSequence>::  
        subrange<isMutable> const *parent_;  
```  
I'm not a fan of "parent" but it isn't a showstopper. I would have just used `b_`.

> Username: madmongo1  
> Created_at: 2020-02-27 22:49:46 UTC  
> Updated_at: 2020-02-28 17:17:52 UTC  
> Url: https://github.com/boostorg/beast/pull/1861#discussion_r385416734  

I think `b_` is un-necessarily cryptic - I speak as someone who used to be quite happy being limted to 6 characters for label names in 6809 assembler. Even then we'd try to spll wt mnngfl nms.


---

## Review 18 [Commented]

> Username: vinniefalco  
> Created_at: 2020-02-27 18:26:15 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/1861#pullrequestreview-365894941  

📁 include/boost/beast/core/impl/buffers_adaptor.hpp

```diff
 488 |+     std::size_t pos,
 489 |+     std::size_t n)
 490 |+ -> void
```

> Username: vinniefalco  
> Created_at: 2020-02-27 18:26:15 UTC  
> Updated_at: 2020-02-28 17:17:52 UTC  
> Url: https://github.com/boostorg/beast/pull/1861#discussion_r385290442  

We should never see `-> void`


---

## Review 19 [Commented]

> Username: vinniefalco  
> Created_at: 2020-02-27 18:26:39 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/1861#pullrequestreview-365895221  

📁 include/boost/beast/core/impl/buffers_adaptor.hpp

```diff
 493 |+         last_ = first_;
 494 |+ 
 495 |+     // note: fallthrough
```

> Username: vinniefalco  
> Created_at: 2020-02-27 18:26:39 UTC  
> Updated_at: 2020-02-28 17:17:52 UTC  
> Url: https://github.com/boostorg/beast/pull/1861#discussion_r385290657  

cryptic


---

## Review 20 [Commented]

> Username: vinniefalco  
> Created_at: 2020-02-27 18:27:18 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/1861#pullrequestreview-365895691  

📁 include/boost/beast/core/impl/buffers_adaptor.hpp

```diff
 520 |+         {
 521 |+             first_offset_ = adjust;
 522 |+             pos = 0;
```

> Username: vinniefalco  
> Created_at: 2020-02-27 18:27:18 UTC  
> Updated_at: 2020-02-28 17:17:52 UTC  
> Url: https://github.com/boostorg/beast/pull/1861#discussion_r385290992  

you could `break` and skip the loop condition...

> Username: madmongo1  
> Created_at: 2020-02-27 23:01:55 UTC  
> Updated_at: 2020-02-28 17:17:52 UTC  
> Url: https://github.com/boostorg/beast/pull/1861#discussion_r385421053  

This would have no effect on the quality of gcc's code, and would for me make the loop more difficult to reason about as it's another exit point.  
  
https://godbolt.org/z/yNsgjF  
  
nevertheless will add


---

## Review 21 [Commented]

> Username: vinniefalco  
> Created_at: 2020-02-27 18:27:41 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/1861#pullrequestreview-365895956  

📁 include/boost/beast/core/impl/buffers_adaptor.hpp

```diff
 502 |+     }
 503 |+ 
 504 |+     auto last_element = [this](iter_type iter) {
```

> Username: vinniefalco  
> Created_at: 2020-02-27 18:27:41 UTC  
> Updated_at: 2020-02-28 17:17:52 UTC  
> Url: https://github.com/boostorg/beast/pull/1861#discussion_r385291189  

`auto is_last = ...`


---

## Review 22 [Commented]

> Username: vinniefalco  
> Created_at: 2020-02-27 18:28:25 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/1861#pullrequestreview-365896496  

📁 include/boost/beast/core/impl/buffers_adaptor.hpp

```diff
 572 |+ buffers_adaptor<MutableBufferSequence>::
 573 |+ subrange<isMutable>::
 574 |+ begin() const -> iterator
```

> Username: vinniefalco  
> Created_at: 2020-02-27 18:28:25 UTC  
> Updated_at: 2020-02-28 17:17:52 UTC  
> Url: https://github.com/boostorg/beast/pull/1861#discussion_r385291617  

I do like return types on their own line, whether auto or not  
```  
template<class MutableBufferSequence>  
template<bool isMutable>  
auto  
buffers_adaptor<MutableBufferSequence>::  
subrange<isMutable>::  
begin() const ->  
    iterator  
```


---

## Review 23 [Commented]

> Username: vinniefalco  
> Created_at: 2020-02-27 18:35:42 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/1861#pullrequestreview-365901377  

📁 test/beast/core/buffers_adaptor_subrange.cpp

```diff
  45 |+ namespace detail {
  46 |+ 
  47 |+ class buffers_adaptor_subrange_test
```

> Username: vinniefalco  
> Created_at: 2020-02-27 18:35:41 UTC  
> Updated_at: 2020-02-28 17:17:52 UTC  
> Url: https://github.com/boostorg/beast/pull/1861#discussion_r385295510  

This all needs to go in buffers_adaptor.cpp not this new weird file which should not exist


---

## Review 24 [Commented]

> Username: vinniefalco  
> Created_at: 2020-02-27 18:36:52 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/1861#pullrequestreview-365902148  

📁 test/beast/core/test_buffer.hpp

```diff
 360 | 
 361 | /** Test an instance of a dynamic buffer or mutable dynamic buffer.
```

> Username: vinniefalco  
> Created_at: 2020-02-27 18:36:52 UTC  
> Updated_at: 2020-02-28 17:17:52 UTC  
> Url: https://github.com/boostorg/beast/pull/1861#discussion_r385296134  

There should be no such thing as "mutable dynamic buffer". All of Beast's dynamic buffers should support both `const` and non-`const` `data()`. You might need a separate commit for that.

> Username: madmongo1  
> Created_at: 2020-02-27 23:20:54 UTC  
> Updated_at: 2020-02-28 17:17:52 UTC  
> Url: https://github.com/boostorg/beast/pull/1861#discussion_r385427382  

I don't understand what you mean.  
The code comment about was not written by me.

> Username: vinniefalco  
> Created_at: 2020-02-27 23:49:10 UTC  
> Updated_at: 2020-02-28 17:17:52 UTC  
> Url: https://github.com/boostorg/beast/pull/1861#discussion_r385436083  

Yep I know, lets discuss it


---

## Review 25 [Commented]

> Username: vinniefalco  
> Created_at: 2020-02-27 18:37:29 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/1861#pullrequestreview-365902580  

📁 test/beast/core/test_helpers.hpp

```diff
 208 |+ 
 209 |+ 
 210 |+ } // test_heplers
```

> Username: vinniefalco  
> Created_at: 2020-02-27 18:37:29 UTC  
> Updated_at: 2020-02-28 17:17:52 UTC  
> Url: https://github.com/boostorg/beast/pull/1861#discussion_r385296465  

No I've decided I don't want to start adding this to every test, we need to think of a better way.


---

## Review 26 [Commented]

> Username: vinniefalco  
> Created_at: 2020-02-27 23:58:50 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/1861#pullrequestreview-366082523  

📁 test/beast/core/buffers_adaptor.cpp

```diff
 151 |+             BEAST_EXPECTS(typeid(typename decltype(sub)::value_type) == typeid(value_type), func);
 152 |+ 
 153 |+             auto as_string = [](decltype(sub) const &sr) {
```

> Username: vinniefalco  
> Created_at: 2020-02-27 23:58:50 UTC  
> Updated_at: 2020-02-28 17:17:52 UTC  
> Url: https://github.com/boostorg/beast/pull/1861#discussion_r385439124  

What's wrong with calling `beast::buffers_to_string`? It is even more efficient than this function (since it reserves the space first)  
https://www.boost.org/doc/libs/develop/libs/beast/doc/html/beast/ref/boost__beast__buffers_to_string.html


---

## Review 27 [Commented]

> Username: vinniefalco  
> Created_at: 2020-02-27 23:59:42 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/1861#pullrequestreview-366082835  

📁 test/beast/core/buffers_adaptor.cpp

```diff
 160 |+                     auto f = reinterpret_cast<const char *>(buf.data());
 161 |+                     auto l = f + buf.size();
 162 |+                     result.insert(std::end(result), f, l);
```

> Username: vinniefalco  
> Created_at: 2020-02-27 23:59:41 UTC  
> Updated_at: 2020-02-28 17:17:52 UTC  
> Url: https://github.com/boostorg/beast/pull/1861#discussion_r385439382  

Why `std::end`? The type of `result` is known, this is not a generic context. Nothing is gained by calling `std::end` instead of just using `result.end()`. Or even calling `result.append(f, l)`. I would like to understand why you chose one over the other, lets discuss.

> Username: madmongo1  
> Created_at: 2020-02-28 09:16:32 UTC  
> Updated_at: 2020-02-28 17:17:52 UTC  
> Url: https://github.com/boostorg/beast/pull/1861#discussion_r385583971  

I use std::end by force of habit, since it works in all contexts so I don't have to waste time reasoning about the underlying container. It's a zero cost abstraction, and I prefer free functions over member functions.  
I could have used append, but it's specific to std::string and not generic across all containers, so to me it smells bad because it makes the code logic brittle (if I choose a different container type, I have to re-write logic, which makes no sense).


---
