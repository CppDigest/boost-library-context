# #402 - Possibility to disable (or enhance) logging from png and jpeg extensions (maybe others) [Open]

> Username: misos1  
> Created at: 2019-11-06 20:14:48 UTC  
> Updated at: 2020-01-22 19:24:09 UTC  
> Url: https://github.com/boostorg/gil/issues/402  

### Is your feature request related to a problem? Please describe.  
  
In case of error both [libjpeg](https://github.com/boostorg/gil/blob/develop/include/boost/gil/extension/io/jpeg/detail/writer_backend.hpp#L94) and [libpng](https://github.com/boostorg/gil/blob/develop/include/boost/gil/extension/io/png/detail/writer_backend.hpp#L59) will write message to stderr in contrast with [libtiff](https://github.com/boostorg/gil/blob/develop/include/boost/gil/extension/io/tiff/detail/log.hpp#L23) where can be logging turned off.  
  
### Describe the solution you'd like  
  
Possibility to turn off writing error messages to stderr.  
  
### Describe alternatives you've considered  
  
Maybe incorporate error messages from used libraries into exception error string. So instead of something like this:  
  
stderr: `libpng error: [00][00][00][00]: invalid chunk type`  
C++ exception what(): "png is invalid: iostream error"  
  
It could be:  
  
C++ exception what(): "png is invalid: [00][00][00][00]: invalid chunk type"  
  
This would be nice for all extensions including tiff.

---

## Comment 1

> Username: simmplecoder  
> Created at: 2020-01-22 18:06:24 UTC  
> Url: https://github.com/boostorg/gil/issues/402#issuecomment-577312615  

Could you please provide an example where a warning would be generated? I believe I can set a custom handler for libpng warnings and just throw a warning exception (not the best idea?). I believe it is allowed to return to the caller if a warning is encountered, thus a user provided function might be reasonable idea as well. I'll re-read and quote the official docs later on the last sentence.

---

## Comment 2

> Username: misos1  
> Created at: 2020-01-22 18:58:06 UTC  
> Url: https://github.com/boostorg/gil/issues/402#issuecomment-577333917  

In #401: "libpng error: Not enough image data"  
  
Error mentioned in this issue should be generated for example when using png data:  
```  
		0x89, 'P', 'N', 'G', 0x0D, 0x0A, 0x1A, 0x0A,  
		0x00, 0x00, 0x00, 0x00,  
		0x00, 0x00, 0x00, 0x00,  
```

---

## Comment 3

> Username: simmplecoder  
> Created at: 2020-01-22 19:05:00 UTC  
> Url: https://github.com/boostorg/gil/issues/402#issuecomment-577336828  

At the moment I am forwarding whatever error message is passed to the  
custom error handler, please have a look at  
https://github.com/boostorg/gil/pull/428 . I was thinking if warnings  
should be propagated upwards too, or just swallowed.

---

## Comment 4

> Username: misos1  
> Created at: 2020-01-22 19:06:53 UTC  
> Updated at: 2020-01-22 19:08:29 UTC  
> Url: https://github.com/boostorg/gil/issues/402#issuecomment-577337547  

Regarding warnings I found in sources for example this:  
  
https://github.com/glennrp/libpng/blob/301f7a14295a3bdfaf406dbb5004d0784dc137ea/png.c#L2546-L2559  
  
But seems libpng must be compiled with PNG_WARNINGS_SUPPORTED. So maybe I would try with IHDR like this:  
  
```  
		0x00, 0x00, 0x00, 0x0D,  
		'I', 'H', 'D', 'R',  
		0x00, 0x00, 0x00, 0x00,  
		0x00, 0x00, 0x05, 0xA9,  
		0x08, 0x02, 0x00, 0x00, 0x00,  
		// crc32 checksum of this IHDR  
```

---

## Comment 5

> Username: misos1  
> Created at: 2020-01-22 19:19:09 UTC  
> Url: https://github.com/boostorg/gil/issues/402#issuecomment-577342603  

I think warning function should return to libpng and do not trigger exceptions. If are warnings in libpng disabled by default maybe would be best to not set warning handler. Or provide user function as you mentioned.

---

## Comment 6

> Username: simmplecoder  
> Created at: 2020-01-22 19:24:09 UTC  
> Url: https://github.com/boostorg/gil/issues/402#issuecomment-577344773  

Perhaps I could create a header file that would define the no warnings by  
default, and be empty (except include guards), if an option is passed  
through CMake/build2?  
  
I don’t know how to do that, but with some guidance I am eager to learn.  
  
/cc @mloskot , @stefanseefeld, what do you think?
