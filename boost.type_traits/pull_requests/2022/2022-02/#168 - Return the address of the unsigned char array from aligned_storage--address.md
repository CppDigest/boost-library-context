# #168 Return the address of the unsigned char[] array from aligned_storage::address [Open]

> Username: pdimov  
> Created at: 2022-02-18 19:38:25 UTC  
> Updated at: 2025-04-21 12:27:15 UTC  
> Url: https://github.com/boostorg/type_traits/pull/168  

The standard says in https://eel.is/c++draft/basic.memobj#intro.object-3 that an array of `unsigned char` can provide storage for objects created in it with placement `new`. But it doesn't say anything about arrays of `char` or about object types such as `aligned_storage_imp`.  
  
To be on the right side of it, this changes `aligned_storage_imp` to contain an array of `unsigned char` instead of `char`, and to return its address from the `address` member function, instead of `this`.  
  
This is a relatively high risk change, because `aligned_storage` is used, at least, in the following libraries:  
```  
Inclusion report for <boost/aligned_storage.hpp> (in module type_traits):  
  
    from accumulators:  
        <boost/accumulators/framework/accumulators/droppable_accumulator.hpp>  
  
    from any:  
        <boost/any/basic_any.hpp>  
  
    from container:  
        <boost/container/devector.hpp>  
  
    from foreach:  
        <boost/foreach.hpp>  
  
    from lockfree:  
        <boost/lockfree/spsc_queue.hpp>  
  
    from log:  
        <libs/log/src/thread_id.cpp>  
  
    from serialization:  
        <boost/serialization/detail/stack_constructor.hpp>  
  
    from signals2:  
        <boost/signals2/detail/slot_call_iterator.hpp>  
  
    from spirit:  
        <boost/spirit/home/classic/core/non_terminal/impl/static.hpp>  
```  
```  
Inclusion report for <boost/type_traits/aligned_storage.hpp> (in module type_traits):  
  
    from asio:  
        <boost/asio/detail/type_traits.hpp>  
  
    from container:  
        <boost/container/node_handle.hpp>  
  
    from flyweight:  
        <boost/flyweight/detail/archive_constructed.hpp>  
        <boost/flyweight/key_value.hpp>  
  
    from geometry:  
        <boost/geometry/index/detail/serialization.hpp>  
        <boost/geometry/index/detail/varray.hpp>  
  
    from iostreams:  
        <boost/iostreams/detail/optional.hpp>  
  
    from log:  
        <boost/log/detail/threadsafe_queue.hpp>  
  
    from multi_index:  
        <boost/multi_index/detail/archive_constructed.hpp>  
        <boost/multi_index/detail/index_node_base.hpp>  
        <boost/multi_index/detail/node_handle.hpp>  
        <boost/multi_index/detail/scoped_bilock.hpp>  
        <boost/multi_index/detail/seq_index_ops.hpp>  
  
    from parameter:  
        <boost/parameter/aux_/maybe.hpp>  
  
    from pool:  
        <boost/pool/singleton_pool.hpp>  
  
    from python:  
        <boost/python/detail/referent_storage.hpp>  
  
    from serialization:  
        <boost/serialization/void_cast.hpp>  
  
    from signals2:  
        <boost/signals2/detail/auto_buffer.hpp>  
  
    from unordered:  
        <boost/unordered/detail/implementation.hpp>  
  
    from variant:  
        <boost/variant/variant.hpp>  
```  
  
I've verified that the following tests:  
```  
b2 -j32 libs/any/test libs/container/test libs/multi_index/test libs/pool/test libs/unordered/test libs/variant/test toolset=clang address-model=64 variant=debug,release  
```  
are not affected by the change, that is, no new failures are introduced by it.

---

## Comment 1

> Username: jzmaddock  
> Created_at: 2022-02-18 20:02:24 UTC  
> Url: https://github.com/boostorg/type_traits/pull/168#issuecomment-1045108734  

Thanks for taking this on: I've been lurking in that conversation, but certainly don't know enough to comment!

---

## Comment 2

> Username: pdimov  
> Created_at: 2022-02-18 20:09:09 UTC  
> Url: https://github.com/boostorg/type_traits/pull/168#issuecomment-1045117083  

Frankly, neither do I. The only one who I know to be qualified to comment on it is Richard Smith (@zygoloid).

---

## Comment 3

> Username: jzmaddock  
> Created_at: 2025-04-21 11:17:34 UTC  
> Url: https://github.com/boostorg/type_traits/pull/168#issuecomment-2818208167  

OMG, 3 years old, sorry!  Should this be merged?

---

## Comment 4

> Username: pdimov  
> Created_at: 2025-04-21 12:27:14 UTC  
> Url: https://github.com/boostorg/type_traits/pull/168#issuecomment-2818316028  

Probably. :-)

---
