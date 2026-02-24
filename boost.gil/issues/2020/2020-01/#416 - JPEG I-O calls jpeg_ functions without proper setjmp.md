# #416 - JPEG I/O calls jpeg_ functions without proper setjmp [Closed]

> Username: misos1  
> Created at: 2020-01-09 20:17:58 UTC  
> Updated at: 2020-02-26 07:55:28 UTC  
> Closed at: 2020-02-26 03:08:08 UTC  
> Url: https://github.com/boostorg/gil/issues/416  

This is sister issue of #401 for JPEG I/O.  
  
One example is writer_backend destructor:  
https://github.com/boostorg/gil/blob/79ad37f75a07c8d0931afbfd32e6cbfed9af7271/include/boost/gil/extension/io/jpeg/detail/writer_backend.hpp#L114-L118  
  
When called from write_view there is no valid setjmp where longjmp from jpeg_ functions can jump. If it happens it will jump into some function with already destroyed frame like writer_backend constructor or write_rows. I think we should not assume that jpeg_finish_compress or any jpeg_ function cannot call error handler.  
  
There can be more such places. Possible solution would be to setup setjmp at top of each function which calls jpeg_ functions.

---

## Comment 1

> Username: mloskot  
> Created at: 2020-01-10 21:30:37 UTC  
> Updated at: 2020-02-13 19:30:57 UTC  
> Url: https://github.com/boostorg/gil/issues/416#issuecomment-573213137  

> Possible solution would be to setup setjmp at top of each function which calls jpeg_ functions.  
  
I'm trying to think of a case when jumping to the destructor may be troublesome if there is `setjmp` in `~writer_backend()`, e.g.  
  
