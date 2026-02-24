# #1870 Dynamic buffer [Closed]

> Username: madmongo1  
> Created at: 2020-03-03 15:14:21 UTC  
> Updated at: 2020-03-17 15:12:17 UTC  
> Closed at: 2020-03-17 15:12:17 UTC  
> Url: https://github.com/boostorg/beast/pull/1870  

Adds dynamic_buffer() free function overloads and dynamic_buffer_v0_proxy template class.

---

## Review 1 [Commented]

> Username: vinniefalco  
> Created_at: 2020-03-03 15:52:41 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/1870#pullrequestreview-368097917  

📁 doc/qbk/quickref.xml

```diff
 133 |           <member><link linkend="beast.ref.boost__beast__buffers_prefix_view">buffers_prefix_view</link></member>
 134 |           <member><link linkend="beast.ref.boost__beast__buffers_suffix">buffers_suffix</link></member>
 135 |+           <member><link linkend="beast.ref.boost__beast__dynamic_buffer_v0_proxy">dynamic_buffer_v0_proxy</link></member>
```

> Username: vinniefalco  
> Created_at: 2020-03-03 15:52:40 UTC  
> Updated_at: 2020-03-04 15:05:59 UTC  
> Url: https://github.com/boostorg/beast/pull/1870#discussion_r387114329  

This shouldn't be public


---

## Review 2 [Commented]

> Username: vinniefalco  
> Created_at: 2020-03-03 15:55:27 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/1870#pullrequestreview-368100451  

📁 include/boost/beast/core/buffers_adaptor.hpp

```diff
 263 |+             auto prev = std::prev(i);
 264 |+             end_ = i;
 265 |+             return prev;
```

> Username: vinniefalco  
> Created_at: 2020-03-03 15:55:26 UTC  
> Updated_at: 2020-03-04 15:05:59 UTC  
> Url: https://github.com/boostorg/beast/pull/1870#discussion_r387116215  

This is confusing to me, why not  
```  
{  
  end_ = i;  
  return std::prev(i);  
}  
```  
?

> Username: madmongo1  
> Created_at: 2020-03-04 12:08:14 UTC  
> Updated_at: 2020-03-04 15:05:59 UTC  
> Url: https://github.com/boostorg/beast/pull/1870#discussion_r387624879  

good question. It probably evolved from something else.


---

## Review 3 [Commented]

> Username: vinniefalco  
> Created_at: 2020-03-03 15:58:16 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/1870#pullrequestreview-368103083  

📁 include/boost/beast/core/detail/dynamic_buffer_v0.hpp

```diff
  27 |+     class DynamicBuffer_v0,
  28 |+     class MutableBuffersType = typename DynamicBuffer_v0::mutable_buffers_type,
  29 |+     class ConstBuffersType = typename DynamicBuffer_v0::const_buffers_type>
```

> Username: vinniefalco  
> Created_at: 2020-03-03 15:58:16 UTC  
> Updated_at: 2020-03-04 15:05:59 UTC  
> Url: https://github.com/boostorg/beast/pull/1870#discussion_r387118346  

Why are `MutableBuffersType` and `ConstBuffersType` for?

> Username: madmongo1  
> Created_at: 2020-03-04 12:13:43 UTC  
> Updated_at: 2020-03-04 15:05:59 UTC  
> Url: https://github.com/boostorg/beast/pull/1870#discussion_r387627418  

I have binned this entire metafunction. It was for detecting and wrapping Asio DynamicBuffer_v1 types. I don't think we need to support them since they're deprecated in ASIO and never previously worked with Beast

> Username: madmongo1  
> Created_at: 2020-03-04 12:14:52 UTC  
> Updated_at: 2020-03-04 15:05:59 UTC  
> Url: https://github.com/boostorg/beast/pull/1870#discussion_r387627929  

will revisit if I'm wrong


---

## Review 4 [Commented]

> Username: vinniefalco  
> Created_at: 2020-03-03 15:59:23 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/1870#pullrequestreview-368104079  

📁 include/boost/beast/core/dynamic_buffer.hpp

