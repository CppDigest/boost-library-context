# #224 - TIFF I/O asserts with empty image [Open]

> Username: sdebionne  
> Created at: 2019-01-25 17:05:35 UTC  
> Updated at: 2019-07-23 19:32:52 UTC  
> Url: https://github.com/boostorg/gil/issues/224  

### Minimal Working Example (in C++)  
  
```cpp  
/* whatever image type*/ image(); //empty image  
tiff_write_view("test.tif", image);  
```  
  
### Actual behavior  
  
Assert: `front() called on empty vector` reported by MSVC in Debug  
In file `boost\gil\extension\io\tiff\detail\write.hpp`.  
  
### Expected  behavior  
  
Parameter checking should be done upfront, maybe in `write_view()`.  
  
### Notes  
  
This might apply to other I/O extensions.  
  
Also, could you fix the indentation in the I/O extension files (mix of tabs / space)? Or are you planning on generalizing the use of `clang-format` in the future?  
  
TBH, I am a bit puzzled with the new I/O framework (I have extensions written the old way). Looking at the TIFF format for instance:  
  
Old: 29 KB in two files  
New: 114 KB in 13 files  
  
What are the new functionalities that require such a large number of additional code? Furthermore the documentation in "Extending GIL::IO with new Formats" makes it sound like it's an easy job :-)

---

## Comment 1

> Username: mloskot  
> Created at: 2019-01-25 18:11:44 UTC  
> Url: https://github.com/boostorg/gil/issues/224#issuecomment-457666309  

You've made good point about the parameters checking.  
  
-----  
  
The this announcement of [Formal Review of IO and Toolbox extensions to Boost.GIL](https://lists.boost.org/boost-announce/2010/11/0273.php) includes points provided by @chhenning - author of the new IO (aka IOv2).  
  
----  
  
The `clang-format` is being evaluated, see #87, but that is not ideal or as capable as it may seem.  
  
Currently, I'm preparing for MPL to MP11 overhaul and part of the preparation as well as the switch itself is **manual** reformatting to follow the recently agreed [Guidelines](https://github.com/boostorg/gil/blob/develop/CONTRIBUTING.md#guidelines), to make the code less horizontally 'runny', wrapped at max 90 characters, aligned to left-edge of editor, using `auto` and trailing return,  
and to make complex constructs more readable.   
  
Here are samples of the aimed format:  
  
https://github.com/boostorg/gil/blob/54f1817e991c44e42c50fcbf91ad95a37f965a19/include/boost/gil/color_base.hpp#L26-L33  
  
https://github.com/boostorg/gil/blob/54f1817e991c44e42c50fcbf91ad95a37f965a19/include/boost/gil/io/get_writer.hpp#L62-L83  
  
If you know how to achieve similar to this (or similar to other highly-templated code like Boost.Spirit)  
using `clang-format`, please feel free to chime into the #87 discussion.
