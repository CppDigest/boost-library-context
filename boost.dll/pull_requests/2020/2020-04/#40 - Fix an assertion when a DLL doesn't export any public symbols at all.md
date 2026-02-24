# #40 Fix an assertion when a DLL doesn't export any public symbols at all [Merged]

> Username: Naios  
> Created at: 2020-04-20 02:18:08 UTC  
> Updated at: 2020-05-05 18:11:40 UTC  
> Merged at: 2020-05-05 18:09:48 UTC  
> Closed at: 2020-05-05 18:09:48 UTC  
> Url: https://github.com/boostorg/dll/pull/40  

Can be reproduced with any shared library on Windows that was built from an empty source file,  
for example with the following `.dll`: [example.dll.zip](https://github.com/boostorg/dll/files/4500814/example.dll.zip).  
  
`h.OptionalHeader.DataDirectory[IMAGE_DIRECTORY_ENTRY_EXPORT_].VirtualAddress` evaluates to 0 in such cases and thus `real_offset` becomes 0 which triggers the assertion below.  
  
https://github.com/boostorg/dll/blob/ab379539806956b4113050ebb978615a4f6a09f0/include/boost/dll/detail/pe_info.hpp#L217

---

## Comment 1

> Username: apolukhin  
> Created_at: 2020-05-05 18:11:40 UTC  
> Url: https://github.com/boostorg/dll/pull/40#issuecomment-624219936  

Many thanks for the fix! Added some tests in 4740f758dbd79b6f4278b0fef300139b94162b6f

---