```diff
  17 |+ #include <boost/asio/buffer.hpp>
  18 |+ #include <boost/assert.hpp>
  19 |+ #include <boost/type_traits/make_void.hpp>
```

> Username: vinniefalco  
> Created_at: 2020-03-03 15:59:23 UTC  
> Updated_at: 2020-03-04 15:05:59 UTC  
> Url: https://github.com/boostorg/beast/pull/1870#discussion_r387119134  

Isn't there a `make_void` in `<boost/beast/detail/config.hpp>`? I try not to include these little Boost headers, they will have to come out anyway when we do standalone beast.

> Username: madmongo1  
> Created_at: 2020-03-04 12:16:05 UTC  
> Updated_at: 2020-03-04 15:05:59 UTC  
> Url: https://github.com/boostorg/beast/pull/1870#discussion_r387628517  

found it. At the moment, we're importing the boost make_void in the beast type_traits header, but I guess this can change.


---

## Review 5 [Commented]

> Username: vinniefalco  
> Created_at: 2020-03-03 15:59:44 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/1870#pullrequestreview-368104415  

📁 include/boost/beast/core/dynamic_buffer.hpp

```diff
  34 |+  */
  35 |+ template<class DynamicBuffer_v0>
  36 |+ struct dynamic_buffer_v0_proxy
```

> Username: vinniefalco  
> Created_at: 2020-03-03 15:59:44 UTC  
> Updated_at: 2020-03-04 15:05:59 UTC  
> Url: https://github.com/boostorg/beast/pull/1870#discussion_r387119417  

This type should be private, e.g. `detail::dynamic_buffer_v0_proxy`


---

## Review 6 [Commented]

> Username: vinniefalco  
> Created_at: 2020-03-03 16:11:07 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/1870#pullrequestreview-368114459  

📁 include/boost/beast/core/flat_buffer.hpp

```diff
 531 |+ {
 532 |+ template<class Allocator>
 533 |+ struct is_dynamic_buffer_v0<basic_flat_buffer<Allocator>>
```

> Username: vinniefalco  
> Created_at: 2020-03-03 16:11:06 UTC  
> Updated_at: 2020-03-04 15:05:59 UTC  
> Url: https://github.com/boostorg/beast/pull/1870#discussion_r387127258  

This could go in `core/impl/flat_buffer.hpp` no?

> Username: madmongo1  
> Created_at: 2020-03-04 12:16:35 UTC  
> Updated_at: 2020-03-04 15:05:59 UTC  
> Url: https://github.com/boostorg/beast/pull/1870#discussion_r387628725  

or even detail

> Username: madmongo1  
> Created_at: 2020-03-04 12:17:46 UTC  
> Updated_at: 2020-03-04 15:05:59 UTC  
> Url: https://github.com/boostorg/beast/pull/1870#discussion_r387629273  

... on second thoughts, it seems pointless making 5 new files for this

> Username: vinniefalco  
> Created_at: 2020-03-04 12:25:26 UTC  
> Updated_at: 2020-03-04 15:05:59 UTC  
> Url: https://github.com/boostorg/beast/pull/1870#discussion_r387632837  

Yeah 5 new files is unnecessary, but core/impl/flat_buffer.hpp already exists why not put it there. Otherwise, Doxygen will see it, which doesn't help anyone.

> Username: madmongo1  
> Created_at: 2020-03-04 12:42:13 UTC  
> Updated_at: 2020-03-04 15:05:59 UTC  
> Url: https://github.com/boostorg/beast/pull/1870#discussion_r387640997  

I was not clear. I agree with your suggestion.


---

## Review 7 [Commented]

> Username: vinniefalco  
> Created_at: 2020-03-03 16:12:17 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/1870#pullrequestreview-368115527  

📁 include/boost/beast/core/impl/buffers_adaptor.hpp

```diff
 310 |         begin_, end_,
 311 |-         in_pos_, in_size_);
 311 |+         in_pos_, size());
```

> Username: vinniefalco  
> Created_at: 2020-03-03 16:12:17 UTC  
> Updated_at: 2020-03-04 15:05:59 UTC  
> Url: https://github.com/boostorg/beast/pull/1870#discussion_r387128109  

