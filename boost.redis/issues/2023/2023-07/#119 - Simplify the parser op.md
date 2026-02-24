# #119 - Simplify the parser op [Closed]

> Username: mzimbres  
> Created at: 2023-07-02 09:15:48 UTC  
> Updated at: 2023-07-30 09:44:59 UTC  
> Closed at: 2023-07-30 09:44:59 UTC  
> Url: https://github.com/boostorg/redis/issues/119  

The [parser op](https://github.com/boostorg/redis/blob/46525371b9247a1ec208a6157880ec77f5ddadc4/include/boost/redis/detail/read_ops.hpp#L26) which currently consists of calls to `async_read_until` and `async_read`, look more complex than it should. This makes it non trivial to decouple the parser from IO operations, which would be useful for testing.  
  
Moreover, I think that although `async_read_until` has served us well so far, it doesn't look like the right fit for RESP3 protocol since it results in too many rescheduling and rotations of the underlying buffer, for more background see https://lists.boost.org/Archives/boost/2022/04/252726.php.  
  
If the parser is refactored to work only with calls to `async_read_some`, all those problems will go away, its incremental character will become evident and using it without doing IO will be easier, this is going to simplify some tests.
