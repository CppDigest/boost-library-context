# #401 - Segmentation fault when reading purposely corrupted png image (related to jpeg) [Closed]

> Username: misos1  
> Created at: 2019-11-06 17:19:18 UTC  
> Updated at: 2020-01-15 10:35:40 UTC  
> Closed at: 2020-01-15 10:34:43 UTC  
> Url: https://github.com/boostorg/gil/issues/401  

### Actual behavior  
  
Segmentation fault - signal SIGSEGV.  
  
In [png read.hpp](https://github.com/boostorg/gil/blob/master/include/boost/gil/extension/io/png/detail/read.hpp#L231) in functions which use png library like `apply` and `read_rows` is missing `setjmp` setup as is done in [jpeg read.hpp](https://github.com/boostorg/gil/blob/develop/include/boost/gil/extension/io/jpeg/detail/read.hpp#L171). Latest `setjmp` is used in [png reader_backend.hpp](https://github.com/boostorg/gil/blob/master/include/boost/gil/extension/io/png/detail/reader_backend.hpp#L124) in function `read_header` so for example any png error during decompression in `read_rows` in `png_read_rows` will cause `longjmp` to jump to this place which is inside function which frame at this point is already destroyed and so subsequent call to `png_destroy_read_struct` causes crash.  
  
```  
libpng error: Not enough image data  
Process 20104 stopped  
* thread #1, name = 'a.out', stop reason = signal SIGSEGV: invalid address (fault address: 0x0)  
    frame #0: 0x00007ffff7bb3cbd libpng16.so.16`png_destroy_read_struct + 13  
libpng16.so.16`png_destroy_read_struct:  
```  
  
### Expected  behavior  
  
Gracefully thrown exception.  
  
### C++ Minimal Working Example  
  
```cpp  
#include <boost/gil.hpp>  
#include <boost/gil/extension/io/png.hpp>  
  
int main()  
{  
	std::initializer_list<unsigned char> corrupt_png = {  
		0x89, 'P', 'N', 'G', 0x0D, 0x0A, 0x1A, 0x0A,  
		0x00, 0x00, 0x00, 0x0D,  
		'I', 'H', 'D', 'R',  
		0x00, 0x00, 0x04, 0x00,  
		0x00, 0x00, 0x05, 0xA9,  
		0x08, 0x02, 0x00, 0x00, 0x00,  
		0x68, 0x1B, 0xF7, 0x46,  
		0x00, 0x00, 0x00, 0x00,  
		'I', 'D', 'A', 'T',  
		0x35, 0xAF, 0x06, 0x1E,  
		0x00, 0x00, 0x00, 0x00,  
		'I', 'E', 'N', 'D',  
		0xAE, 0x42, 0x60, 0x82  
	};  
	std::stringstream ss(std::string(corrupt_png.begin(), corrupt_png.end()), std::ios_base::in | std::ios_base::binary);  
	boost::gil::rgb8_image_t img;  
	boost::gil::read_image(ss, img, boost::gil::png_tag{});  
	return 0;  
}  
```  
  
### Environment  
  
- Compiler version: -  
- Build settings: -  
- Version (Git ref or `<boost/version.hpp>`): latest master 1_71

---

## Comment 1

> Username: misos1  
> Created at: 2019-11-06 19:54:24 UTC  
> Url: https://github.com/boostorg/gil/issues/401#issuecomment-550474355  

Same problem is with [png write.hpp write_view](https://github.com/boostorg/gil/blob/develop/include/boost/gil/extension/io/png/detail/write.hpp#L123) and [png writer_backend.hpp write_header](https://github.com/boostorg/gil/blob/develop/include/boost/gil/extension/io/png/detail/writer_backend.hpp#L99). Only place with `setjmp` is at the end of `writer_backend` constructor. Although it is harder to construct test case.

---

## Comment 2

> Username: misos1  
> Created at: 2019-11-07 20:01:40 UTC  
> Url: https://github.com/boostorg/gil/issues/401#issuecomment-551240386  

There was same problem with jpeg but that was fixed:  
  
http://boost.2283326.n4.nabble.com/gil-Can-not-open-test-jpg-td2671443i13.html  
  
There is mentioned also same update for libpng which was probably forgotten.

---

## Comment 3

> Username: mloskot  
> Created at: 2019-11-11 00:17:23 UTC  
> Url: https://github.com/boostorg/gil/issues/401#issuecomment-552250945  

@misos1 Thank you very much for the excellent bug report and the history digging.   
Although I will try to look at this issue after the weekend, as labelled, we will appreciate any help/pull request in fixing the problem.

---

## Comment 4

> Username: simmplecoder  
> Created at: 2019-11-11 07:36:45 UTC  
> Updated at: 2019-11-24 12:02:18 UTC  
> Url: https://github.com/boostorg/gil/issues/401#issuecomment-552327633  

@mloskot, I can have a look at it after November 22. If I won't come back please ping me.  
  
**EDIT:** It might take a bit longer, probably after finals are over (around 4th of December).

---

## Comment 5

> Username: simmplecoder  
> Created at: 2020-01-03 18:23:40 UTC  
> Url: https://github.com/boostorg/gil/issues/401#issuecomment-570655568  

@misos1, I believe I fixed the problem. Let me know if you have any other test cases in mind. You can also have a look at https://github.com/boostorg/gil/pull/414 . Thanks for providing such a thorough bug report and a great test case.

---

## Comment 6

> Username: misos1  
> Created at: 2020-01-09 16:55:23 UTC  
> Url: https://github.com/boostorg/gil/issues/401#issuecomment-572653639  

@simmplecoder Looks well but there are more places where is missing setjmp. For example:  
  
- in jpeg reader_backend is setjmp covering all jpeg calls but in png reader_backend it starts after some png calls like png_create_info_struct (I would assume that all png functions can longjmp even if it would be due to unsuccessful allocation)  
  
- in jpeg scanline_read is called setjmp also in functions read and skip  
  
- in jpeg write is setjmp in write_rows, there is no such function in png write instead there is write_view  
  
- in jpeg writer_backend in writer_backend constructor, in png writer_backend is setjmp called after some calls to png functions and I am not sure if write_header is covered  
  
Also I noticed in jpeg writer_backend destructor is called jpeg_finish_compress and there is no setjmp so I am not sure if this is covered.  
  
Some calls to setjmp in jpeg may be superfluous and some may be missing. It is hard to see especially in such fragmented code. That is one of problems of setjmp/longjmp. It is ok to have one setjmp call in toplevel function and omit it in functions which are called only from it. I would either put setjmp at start of each function which calls some jpeg/png functions or analyse full call tree whether are all covered and none function which calls png/jpeg functions is called without set setjmp.

---

## Comment 7

> Username: mloskot  
> Created at: 2020-01-09 17:01:25 UTC  
> Url: https://github.com/boostorg/gil/issues/401#issuecomment-572656445  

@misos1 Thank you very much for the detailed review of the issues in the JPEG I/O.  
  
This #414 and its follow-up #401, are focused specifically on the PNG I/O and I'd suggest to keep it PNG-specific.  
  
Then, I'd suggest to report your JPEG details as new issue which can be followed by new PRs.

---

## Comment 8

> Username: misos1  
> Created at: 2020-01-09 17:10:27 UTC  
> Updated at: 2020-01-09 17:11:20 UTC  
> Url: https://github.com/boostorg/gil/issues/401#issuecomment-572660126  

@mloskot With that 4 points at top I meant where are calls to setjmp in jpeg extension but missing in png extension where they probably should be also. My notice about jpeg writer_backend destructor may be wrong as I am really not sure whether it is ok or not without analysing full call tree. So in last paragraph I suggested how it could be resolved in both jpeg and png. I would prefer it clear and call setjmp in each function which uses png/jpeg functions so it would be clear on glance that there is no missing setjmp for the price of performance rather than to analyse call tree and open possibility that some future change will create another bug.

---

## Comment 9

> Username: simmplecoder  
> Created at: 2020-01-09 17:13:10 UTC  
> Updated at: 2020-01-09 17:14:02 UTC  
> Url: https://github.com/boostorg/gil/issues/401#issuecomment-572661191  

@mloskot, I believe we can fix this by just creating a template function that does the setjmp and throws on error.  
  
Something like this:  
```  
template <typename F, typename ... Args>  
[[ noreturn ]] void wrap_jmp_call(std::jmp_buf buf, F&& callable, Args&& ... args)   
{  
    if (std::setjmp(buf))   
    {  
        io_error("<png/libjpeg> is invalid");  
    }  
    std::forward<F>(callable)(std::forward<Args>(args)...);  
}  
```  
  
Then it could be called like this:  
  
`wrap_jmp_call(/*get the jmp buf*/, [this]() {/*call already implemented member function*/});`

---

## Comment 10

> Username: mloskot  
> Created at: 2020-01-09 17:21:52 UTC  
> Updated at: 2020-01-09 17:22:32 UTC  
> Url: https://github.com/boostorg/gil/issues/401#issuecomment-572664761  

@misos1 My apologies, apparently I only skimmed your comment.   
  
>  I would prefer it clear and call setjmp in each function which uses png/jpeg functions   
  
Yes, I agree with it  
  
In https://github.com/boostorg/gil/pull/414#issuecomment-572201988 I recalled archived discussion on the list about the GIL I/O and `setjmp` issues. In that thread Phil Endecott made very similar suggestion, https://lists.boost.org/Archives/boost/2010/03/163724.php:  
  
```  
1. setjmp() in each function (scope?) that calls into libjpeg. That's  
nasty because setjmp(), IIRC, is detected by the compiler and prevents  
some optimisations. It's relatively non-intrusive in your code though.   
```  
  
Finally, @chhenning agreed it's a reasonable choice https://lists.boost.org/Archives/boost/2010/03/163788.php  
  
I also agree it's a good idea to apply.  
  
@simmplecoder what do you think?

---

## Comment 11

> Username: misos1  
> Created at: 2020-01-09 17:34:54 UTC  
> Updated at: 2020-01-09 17:35:06 UTC  
> Url: https://github.com/boostorg/gil/issues/401#issuecomment-572669703  

On the other side as I am thinking about it setjmp involves saving context what means sizeof(jmp_buf) which is 148 bytes on x64. Doing this for each image scanline (as in jpeg extension) means it may be as expensive as decoding that scanline for color images with width below few tens of pixels. It could be good if it can be skipped in such contexts and instead put it before reading scanlines.

---

## Comment 12

> Username: mloskot  
> Created at: 2020-01-09 21:52:54 UTC  
> Updated at: 2020-01-09 21:54:18 UTC  
> Url: https://github.com/boostorg/gil/issues/401#issuecomment-572773466  

@misos1  
https://github.com/boostorg/gil/issues/401#issuecomment-572653639  
> Some calls to setjmp in jpeg may be superfluous and some may be missing. It is hard to see especially in such fragmented code.   
  
https://github.com/boostorg/gil/issues/401#issuecomment-572660126  
> I would either put setjmp at start of each function which calls some jpeg/png functions or analyse full call tree whether are all covered and none function which calls png/jpeg functions is called without set setjmp.  
>   
> It is hard to see especially in such fragmented code.   
  
Yes, reviewing the call tree to find the *root* level functions that can be sole places to call `setjmp` is an important task here. It would be very helpful to clarify the picture and add inline comments explaining why `setjmp` is called here and omited there.  
  
I will try to find moment and run such review over this weekend. First, I'll pick the JPEG implementation, then we can project the findings to the PNG one.  
  
------  
  
@simmplecoder Your idea in https://github.com/boostorg/gil/issues/401#issuecomment-572661191 seems valid, but let's try to clarify the call tree first. Chances are, we don't need it and  will stick to limited explicit `setjmp`-s.

---

## Comment 13

> Username: misos1  
> Created at: 2020-01-10 13:08:32 UTC  
> Url: https://github.com/boostorg/gil/issues/401#issuecomment-573028703  

Another important aspect of longjmp is that C++ destructors are not called for local variables declared after setjmp when longjmp occurs, they are called only for local variables declared before as mentioned here:  
  
https://github.com/boostorg/gil/blob/a1182da3f4b1221e68285292851af6ae8769e0ce/include/boost/gil/extension/io/jpeg/detail/read.hpp#L162-L174  
  
This is why there is additional setjmp in private method read_rows notwithstanding that this function is called only from apply which calls setjmp at start. So although #414 fixes segfault when reading corrupted png from this issue it probably leaks some memory.  
  
So either there must be additional setjmp after we read header to get image dimensions and declaration of buffer local variable for which we use these dimensions or this variable must be declared before top level setjmp or be class member (allocation itself can occur after setjmp).

---

## Comment 14

> Username: mloskot  
> Created at: 2020-01-10 20:54:55 UTC  
> Updated at: 2020-01-10 21:07:37 UTC  
> Url: https://github.com/boostorg/gil/issues/401#issuecomment-573201377  

### JPEG reading methods and setjmp scopes  
  
There are two methods of reading JPEG rows which are distinct and only share common point of `setjmp` for reading of the image info/metadata by `reader_backend`:  
  
1. `reader` - no `setjmp` per scanline; two clear scopes with libjpeg calls, outer and inner.  
2. `scanline_reader` - with `setjmp` per scanline; multiple parallel scopes with libjpeg calls  
  
As per @misos1 https://github.com/boostorg/gil/issues/401#issuecomment-572669703, the 1 is optimal and the 2 may suffer from performance hit corresponding to numerous `setjmp`.  
  
It does not seem possible to avoid `setjmp` in `scanline_reader` per scanline in `read` or `skip`.  
  
### Conclusion  
  
I think we should not die hard to avoid the hit for the scanline-wise reading, especially if it would mean trading the exception safety. Users still have choice of the faster method.    
Shortly, I'd leave the JPEG implementation as it is (adding `setjmp` if any are missing).  
  
We still have to address your findings in #416   
  
### Examples  
  
1. Read image metadata only  
  
    ```cpp  
    auto b = gil::read_image_info(file_jpg, gil::jpeg_tag{});  
        // 1x setjmp in reader_backend ctor, in jpeg/detail/reader_backend.hpp  
        // sole scope of calls to libjpeg  
    auto i = b.get_info();  
    ```  
  
2. Read all data at once (`reader::read_rows`)  
  
    ```cpp  
    gil::rgb8_image_t img;  
    gil::read_image(file_jpg, img, gil::jpeg_tag{});  
        // 2x setjmp in jpeg/detail/read.hpp  
        // in reader::apply (outer scope)  
        // in reader::read_rows (inner scope, but not redundant , see also misos1's earlier comment)  
    ```  
  
3. Read individual scanlines (`scanline_reader::read`)  
  
    ```cpp  
    auto r = gil::make_scanline_reader(file_jpg, gil::jpeg_tag{});  
        // 1x setjmp in reader_backend ctor (scope 1), in jpeg/detail/reader_backend.hpp  
    auto i = r.begin(); // no calls to libjpeg  
    auto e = r.end();   // no calls to libjpeg  
    while (i != e)      // no calls to libjpeg  
    {  
        *i;  // 1x setjmp in scanline_reader::read (scope 2, outer), from scanline_read_iterator::dereference  
                // 0x setjmp in scanline_reader::read_scanline (inner scope, no need of setjmp)  
                // skipping disabled  
        ++i; // re-enables skipping, but no calls to scanline_reader::skip follow  
    }  
    ```  
  
4. Skip individual scanlines (`scanline_reader::skip`)  
  
    ```cpp  
    auto r = gil::make_scanline_reader(file_jpg, gil::jpeg_tag{});  
        // 1x setjmp in reader_backend ctor (scope 1), in jpeg/detail/reader_backend.hpp  
    auto i = r.begin(); // no calls to libjpeg  
    auto e = r.end();   // no calls to libjpeg  
    while (i != e)      // no calls to libjpeg  
        ++i; // 1x setjmp in scanline_reader::skip (scope 2), from scanline_read_iterator::increment  
    ```  
  
-----  
  
@misos1 Perhaps long day but I don't quite get your https://github.com/boostorg/gil/issues/401#issuecomment-573028703  
  
> So either there must be additional setjmp **after** we read header to get image dimensions and declaration of buffer local variable for which we use these dimensions or this variable must be declared **before** top level setjmp or be class member (allocation itself can occur **after** setjmp).  
  
Currently, doesn't call to `setjmp` occur after the image info is read and buffer declared?  
Or, which variable you mean to move **before** (the `buffer` is before, isn't it?).

---

## Comment 15

> Username: misos1  
> Created at: 2020-01-13 21:35:46 UTC  
> Url: https://github.com/boostorg/gil/issues/401#issuecomment-573882220  

> Currently, doesn't call to setjmp occur after the image info is read and buffer declared?  
> Or, which variable you mean to move before (the buffer is before, isn't it?).  
  
Yes I just mentioned that there are alternatives which would allow to skip that second setjmp. That buffer can be declared in outer function before first setjmp and passed by reference to `apply` or declared as class variable.