Was this a bug?

> Username: madmongo1  
> Created_at: 2020-03-04 13:33:34 UTC  
> Updated_at: 2020-03-04 15:05:59 UTC  
> Url: https://github.com/boostorg/beast/pull/1870#discussion_r387667976  

Might have been a rebasing artefact.


---

## Review 8 [Commented]

> Username: vinniefalco  
> Created_at: 2020-03-03 16:13:33 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/1870#pullrequestreview-368116763  

📁 include/boost/beast/core/impl/dynamic_buffer.hpp

```diff
  11 |+ #define BOOST_BEAST_CORE_IMPL_DYNAMIC_BUFFER_HPP
  12 |+ 
  13 |+ #include <boost/beast/core/dynamic_buffer.hpp>
```

> Username: vinniefalco  
> Created_at: 2020-03-03 16:13:32 UTC  
> Updated_at: 2020-03-04 15:05:59 UTC  
> Url: https://github.com/boostorg/beast/pull/1870#discussion_r387129081  

No need to include this, since this file cannot be included individually.


---

## Review 9 [Commented]

> Username: vinniefalco  
> Created_at: 2020-03-03 16:14:10 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/1870#pullrequestreview-368117345  

📁 include/boost/beast/core/impl/dynamic_buffer.hpp

```diff
  23 |+ : storage_(std::addressof(storage))
  24 |+ {
  25 |+     boost::ignore_unused(storage_->prepare(0));
```

> Username: vinniefalco  
> Created_at: 2020-03-03 16:14:10 UTC  
> Updated_at: 2020-03-04 15:05:59 UTC  
> Url: https://github.com/boostorg/beast/pull/1870#discussion_r387129535  

I am not a fan of this use of `ignore_unused`  to discard the return value.

> Username: vinniefalco  
> Created_at: 2020-03-03 17:09:21 UTC  
> Updated_at: 2020-03-04 15:05:59 UTC  
> Url: https://github.com/boostorg/beast/pull/1870#discussion_r387167115  

This should be rolled up into a member function `normalize()` (or `normalise()`)


---

## Review 10 [Commented]

> Username: vinniefalco  
> Created_at: 2020-03-03 16:14:39 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/1870#pullrequestreview-368117760  

📁 include/boost/beast/core/impl/dynamic_buffer.hpp

```diff
  92 |+ {
  93 |+     return detail::dynamic_buffer_v2_access::
  94 |+     data(static_cast<DynamicBuffer_v0 const&>(*storage_),
```

> Username: vinniefalco  
> Created_at: 2020-03-03 16:14:38 UTC  
> Updated_at: 2020-03-04 15:05:59 UTC  
> Url: https://github.com/boostorg/beast/pull/1870#discussion_r387129873  

continuations need an indent


---

## Review 11 [Commented]

> Username: vinniefalco  
> Created_at: 2020-03-03 16:15:19 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/1870#pullrequestreview-368118346  

📁 include/boost/beast/core/impl/dynamic_buffer.hpp

```diff
 112 |+ void
 113 |+ {
 114 |+     storage_->commit(net::buffer_size(storage_->prepare(n)));
```

> Username: vinniefalco  
> Created_at: 2020-03-03 16:15:18 UTC  
> Updated_at: 2020-03-04 15:05:59 UTC  
> Url: https://github.com/boostorg/beast/pull/1870#discussion_r387130367  

Beast provides `buffer_bytes` for this purpose, it is preferable to `net::buffer_size`


---

## Review 12 [Commented]

> Username: vinniefalco  
> Created_at: 2020-03-03 16:35:18 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/1870#pullrequestreview-368136661  

📁 .circleci/config.yml

```diff
  58 |               ./b2 -j 2 cxxstd=latest libs/beast/test
  59 |               ./b2 -j 2 cxxstd=latest libs/beast/example
  60 |+             no_output_timeout: 20m
```

> Username: vinniefalco  
> Created_at: 2020-03-03 16:35:17 UTC  
> Updated_at: 2020-03-04 15:05:59 UTC  
> Url: https://github.com/boostorg/beast/pull/1870#discussion_r387144997  