- the destructor is called during unwinding due to an exception  
- the libjpeg functions fail for some reason  
- `longjmp`  back to `~writer_backend()`  
- and we raise error throwing another exception - we have the [destructor that throws](https://akrzemi1.wordpress.com/2011/09/21/destructors-that-throw/) (unless it's `noexcept(false)`) - almost certain `terminate`   
  
AFAIK, `jpeg_destroy_compress` does not error (or it is not documented) and `jpeg_finish_compress`  errors only if invoked *"before writing the specified number of scanlines"* (how probable?).  
  
Swallowing exception in the destructor seems useful, also to show a reader we have attempted to deal with as well as we can, but it is not a solution to serious trouble.  
  
If that is still not sufficient, perhaps a better solution is to reset `writer_backend::_jerr.error_exit`, in beginning of `~writer_backend()`, with a no-op handler.  
  
/cc @stefanseefeld @chhenning @simmplecoder

---

## Comment 2

> Username: mloskot  
> Created at: 2020-01-13 12:07:48 UTC  
> Updated at: 2020-01-13 12:08:14 UTC  
> Url: https://github.com/boostorg/gil/issues/416#issuecomment-573632694  

@misos1   
> think we should not assume that jpeg_finish_compress or any jpeg_ function cannot call error handler.  
  
I can confirm that in the current set up if any of the two `jpeg_*` calls fail, it'll crash and terminate:  
  
```  
Exception thrown at 0x00007FF83D6BBF58 (ntdll.dll) in test_libjpeg_setjmp.exe: 0xC0000028: An invalid or unaligned stack was encountered during an unwind operation.  
Unhandled exception at 0x00007FF83D6BBF58 (ntdll.dll) in test_libjpeg_setjmp.exe: 0xC0000028: An invalid or unaligned stack was encountered during an unwind operation.  
```  
  
I have also verified this basic improvement and I think this is handling of the situation as best as we can (and quick):  
  
```cpp  
~writer_backend()  
{  
    try  
    {  
        if (setjmp(_mark)) { raise_error(); }  
  
        jpeg_finish_compress(get());  
        jpeg_destroy_compress(get());  
    }  
    catch (...)  
    {  
        // There is no way to recover and as a library  
        // we do not want to crash client, we can just swallow.  
        ;  
    }  
}  
```  
  
The test also cleared also my concerns from https://github.com/boostorg/gil/issues/416#issuecomment-573213137  
  
------  
  
The test I did was to  
- modify the libjpeg to always signal error from  `jpeg_finish_compress`.  
- run `gil::write_view(file_jpg_out, gil::view(img), gil::jpeg_tag{});`   
- modify GIL JPEG's `writer::write_rows` to throw and exercise `~writer_backend` raise-and-swallow with ongoing exception and unwinding.  
- run `gil::write_view(file_jpg_out, gil::view(img), gil::jpeg_tag{});`  again  
  
```c  
GLOBAL(void)  
jpeg_finish_compress (j_compress_ptr cinfo)  
{  
  JDIMENSION iMCU_row;  
  
  ERREXIT(cinfo, JERR_TOO_LITTLE_DATA);  
...  
}  
```  
  
Let's add the `setjmp` to the destructor. Thoughts?  
  
/cc @stefanseefeld @chhenning @simmplecoder

---

## Comment 3

> Username: misos1  
> Created at: 2020-01-13 21:57:11 UTC  
> Url: https://github.com/boostorg/gil/issues/416#issuecomment-573891170  

Why not just?  
``` c++  
~writer_backend()  
{  
        // There is no way to recover and as a library  
        // we do not want to crash client, we can just swallow.  
        if (setjmp(_mark)) { return; }  
  
        jpeg_finish_compress(get());  
        jpeg_destroy_compress(get());  
}  
```  
  
> AFAIK, jpeg_destroy_compress does not error (or it is not documented) and jpeg_finish_compress errors only if invoked "before writing the specified number of scanlines" (how probable?).  
  
Maybe it can happen if there is some error during writing scanlines (like file io error) then it throws and calls destructor while not all scanlines were written?

---

## Comment 4

> Username: mloskot  
> Created at: 2020-01-13 22:00:01 UTC  
> Updated at: 2020-01-13 22:00:38 UTC  
> Url: https://github.com/boostorg/gil/issues/416#issuecomment-573892315  

I think you've got a valid point.  
I was probably too paranoid proposing `try-catch (...)`.  
There is no reason or need to call `raise_error` and just `return` should do the job (with the comment).

---

## Comment 5

> Username: misos1  
> Created at: 2020-01-13 22:07:52 UTC  
> Updated at: 2020-01-13 22:12:16 UTC  
> Url: https://github.com/boostorg/gil/issues/416#issuecomment-573895411  

> Maybe it can happen if there is some error during writing scanlines (like file io error) then it throws and calls destructor while not all scanlines were written?  
  
Also if this case is possible it is safe to call jpeg_finish_compress after other jpeg_ function called error handler? I think jpeg_destroy_compress should be ok and maybe jpeg_finish_compress could be moved to better place if possible. Because as soon as jpeg_finish_compress longjumps then we would leak jpeg struct without calling jpeg_destroy_compress.  
  
I think destroying functions like jpeg_destroy_compress themselves do not need to be put under setjmp as such functions are supposed to be called also after longjmp for cleanup.

---

## Comment 6

> Username: misos1  
> Created at: 2020-01-13 22:18:02 UTC  
> Url: https://github.com/boostorg/gil/issues/416#issuecomment-573899376  

So maybe this would be better if jpeg_finish_compress must be in destructor:  
``` c++  
~writer_backend()  
{  
    // There is no way to recover and as a library  
    // we do not want to crash client, we can just swallow.  
    if (!setjmp(_mark))  
    {  
        jpeg_finish_compress(get());  
    }  
  
    jpeg_destroy_compress(get());  
}  
```

---

## Comment 7

> Username: misos1  
> Created at: 2020-01-13 22:37:41 UTC  
> Url: https://github.com/boostorg/gil/issues/416#issuecomment-573906318  

Just to note this nicely sums up how should be setjmp treated:  
https://en.cppreference.com/w/cpp/utility/program/longjmp  
  
> If the function that called setjmp has exited, the behavior is undefined (in other words, only long jumps up the call stack are allowed)  
>   
> No destructors for automatic objects are called. If replacing of std::longjmp with throw and setjmp with catch would execute a non-trivial destructor for any automatic object, the behavior of such std::longjmp is undefined.  
  
I mentioned that longjmp will not call destructors which can cause memory leaks. But the best is to avoid any such objects under setjmp.

---

## Comment 8

> Username: misos1  
> Created at: 2020-01-13 22:54:20 UTC  
> Updated at: 2020-01-15 10:39:55 UTC  
> Url: https://github.com/boostorg/gil/issues/416#issuecomment-573912001  

I think that second paragraph tells that for example this code should not be bad:  
``` c++  
    void read_rows( const View& view )  
    {  
        using buffer_t = std::vector<ImagePixel>;  
        buffer_t buffer( this->_info._width );  
  
        if( setjmp( this->_mark ))  
        {  
            this->raise_error();  
        }  
        something_which_calls_longjmp  
```  
  
When replaced longjmp and setjmp with throw and try/catch:  
  
``` c++  
    void read_rows( const View& view )  
    {  
        using buffer_t = std::vector<ImagePixel>;  
        buffer_t buffer( this->_info._width );  
  
        try // replaces setjmp  
        {  
            something_which_throws  
        }  
        catch() // replaces setjmp  
        {  
            this->raise_error();  
        }  
```  
  
We see that buffer is not destroyed by try/catch which replaces setjmp but by end of function scope so it should be ok. On the other hand if buffer was declared after setjmp it would appear inside try block in this thought replacement which would be bad.

---

## Comment 9

> Username: mloskot  
> Created at: 2020-01-13 23:05:28 UTC  
> Url: https://github.com/boostorg/gil/issues/416#issuecomment-573915720  

@misos1 You've got it right, I think, and your refinement in https://github.com/boostorg/gil/issues/416#issuecomment-573899376 is fine too. I have done some (limited) tests and the `buffer` is destroyed by the out-of-scope indeed.

---

## Comment 10

> Username: simmplecoder  
> Created at: 2020-02-13 11:18:45 UTC  
> Updated at: 2020-02-13 11:19:03 UTC  
> Url: https://github.com/boostorg/gil/issues/416#issuecomment-585677164  

To put setjmp/longjmp in the destructor, perhaps we could check if we are in the process of handling another exception?  
  
```c++  
if (std::current_exception() != nullptr)  
{  
    // don't even attempt anything else  
   goto cleanup_label;  
   return;  
}  
  
if (setjmp(this->_mark))  
{  
    goto cleanup_label;  
}  
  
jpeg_finish_compress(get())  
cleanup_label:  
jpeg_destroy_compress(get());  
```  
  
Perhaps I overkilled it with `goto`. `jpeg_desotry_compress` doesn't do error reporting, from the [refspec](https://refspecs.linuxbase.org/LSB_3.1.0/LSB-Desktop-generic/LSB-Desktop-generic/libjpeg.jpeg.destroy.decompress.1.html), but `jpeg_finish_compress` [does](https://refspecs.linuxbase.org/LSB_3.1.0/LSB-Desktop-generic/LSB-Desktop-generic/libjpeg.jpeg.finish.decompress.1.html). In essence, there will not be any problems, but perhaps trying to finish compressing might be undesirable. I've never used the library on its own, so I have no idea, just thoughts.

---

## Comment 11

> Username: mloskot  
> Created at: 2020-02-13 19:44:05 UTC  
> Url: https://github.com/boostorg/gil/issues/416#issuecomment-585938396  

@simmplecoder  I'm not entirely clear what is the benefit of this. I'd stick to apparently simpler alternative, either the suggested by @misos1  in https://github.com/boostorg/gil/issues/416#issuecomment-573899376 or the one I suggested with explicit `try-catch` in https://github.com/boostorg/gil/issues/416#issuecomment-573632694  
  
I take those two as equivalent. The only difference I can see is that the second one with the explicit `try-catch` allows a slight debugging convenience by telling debugger to automatically break on exception thrown from `raise_error`.

---

## Comment 12

> Username: misos1  
> Created at: 2020-02-13 19:48:05 UTC  
> Updated at: 2020-02-13 20:10:43 UTC  
> Url: https://github.com/boostorg/gil/issues/416#issuecomment-585940186  

Or maybe (which should be equivalent but without goto):  
``` c++  
~writer_backend()  
{  
    // There is no way to recover and as a library  
    // we do not want to crash client, we can just swallow.  
    if (std::current_exception() == nullptr)  
    {  
        if (!setjmp(_mark))  
        {  
            jpeg_finish_compress(get());  
        }  
    }  
  
    jpeg_destroy_compress(get());  
}  
```  
As I understand the point is to not call jpeg_finish_compress when some other jpeg function already failed. (Maybe with different comment as it is little confusing in my version vs original inside catch.) They do not call jpeg_finish_compress in their jpeg example in such case. But it may be ok to do so. And maybe the best would be to call jpeg_finish_compress somewhere else than destructor if possible, lets say right after we wrote all rows: https://github.com/boostorg/gil/blob/5f3c00244bdc8128d53fa569d17a12a8b80642db/include/boost/gil/extension/io/jpeg/detail/write.hpp#L123-L135

---

## Comment 13

> Username: mloskot  
> Created at: 2020-02-13 21:38:07 UTC  
> Updated at: 2020-02-13 21:40:46 UTC  
> Url: https://github.com/boostorg/gil/issues/416#issuecomment-585984084  

> As I understand the point is to not call jpeg_finish_compress when some other jpeg function already failed.  
  
Yes, it makes sense. I somewhat missed that from @simmplecoder comment, my bad.  
  
> maybe the best would be to call jpeg_finish_compress somewhere else than destructor if possible, lets say right after we wrote all rows:  
  
Yes, I think it's a good idea. Then, in theory, the handling in the destructor would never handle anything. I think we can keep both anyway.  
  
Shall we finally go for @misos1 's idea from https://github.com/boostorg/gil/issues/416#issuecomment-585940186? @simmplecoder, @stefanseefeld What you think?   
  
(Although I lost my hope to be 100% confident I grasped all the `setjmp` nuances in general and specifically in the libraries we use, I think it's a good solution.)

---

## Comment 14

> Username: misos1  
> Created at: 2020-02-13 21:45:40 UTC  
> Updated at: 2020-02-13 21:49:06 UTC  
> Url: https://github.com/boostorg/gil/issues/416#issuecomment-585987119  

I would just put it here and remove it from destructor completely so in destructor would be only one line with `jpeg_destroy_compress` without any setjmp or other exception checking (after check that this is the only place where it is needed, but this is the only place with `jpeg_start_compress`):  
``` c++  
        jpeg_start_compress( this->get()  
                           , TRUE  
                           );  
  
        JSAMPLE* row_addr = reinterpret_cast< JSAMPLE* >( &row_buffer[0] );  
  
        for( int y =0; y != view.height(); ++ y )  
        {  
            std::copy( view.row_begin( y )  
                     , view.row_end  ( y )  
                     , row_buffer.begin()  
                     );  
  
            jpeg_write_scanlines( this->get()  
                                , &row_addr  
                                , 1  
                                );  
        }  
  
        jpeg_finish_compress( this->get() );  
```  
I think `jpeg_finish_compress` just finishes work started by `jpeg_start_compress` after writing all scanlines.

---

## Comment 15

> Username: mloskot  
> Created at: 2020-02-13 21:53:18 UTC  
> Url: https://github.com/boostorg/gil/issues/416#issuecomment-585990011  

Okay. I've double-checked the destructor is a sole place where `jpeg_finish_compress` is called.  
  
I'm shutting down the paranoid side of self now 😄
