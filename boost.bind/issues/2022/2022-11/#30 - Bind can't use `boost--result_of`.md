# #30 - Bind can't use `boost::result_of` [Open]

> Username: denzor200  
> Created at: 2022-11-07 12:28:58 UTC  
> Updated at: 2022-11-09 22:43:06 UTC  
> Url: https://github.com/boostorg/bind/issues/30  

I discovered that `boost::bind` strongly requires to have `result_type` meta-field from the type of the passed functional object. I was surprised, because why not to use `boost::result_of` when possible?  
Can we fix it, even it will be a breaking change?  
  
The shortest-reproducible example here:  
https://godbolt.org/z/5fhb1hdYa

---

## Comment 1

> Username: pdimov  
> Created at: 2022-11-07 12:48:26 UTC  
> Url: https://github.com/boostorg/bind/issues/30#issuecomment-1305564274  

`boost::bind` predates `result_of` and was never updated to support it. As `result_of` is obsolete nowadays, if we're going to change `boost::bind`, we should make it use `decltype` directly.  
  
That's not an entirely trivial change though, because currently objects returned by `boost::bind` have a fixed return type, exposed via `::result_type`.

---

## Comment 2

> Username: denzor200  
> Created at: 2022-11-08 07:09:21 UTC  
> Url: https://github.com/boostorg/bind/issues/30#issuecomment-1306730837  

> we should make it use `decltype` directly.  
  
You suggest to raise up minimal standard version to C++11 here? If so, what will be a reason to use `boost::bind` instead of `std::bind`? I thought this library is a C++03 analogue of C++11's `std::bind`

---

## Comment 3

> Username: denzor200  
> Created at: 2022-11-08 07:09:28 UTC  
> Url: https://github.com/boostorg/bind/issues/30#issuecomment-1306730970  

Or you just want to say that `boost::bind` is obsolete and there is no sense to resolve this issue?

---

## Comment 4

> Username: pdimov  
> Created at: 2022-11-09 22:43:06 UTC  
> Url: https://github.com/boostorg/bind/issues/30#issuecomment-1309486272  

> You suggest to raise up minimal standard version to C++11 here?  
  
On this specific instance I was thinking of using `decltype` when available, and retaining the current behavior when not.  
  
> If so, what will be a reason to use boost::bind instead of std::bind? I thought this library is a C++03 analogue of C++11's std::bind  
  
`boost::bind` actually predates `std::bind` and is the source of the `std::bind` proposal. Much code still uses it so it would still make sense to keep it working even if we drop C++03, and it has some features that `std::bind` lacks, such as operators and some other subtle differences.  
  
> Or you just want to say that boost::bind is obsolete and there is no sense to resolve this issue?  
  
`boost::bind` by itself may not be entirely obsolete, but I wouldn't invest much in new development targeting C++03.