Do these changes cause the tests to produce no output for 20 minutes?

> Username: madmongo1  
> Created_at: 2020-03-04 13:43:26 UTC  
> Updated_at: 2020-03-04 15:05:59 UTC  
> Url: https://github.com/boostorg/beast/pull/1870#discussion_r387673440  

Difficult to say whether it was these changes or just variance at circleci (which I suspect). The default is 10 mins.


---

## Review 13 [Commented]

> Username: vinniefalco  
> Created_at: 2020-03-03 16:35:41 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/1870#pullrequestreview-368136984  

📁 include/boost/beast/core/buffers_adaptor.hpp

```diff
 259 |+             return;
 260 |+ 
 261 |+         auto erase_backwards = [this](iter_type i) -> iter_type
```

> Username: vinniefalco  
> Created_at: 2020-03-03 16:35:41 UTC  
> Updated_at: 2020-03-04 15:05:59 UTC  
> Url: https://github.com/boostorg/beast/pull/1870#discussion_r387145243  

Why the trailing return type?

> Username: madmongo1  
> Created_at: 2020-03-04 13:55:13 UTC  
> Updated_at: 2020-03-04 15:05:59 UTC  
> Url: https://github.com/boostorg/beast/pull/1870#discussion_r387680261  

lambdas can't have prefix return types.  
I take it you mean why a return type at all?   
The answer is that this lambda is designed to be conceptually compatible with std::erase, the return type was designed to hint at that. I can remove it.

> Username: vinniefalco  
> Created_at: 2020-03-04 13:56:13 UTC  
> Updated_at: 2020-03-04 15:05:59 UTC  
> Url: https://github.com/boostorg/beast/pull/1870#discussion_r387680881  

Does GitHub have a facepalm emoji


---

## Review 14 [Commented]

> Username: vinniefalco  
> Created_at: 2020-03-03 16:59:49 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/1870#pullrequestreview-368157187  

📁 include/boost/beast/core/buffers_adaptor.hpp

```diff
 321 |+                 {
 322 |+                     out_ = i;
 323 |+                     out_end_ = out_pos_ = in_pos_ + in_size_;
```

> Username: vinniefalco  
> Created_at: 2020-03-03 16:59:49 UTC  
> Updated_at: 2020-03-04 15:05:59 UTC  
> Url: https://github.com/boostorg/beast/pull/1870#discussion_r387161138  

not a fan of multiple assignments in one statement. Prefer:  
```  
out_pos_ = in_pos_ + in_size_;  
out_end_ = out_pos_;  
```


---

## Review 15 [Commented]

> Username: vinniefalco  
> Created_at: 2020-03-03 17:00:18 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/1870#pullrequestreview-368157549  

📁 include/boost/beast/core/buffers_adaptor.hpp

```diff
 346 | };
 347 | 
 348 |+ } // detail
```

> Username: vinniefalco  
> Created_at: 2020-03-03 17:00:17 UTC  
> Updated_at: 2020-03-04 15:05:59 UTC  
> Url: https://github.com/boostorg/beast/pull/1870#discussion_r387161410  

Yes good comment

> Username: madmongo1  
> Created_at: 2020-03-04 14:07:08 UTC  
> Updated_at: 2020-03-04 15:05:59 UTC  
> Url: https://github.com/boostorg/beast/pull/1870#discussion_r387687415  

it's easy to get lost in non-indented namespaces

> Username: vinniefalco  
> Created_at: 2020-03-04 14:08:15 UTC  
> Updated_at: 2020-03-04 15:05:59 UTC  
> Url: https://github.com/boostorg/beast/pull/1870#discussion_r387687981  

No it isn't


---

## Review 16 [Commented]

> Username: vinniefalco  
> Created_at: 2020-03-03 17:00:27 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/1870#pullrequestreview-368157686  

📁 include/boost/beast/core/buffers_adaptor.hpp

```diff
 339 |+ 
 340 |+ namespace detail
 341 |+ {
```

