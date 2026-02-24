# #75 - Inconsistent documentation on return value of write() for SeekableDevice [Open]

> Username: chenxiaolong  
> Created at: 2018-12-04 03:43:25 UTC  
> Updated at: 2018-12-04 11:23:51 UTC  
> Url: https://github.com/boostorg/iostreams/issues/75  

In the `SeekableDevice` documentation, the description of `write()` says:  
  
https://github.com/boostorg/iostreams/blob/29a831798ec6337848d1f2e8595b59e5503e4f21/doc/concepts/seekable_device.html#L58-L60  
  
but the function signature says the return value is `void`:  
  
https://github.com/boostorg/iostreams/blob/29a831798ec6337848d1f2e8595b59e5503e4f21/doc/concepts/seekable_device.html#L56  
  
Should that say `std::streamsize` instead?

---

## Comment 1

> Username: jeking3  
> Created at: 2018-12-04 11:23:12 UTC  
> Url: https://github.com/boostorg/iostreams/issues/75#issuecomment-444066647  

Yes, it looks like the documentation is incorrect here.
