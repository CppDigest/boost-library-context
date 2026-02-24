# #18 - Clarify constraints on type T of endian_buffer when align::yes [Closed]

> Username: viboes  
> Created at: 2017-01-29 09:25:43 UTC  
> Updated at: 2019-04-29 16:33:47 UTC  
> Closed at: 2019-04-29 16:33:47 UTC  
> Url: https://github.com/boostorg/endian/issues/18  

It is not clear from the documentation of endian_buffer that the user needs to overload endian_reverse for UDT.  
  
http://melpon.org/wandbox/permlink/iPwgdVBV91wsRxeC

---

## Comment 1

> Username: pdimov  
> Created at: 2019-04-29 16:33:47 UTC  
> Url: https://github.com/boostorg/endian/issues/18#issuecomment-487650647  

The constraints on `T` in the aligned case are now  
  
> `T` must be a trivially copyable type (such as `float`) that is assumed to have the same endianness as `uintNbits_t`.