> Username: vinniefalco  
> Created_at: 2020-03-03 17:00:27 UTC  
> Updated_at: 2020-03-04 15:05:59 UTC  
> Url: https://github.com/boostorg/beast/pull/1870#discussion_r387161499  

Prefer it on one line  
```  
namespace detail {  
```


---

## Review 17 [Commented]

> Username: vinniefalco  
> Created_at: 2020-03-03 17:01:04 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/1870#pullrequestreview-368158168  

📁 include/boost/beast/core/buffers_adaptor.hpp

```diff
 266 |+         };
 267 |+ 
 268 |+         auto i = out_;
```

> Username: vinniefalco  
> Created_at: 2020-03-03 17:01:03 UTC  
> Updated_at: 2020-03-04 15:05:59 UTC  
> Url: https://github.com/boostorg/beast/pull/1870#discussion_r387161918  

I usually name a singular iterator `it`, and leave `i` for integers


---

## Review 18 [Commented]

> Username: vinniefalco  
> Created_at: 2020-03-03 17:02:36 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/1870#pullrequestreview-368159428  

📁 test/beast/core/test_buffer.hpp

```diff
 657 |+     auto engine = std::default_random_engine(sequence);
 658 |+     auto range = std::uniform_int_distribution<unsigned int>(0, 61);
 659 |+     std::generate_n(std::back_inserter(result),
```

> Username: vinniefalco  
> Created_at: 2020-03-03 17:02:35 UTC  
> Updated_at: 2020-03-04 15:05:59 UTC  
> Url: https://github.com/boostorg/beast/pull/1870#discussion_r387162940  

Why not just `std::iota`?

> Username: madmongo1  
> Created_at: 2020-03-04 14:37:15 UTC  
> Updated_at: 2020-03-04 15:05:59 UTC  
> Url: https://github.com/boostorg/beast/pull/1870#discussion_r387706685  

because I don't want increasing integers. I want a large shuffled deck of playing cards.


---

## Review 19 [Commented]

> Username: vinniefalco  
> Created_at: 2020-03-03 17:03:10 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/1870#pullrequestreview-368159848  

📁 test/beast/core/test_buffer.hpp

```diff
 666 |+                             return 'A' + char(code - 10);
 667 |+                         else
 668 |+                             return 'a' + char(code - 36);
```

> Username: vinniefalco  
> Created_at: 2020-03-03 17:03:10 UTC  
> Updated_at: 2020-03-04 15:05:59 UTC  
> Url: https://github.com/boostorg/beast/pull/1870#discussion_r387163252  

This is overkill.. adding code to ensure that all the output values are alphanumeric is way too try-hard!!!

> Username: madmongo1  
> Created_at: 2020-03-04 14:17:39 UTC  
> Updated_at: 2020-03-04 15:05:59 UTC  
> Url: https://github.com/boostorg/beast/pull/1870#discussion_r387693625  

if they're not, they are difficult to compare visually.


---

## Review 20 [Commented]

> Username: vinniefalco  
> Created_at: 2020-03-03 17:04:10 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/1870#pullrequestreview-368160668  

📁 test/beast/core/test_buffer.hpp

```diff
 599 | }
 600 | 
 601 |+ namespace subtests
```

> Username: vinniefalco  
> Created_at: 2020-03-03 17:04:10 UTC  
> Updated_at: 2020-03-04 15:05:59 UTC  
> Url: https://github.com/boostorg/beast/pull/1870#discussion_r387163912  

why the namespace? Too many new names!!


---

## Review 21 [Commented]

> Username: vinniefalco  
> Created_at: 2020-03-03 17:04:58 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/1870#pullrequestreview-368161288  

📁 test/beast/core/test_buffer.hpp

```diff
 792 |+     auto source = std::string(size, 'x');
 793 |+     char x = 'A';
 794 |+     std::generate_n(source.begin(), size, [&x]() { return x++; });
```

> Username: vinniefalco  
> Created_at: 2020-03-03 17:04:58 UTC  
> Updated_at: 2020-03-04 15:05:59 UTC  
> Url: https://github.com/boostorg/beast/pull/1870#discussion_r387164387  

