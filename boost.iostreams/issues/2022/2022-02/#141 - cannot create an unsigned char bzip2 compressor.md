# #141 - cannot create an unsigned char bzip2 compressor [Open]

> Username: Spongman  
> Created at: 2022-02-01 03:11:34 UTC  
> Updated at: 2022-02-01 19:31:21 UTC  
> Url: https://github.com/boostorg/iostreams/issues/141  

https://godbolt.org/z/6Gq67Yd8x  
```  
	filtering_stream<output, uint8_t> out;  
	out.push(basic_bzip2_compressor<std::allocator<uint8_t>>{});  
```  
  
This is due to `bzip2_base::char_type` being hard-coded:  
  
https://github.com/boostorg/iostreams/blob/9edd46fe730e2086675a91629287224bd92590ab/include/boost/iostreams/filter/bzip2.hpp#L154-L156

---

## Comment 1

> Username: rdoeffinger  
> Created at: 2022-02-01 18:57:56 UTC  
> Url: https://github.com/boostorg/iostreams/issues/141#issuecomment-1027180830  

I think it's hard-coded because much of the C compression API uses "char" so making it generic would cause lots of other issues.  
Also this isn't just bzip2, isn't it? zlib is the same as far as I can tell.  
This might be a kind of intentional design choice...

---

## Comment 2

> Username: Spongman  
> Created at: 2022-02-01 19:04:10 UTC  
> Updated at: 2022-02-01 19:04:38 UTC  
> Url: https://github.com/boostorg/iostreams/issues/141#issuecomment-1027187036  

Sure, in the same way that _any_ wrapper around a C API has hard-coded types. But surely this can be done in such a way that `basic_bzip2_compressor<std::allocator<uint8_t>>::char_type` returns the correct thing?

---

## Comment 3

> Username: rdoeffinger  
> Created at: 2022-02-01 19:25:08 UTC  
> Url: https://github.com/boostorg/iostreams/issues/141#issuecomment-1027205015  

Note: I am not speaking with any particular authority and certainly not for the boost project.  
Maybe I misunderstand, but I think it returns exactly the right thing: The bzip2 compressor always operates on "char" streams. Changing the allocator does not change the stream type.  
Reading the code I suspect the easier way to make your example work would be to have some wrapper that just adds a couple of casts around the function calls. But that is rather ugly, and possibly not strictly safe to actually do in all cases/environments?  
Certainly not for any type other than uint8_t, so not sure the result will be any less of a mess than just using a "char" filtering stream and do any casts necessary on the input and output side.

---

## Comment 4

> Username: Spongman  
> Created at: 2022-02-01 19:26:20 UTC  
> Updated at: 2022-02-01 19:28:40 UTC  
> Url: https://github.com/boostorg/iostreams/issues/141#issuecomment-1027206026  

If that's the case what is the point of having a polymorphic `basic_bzip2_compressor` in the first place?  
  
I mean, we _could_ have all C++ API just take `void *` everywhere and expect the user to cast everywhere, but that makes for a pretty poor API. This holds in this case, too.

---

## Comment 5

> Username: rdoeffinger  
> Created at: 2022-02-01 19:28:09 UTC  
> Updated at: 2022-02-01 19:31:21 UTC  
> Url: https://github.com/boostorg/iostreams/issues/141#issuecomment-1027207512  

The polymorphism is to allow replacing the allocator, e.g. to allocate from a fixed memory pool on some embedded device. I.e. HOW the memory is allocated, not which TYPE.  
And I don't disagree in principle that it would be nice if it could just accept any kind of type, but I think that's not actually easy to do, nor end up very useful in the end. It looks to me in general the only thing it is meant for when implemented is to support both char and wchar_t
