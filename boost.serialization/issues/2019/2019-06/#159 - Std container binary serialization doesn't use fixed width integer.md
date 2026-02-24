# #159 - Std container binary serialization doesn't use fixed width integer [Closed]

> Username: SharpSnake  
> Created at: 2019-06-01 03:16:05 UTC  
> Updated at: 2019-09-20 19:20:19 UTC  
> Closed at: 2019-09-20 19:20:19 UTC  
> Url: https://github.com/boostorg/serialization/issues/159  

For example, i serialize a std::string( std::vector and other containers have the same problem ) on x64 environment, and deserialize it on x86 environment always get a error result or exception.  
Both use binary_i/oarchive.  
  
This is caused by "size_t", which is the type of the container size.  
Why not use a fixed width integer, like std::uint32_t?

---

## Comment 1

> Username: SharpSnake  
> Created at: 2019-06-01 03:25:09 UTC  
> Url: https://github.com/boostorg/serialization/issues/159#issuecomment-497908116  

And is there a simple way to solve the problem? Thanks.  
 @robertramey

---

## Comment 2

> Username: robertramey  
> Created at: 2019-06-01 04:36:21 UTC  
> Url: https://github.com/boostorg/serialization/issues/159#issuecomment-497911876  

binary archives are not guaranteed to be portable.  That is the can only be guaranteed to be saved/loaded to the same machine architecture.  For portable storage, use a different archive - example text archive.

---

## Comment 3

> Username: SharpSnake  
> Created at: 2019-06-01 04:58:40 UTC  
> Url: https://github.com/boostorg/serialization/issues/159#issuecomment-497912973  

Text and xml archive sure are options. But i prefer binary archives, it's smaller and faster for network transmission, for example.  
Really looking forward to have some improvements for portable binary storage int the future version. Maybe we can custom config to use std::uint32_t or std::uint64_t on both sides of save/load.

---

## Comment 4

> Username: robertramey  
> Created at: 2019-06-01 16:26:08 UTC  
> Url: https://github.com/boostorg/serialization/issues/159#issuecomment-497958320  

>Really looking forward to have some improvements for portable binary storage  
This is the real solution.  There is a portable binary archive in the examples.  And there is a submitted one which has been around for many years and I understand often used. See EOS Portable Archive project.  Looked at integrating it into the official boost package but it turned out to be more effort than I had anticipated.  Anyway, it might be the exact solution your looking for.

---

## Comment 5

> Username: SharpSnake  
> Created at: 2019-06-03 01:46:13 UTC  
> Updated at: 2019-06-03 01:46:39 UTC  
> Url: https://github.com/boostorg/serialization/issues/159#issuecomment-498086821  

I found it, and i will look into this project later.  
Thank you very much, sir. @robertramey
