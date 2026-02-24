# #272 - boost deserialization access violation in boost_serialization-vc142-mt-gd-x64-1_80.dll [Closed]

> Username: danyhm  
> Created at: 2022-11-15 18:43:04 UTC  
> Updated at: 2023-08-24 18:54:29 UTC  
> Closed at: 2023-08-24 18:54:29 UTC  
> Url: https://github.com/boostorg/serialization/issues/272  

Hello,  
I'm serializing an object on a x86 app and deserializing it in another x64 app both in windows.  
I'm using VS2019 16.11.21 and using boost v1.80 with vcpkg. If i use the default settings of vcpkg to use DLL the following exception is thrown while deserializing the object.  
  
`Exception thrown at 0x00007FFD2A9590D8 (boost_serialization-vc142-mt-gd-x64-1_80.dll) in hvppctrl.exe: 0xC0000005: Access violation executing location 0x00007FFD2A9590D8.` in the file `xlocnum` on line 300  
  
however if i use static libraries with vcpkg the deserialization is done correctly with no errors.

---

## Comment 1

> Username: robertramey  
> Created at: 2023-08-24 18:54:29 UTC  
> Url: https://github.com/boostorg/serialization/issues/272#issuecomment-1692244057  

If you can make a small test, I'll look at it.
