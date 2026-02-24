# #35 - floating point support [Closed]

> Username: huangqinjin  
> Created at: 2019-09-14 18:25:16 UTC  
> Updated at: 2019-10-15 13:42:55 UTC  
> Closed at: 2019-10-15 13:42:55 UTC  
> Url: https://github.com/boostorg/endian/issues/35  

[1.71.0 revision history](https://www.boost.org/doc/libs/1_71_0/libs/endian/doc/html/endian.html#overview_changes_in_1_71_0) states that support for float and double is added. But why no `{little,big,native}_float32_t` and `{little,big,native}_float64_t` typedefs provided?

---

## Comment 1

> Username: pdimov  
> Created at: 2019-10-15 11:55:56 UTC  
> Url: https://github.com/boostorg/endian/issues/35#issuecomment-542175396  

I've added these typedefs to the develop branch and they will be in the next release.
