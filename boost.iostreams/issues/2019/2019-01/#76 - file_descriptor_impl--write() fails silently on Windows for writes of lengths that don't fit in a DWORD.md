# #76 - file_descriptor_impl::write() fails silently on Windows for writes of lengths that don't fit in a DWORD [Open]

> Username: lvoege  
> Created at: 2019-01-13 19:01:44 UTC  
> Updated at: 2019-02-19 20:01:42 UTC  
> Url: https://github.com/boostorg/iostreams/issues/76  

https://github.com/boostorg/iostreams/blob/29a831798ec6337848d1f2e8595b59e5503e4f21/src/file_descriptor.cpp#L301  
  
that static cast overflows when n doesn't fit in a DWORD, but then the un-overflowed value is returned. pass it 5GB to write and it'll write 1GB and cheerfully tell the caller it wrote the whole 5GB.

---

## Comment 1

> Username: mclow  
> Created at: 2019-01-13 19:34:40 UTC  
> Url: https://github.com/boostorg/iostreams/issues/76#issuecomment-453858588  

If you write a test for it, I'll fix it. ;-)  
`throw_system_failure("failed writing");`

---

## Comment 2

> Username: jeking3  
> Created at: 2019-02-19 20:01:41 UTC  
> Url: https://github.com/boostorg/iostreams/issues/76#issuecomment-465287934  

Well, he wrote the test... :)