What's wrong with `std::iota`?

> Username: madmongo1  
> Created_at: 2020-03-04 14:32:36 UTC  
> Updated_at: 2020-03-04 15:05:59 UTC  
> Url: https://github.com/boostorg/beast/pull/1870#discussion_r387703592  

I want a sequence of random alphanumerics so I can easily compare them visually and see where the buffer arithmetic messed up.

> Username: vinniefalco  
> Created_at: 2020-03-04 15:43:38 UTC  
> Updated_at: 2020-03-04 15:44:04 UTC  
> Url: https://github.com/boostorg/beast/pull/1870#discussion_r387752279  

Less try-hard please


---

## Review 22 [Commented]

> Username: vinniefalco  
> Created_at: 2020-03-03 17:05:30 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/1870#pullrequestreview-368161669  

📁 test/beast/core/test_buffer.hpp

```diff
 786 |+ }
 787 |+ 
 788 |+ template<class DynBufferV0Generator>
```

> Username: vinniefalco  
> Created_at: 2020-03-03 17:05:29 UTC  
> Updated_at: 2020-03-04 15:05:59 UTC  
> Url: https://github.com/boostorg/beast/pull/1870#discussion_r387164687  

Too many new names! "Dyn" now? What's wrong with `template< class G >`? Too many long names are being added, for types that have no meaningful consequence for readers other than that they are just _there_. No one knows what a `DynBufferV0Generator` is but the choice of name elevates its importance needlessly.

> Username: madmongo1  
> Created_at: 2020-03-04 14:33:19 UTC  
> Updated_at: 2020-03-04 15:05:59 UTC  
> Url: https://github.com/boostorg/beast/pull/1870#discussion_r387704069  

I prefer descriptive names. One day this will be a concept. But sure, not problem to change it.


---

## Comment 23

> Username: codecov[bot]  
> Created_at: 2020-03-03 19:03:59 UTC  
> Updated_at: 2020-03-04 21:41:16 UTC  
> Url: https://github.com/boostorg/beast/pull/1870#issuecomment-594114456  

