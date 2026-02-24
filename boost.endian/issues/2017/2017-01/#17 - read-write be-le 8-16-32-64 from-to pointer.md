# #17 - read/write be/le 8/16/32/64 from/to pointer [Closed]

> Username: OlafvdSpek  
> Created at: 2017-01-11 16:07:15 UTC  
> Updated at: 2020-05-03 14:17:43 UTC  
> Closed at: 2020-05-03 14:17:43 UTC  
> Url: https://github.com/boostorg/endian/issues/17  

One often needs to read or write values from/to a buffer while having a pointer. Could you support this?  
  
I guess one could use `*reinterpret_cast<big_int64_buf_t&>(*p)` but I think functions would be more convenient.  
The functions are probably implemented already but aren't exposed.

---

## Comment 1

> Username: feverzsj  
> Created at: 2017-04-29 15:54:39 UTC  
> Url: https://github.com/boostorg/endian/issues/17#issuecomment-298177161  

yes, there are:  
detail::store_little_endian / load_little_endian  
detail::store_big_endian / load_big_endian

---

## Comment 2

> Username: pdimov  
> Created at: 2019-04-28 15:19:04 UTC  
> Url: https://github.com/boostorg/endian/issues/17#issuecomment-487388664  

The current develop has `endian_load` and `endian_store`, I haven't yet documented them though.

---

## Comment 3

> Username: pdimov  
> Created at: 2019-04-30 16:07:10 UTC  
> Url: https://github.com/boostorg/endian/issues/17#issuecomment-488013534  

Now documented, https://www.boost.org/doc/libs/develop/libs/endian/doc/html/endian.html#conversion_reference

---

## Comment 4

> Username: OlafvdSpek  
> Created at: 2019-04-30 16:30:14 UTC  
> Url: https://github.com/boostorg/endian/issues/17#issuecomment-488021816  

> 3 or 4 when sizeof(T) is 4  
  
Why can't it be 1 or 2 when sizeof(T) is 4?

---

## Comment 5

> Username: pdimov  
> Created at: 2019-04-30 16:41:00 UTC  
> Url: https://github.com/boostorg/endian/issues/17#issuecomment-488025540  

Do you have a need for that? The present "client" of these functions is `endian_buffer` which also disallows `endian_buffer<order::little, uint32_t, 16>` for instance because you can just use `uint16_t` as the value type for the 16 bit buffer.

---

## Comment 6

> Username: OlafvdSpek  
> Created at: 2019-04-30 16:52:23 UTC  
> Updated at: 2019-04-30 16:56:31 UTC  
> Url: https://github.com/boostorg/endian/issues/17#issuecomment-488029420  

> Do you have a need for that?  
  
Maybe. An int could be used internally while a 16-bit value is used externally.  
  
```  
boost::endian::endian_store<int, 2, boost::endian::big>(p, 1 + 2);  
```  
  
OK, I guess I could use int16_t here, I was thinking T was deduced automatically, which might've been more convenient.  
The current syntax is verbose, something like `boost::endian::store_be16(p, 1 + 2)` would be simpler.

---

## Comment 7

> Username: pdimov  
> Created at: 2019-04-30 23:26:27 UTC  
> Url: https://github.com/boostorg/endian/issues/17#issuecomment-488150705  

OK, I relaxed the requirements on `N`.

---

## Comment 8

> Username: OlafvdSpek  
> Created at: 2019-05-01 12:01:43 UTC  
> Url: https://github.com/boostorg/endian/issues/17#issuecomment-488266040  

Thanks. Any chance for the convenience functions with shorter syntax?

---

## Comment 9

> Username: pdimov  
> Created at: 2019-10-12 23:44:37 UTC  
> Url: https://github.com/boostorg/endian/issues/17#issuecomment-541370133  

Added convenience load/store functions to develop: https://www.boost.org/doc/libs/develop/libs/endian/doc/html/endian.html#conversion_reference

---

## Comment 10

> Username: OlafvdSpek  
> Created at: 2019-10-13 09:39:36 UTC  
> Url: https://github.com/boostorg/endian/issues/17#issuecomment-541403079  

Thanks. Would native (but unaligned) endian functions make sense for completeness?

---

## Comment 11

> Username: pdimov  
> Created at: 2019-10-15 11:57:55 UTC  
> Url: https://github.com/boostorg/endian/issues/17#issuecomment-542176107  

I'd rather not add these without at least some indication that they will be of use. That's 28 more functions after all.
