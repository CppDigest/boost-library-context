# #116 Add to_chars for writing uuid strings without allocating [Merged]

> Username: gummif  
> Created at: 2020-08-13 21:56:38 UTC  
> Updated at: 2021-06-15 16:47:36 UTC  
> Merged at: 2021-06-15 16:47:36 UTC  
> Closed at: 2021-06-15 16:47:36 UTC  
> Url: https://github.com/boostorg/uuid/pull/116  

This is in particular useful for when allocating a std::string is too expensive.

---

## Review 1 [Changes requested]

> Username: Lastique  
> Created_at: 2020-08-13 22:10:57 UTC  
> State: CHANGES_REQUESTED  
> Url: https://github.com/boostorg/uuid/pull/116#pullrequestreview-467161527  

📁 include/boost/uuid/uuid_io.hpp

```diff
 172 |+     std::string result;
 173 |+     result.reserve(36);
 174 |+     to_chars(u, std::back_inserter(result), result.capacity());
```

> Username: Lastique  
> Created_at: 2020-08-13 22:03:18 UTC  
> Updated_at: 2020-08-14 18:26:53 UTC  
> Url: https://github.com/boostorg/uuid/pull/116#discussion_r470274124  

This should probably use `resize` and pass a pointer to the string `data()`.

> Username: gummif  
> Created_at: 2020-08-14 18:24:28 UTC  
> Updated_at: 2020-08-14 18:26:53 UTC  
> Url: https://github.com/boostorg/uuid/pull/116#discussion_r470789084  

Will do.

---

📁 include/boost/uuid/uuid_io.hpp

```diff
 149 |- inline std::string to_string(uuid const& u)
 149 |+ template<class OutputIterator>
 150 |+ bool to_chars(uuid const& u, OutputIterator out, size_t buffer_size)
```

> Username: Lastique  
> Created_at: 2020-08-13 22:09:38 UTC  
> Updated_at: 2020-08-14 18:26:53 UTC  
> Url: https://github.com/boostorg/uuid/pull/116#discussion_r470276470  

`std::size_t`.  
  
And it's not quite conventional to pass a size along with an output iterator. Doubly so since you are not outputting anything if `buffer_size < 36`.  
  
I think this method should follow the convention of working with iterators, where you pass only the output iterator, which ensures there is enough storage. Another version specifically designed for raw buffers can be provided. That version would accept a raw pointer and a size of the buffer.

> Username: gummif  
> Created_at: 2020-08-14 18:24:10 UTC  
> Updated_at: 2020-08-14 18:26:53 UTC  
> Url: https://github.com/boostorg/uuid/pull/116#discussion_r470788835  

Yes, it's a bit unconventional. Another overload is a good idea.


---

## Comment 2

> Username: gummif  
> Created_at: 2020-08-21 19:19:13 UTC  
> Url: https://github.com/boostorg/uuid/pull/116#issuecomment-678450862  

The PR is ready for another review round.

---

## Comment 3

> Username: Lastique  
> Created_at: 2020-08-22 11:40:00 UTC  
> Url: https://github.com/boostorg/uuid/pull/116#issuecomment-678630128  

Looks ok to me.

---

## Review 4 [Commented]

> Username: jeking3  
> Created_at: 2020-09-30 17:03:17 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/uuid/pull/116#pullrequestreview-499638867  

One comment about use of return value.

📁 include/boost/uuid/uuid_io.hpp

```diff
 165 |+ }
 166 |+ 
 167 |+ inline bool to_chars(uuid const& u, char* first, char* last)
```

> Username: jeking3  
> Created_at: 2020-09-30 17:03:04 UTC  
> Updated_at: 2020-09-30 17:03:17 UTC  
> Url: https://github.com/boostorg/uuid/pull/116#discussion_r497666059  

I'm not a huge fan of return value checking for success... should this perhaps throw something instead like a std::length_error should the buffer size be insufficient?

> Username: Lastique  
> Created_at: 2020-09-30 19:50:24 UTC  
> Url: https://github.com/boostorg/uuid/pull/116#discussion_r497760986  

I think a return value is fine for a low level tool like this.

> Username: gummif  
> Created_at: 2020-10-01 10:32:48 UTC  
> Url: https://github.com/boostorg/uuid/pull/116#discussion_r498144674  

This was intended to be similar to the API of https://en.cppreference.com/w/cpp/utility/to_chars (non-throwing and non-allocating) but I am open to changing it.


---

## Comment 5

> Username: jeking3  
> Created_at: 2021-06-15 16:47:28 UTC  
> Url: https://github.com/boostorg/uuid/pull/116#issuecomment-861661755  

Hey, sorry for the long delay, I've been off in startup-land.

---