# [Codecov](https://codecov.io/gh/boostorg/beast/pull/1870?src=pr&el=h1) Report  
> Merging [#1870](https://codecov.io/gh/boostorg/beast/pull/1870?src=pr&el=desc) into [develop](https://codecov.io/gh/boostorg/beast/commit/c8a726f962b2fbf77d00b273b3c6fb0dd975a6b5?src=pr&el=desc) will **decrease** coverage by `0.39%`.  
> The diff coverage is `69.76%`.  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/beast/pull/1870/graphs/tree.svg?width=650&token=Gq6MFA9JRF&height=150&src=pr)](https://codecov.io/gh/boostorg/beast/pull/1870?src=pr&el=tree)  
  
```diff  
@@            Coverage Diff             @@  
##           develop    #1870     +/-   ##  
==========================================  
- Coverage    95.09%   94.69%   -0.4%       
==========================================  
  Files          156      159      +3       
  Lines        12011    12221    +210       
==========================================  
+ Hits         11422    11573    +151       
- Misses         589      648     +59  
```  
  
  
| [Impacted Files](https://codecov.io/gh/boostorg/beast/pull/1870?src=pr&el=tree) | Coverage Δ | |  
|---|---|---|  
| [include/boost/beast/core/impl/multi\_buffer.hpp](https://codecov.io/gh/boostorg/beast/pull/1870/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC9jb3JlL2ltcGwvbXVsdGlfYnVmZmVyLmhwcA==) | `98.96% <ø> (+0.82%)` | :arrow_up: |  
| [include/boost/beast/core/flat\_static\_buffer.hpp](https://codecov.io/gh/boostorg/beast/pull/1870/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC9jb3JlL2ZsYXRfc3RhdGljX2J1ZmZlci5ocHA=) | `100% <ø> (ø)` | :arrow_up: |  
| [include/boost/beast/core/buffers\_adaptor.hpp](https://codecov.io/gh/boostorg/beast/pull/1870/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC9jb3JlL2J1ZmZlcnNfYWRhcHRvci5ocHA=) | `100% <ø> (ø)` | :arrow_up: |  
| [include/boost/beast/core/flat\_buffer.hpp](https://codecov.io/gh/boostorg/beast/pull/1870/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC9jb3JlL2ZsYXRfYnVmZmVyLmhwcA==) | `100% <ø> (ø)` | :arrow_up: |  
| [include/boost/beast/core/impl/static\_buffer.hpp](https://codecov.io/gh/boostorg/beast/pull/1870/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC9jb3JlL2ltcGwvc3RhdGljX2J1ZmZlci5ocHA=) | `100% <ø> (ø)` | :arrow_up: |  
| [...clude/boost/beast/core/impl/flat\_static\_buffer.hpp](https://codecov.io/gh/boostorg/beast/pull/1870/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC9jb3JlL2ltcGwvZmxhdF9zdGF0aWNfYnVmZmVyLmhwcA==) | `100% <ø> (ø)` | :arrow_up: |  
| [include/boost/beast/core/static\_buffer.hpp](https://codecov.io/gh/boostorg/beast/pull/1870/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC9jb3JlL3N0YXRpY19idWZmZXIuaHBw) | `100% <ø> (ø)` | :arrow_up: |  
| [...boost/beast/core/detail/impl/dynamic\_buffer\_v0.hpp](https://codecov.io/gh/boostorg/beast/pull/1870/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC9jb3JlL2RldGFpbC9pbXBsL2R5bmFtaWNfYnVmZmVyX3YwLmhwcA==) | `100% <100%> (ø)` | |  
| [...lude/boost/beast/core/detail/dynamic\_buffer\_v0.hpp](https://codecov.io/gh/boostorg/beast/pull/1870/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC9jb3JlL2RldGFpbC9keW5hbWljX2J1ZmZlcl92MC5ocHA=) | `100% <100%> (ø)` | |  
| [include/boost/beast/core/impl/dynamic\_buffer.hpp](https://codecov.io/gh/boostorg/beast/pull/1870/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC9jb3JlL2ltcGwvZHluYW1pY19idWZmZXIuaHBw) | `100% <100%> (ø)` | |  
| ... and [14 more](https://codecov.io/gh/boostorg/beast/pull/1870/diff?src=pr&el=tree-more) | |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/beast/pull/1870?src=pr&el=continue).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/beast/pull/1870?src=pr&el=footer). Last update [c8a726f...6ce6099](https://codecov.io/gh/boostorg/beast/pull/1870?src=pr&el=lastupdated). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments).

---

## Review 24 [Commented]

> Username: vinniefalco  
> Created_at: 2020-03-04 15:44:04 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/1870#pullrequestreview-368851622  

📁 include/boost/beast/core/detail/dynamic_buffer_v0.hpp

```diff
  79 |+     @see flat_static_buffer
  80 |+     @see multi_buffer
  81 |+     @see static_buffer
```

> Username: vinniefalco  
> Created_at: 2020-03-04 15:08:46 UTC  
> Updated_at: 2020-03-04 15:44:04 UTC  
> Url: https://github.com/boostorg/beast/pull/1870#discussion_r387727416  

```  
    @see buffers_adaptor, flat_buffer, flat_static_buffer, multi_buffer, static_buffer  
```


---

## Review 25 [Commented]

> Username: vinniefalco  
> Created_at: 2020-03-04 15:48:17 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/1870#pullrequestreview-368893727  

📁 include/boost/beast/core/detail/buffer.hpp

```diff
  85 |+     n = (std::min)(n, target.size());
  86 |+     target = net::mutable_buffer(target.data(), n);
  87 |+     return target;
```

> Username: vinniefalco  
> Created_at: 2020-03-04 15:48:16 UTC  
> Updated_at: 2020-03-04 15:48:17 UTC  
> Url: https://github.com/boostorg/beast/pull/1870#discussion_r387755502  

This whole function could be reduced to  
```  
return net::mutable_buffer( target.data(), (std::min)(n, target.size() );  
```  
At which point why do we need a whole separate function?


---
