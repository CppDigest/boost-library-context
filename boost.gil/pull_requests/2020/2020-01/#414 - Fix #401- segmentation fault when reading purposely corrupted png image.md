# #414 Fix #401: segmentation fault when reading purposely corrupted png image [Merged]

> Username: simmplecoder  
> Created at: 2020-01-03 18:22:23 UTC  
> Updated at: 2020-01-15 10:35:31 UTC  
> Merged at: 2020-01-15 10:34:43 UTC  
> Closed at: 2020-01-15 10:34:43 UTC  
> Url: https://github.com/boostorg/gil/pull/414  

### Description  
  
This pull request fixes https://github.com/boostorg/gil/issues/401#issue-518615480 by replicating the mechanism used in jpeg reader. Since on initialization the error handling mechanism is not selected, libpng uses default one, which is through setjmp/longjmp (the same as in jpeg). There is also a test that confirms the case is covered.  
  
### References  
  
https://github.com/boostorg/gil/issues/401#issue-518615480  
  
### Tasklist  
  
- [X] Fix erratic behavior  
- [X] Add test case(s)  
- [x] Ensure all CI builds pass  
- [x] Review and approve

---

## Comment 1

> Username: simmplecoder  
> Created_at: 2020-01-08 12:29:17 UTC  
> Updated_at: 2020-01-08 12:30:20 UTC  
> Url: https://github.com/boostorg/gil/pull/414#issuecomment-572027145  

Should I merge this? I don't see big opportunities for improvement. It is possible to set custom function for error handler, but I don't see much of a point. Exception is thrown immediately afterwards, so I don't think performance is primary goal, nor do I think custom handler will add much clarity.

---

## Review 2 [Changes requested]

> Username: mloskot  
> Created_at: 2020-01-08 18:17:55 UTC  
> State: CHANGES_REQUESTED  
> Url: https://github.com/boostorg/gil/pull/414#pullrequestreview-340037504  

Sorry for delayed review. Please, could you address the two minor comments?  
  
FYI, I have checked the fix at run-time using VS2019

📁 include/boost/gil/extension/io/png/detail/read.hpp

```diff
  21 | #include <boost/gil/io/reader_base.hpp>
  22 | #include <boost/gil/io/row_buffer_helper.hpp>
  23 | #include <boost/gil/io/typedefs.hpp>
```

> Username: mloskot  
> Created_at: 2020-01-08 17:52:28 UTC  
> Updated_at: 2020-01-14 16:15:17 UTC  
> Url: https://github.com/boostorg/gil/pull/414#discussion_r364362214  

Missing `#include <csetjmp>`, before the `#include <type_traits>`.


📁 test/extension/io/png_read_test.cpp

```diff
 734 |+ // author: https://github.com/misos1
 735 |+ 
 736 |+ 	std::initializer_list<unsigned char> corrupt_png = {
```

> Username: mloskot  
> Created_at: 2020-01-08 18:13:21 UTC  
> Updated_at: 2020-01-14 16:15:17 UTC  
> Url: https://github.com/boostorg/gil/pull/414#discussion_r364371044  

Please, indent with 4 spaces and no tabs.  
  
https://github.com/boostorg/gil/blob/develop/CONTRIBUTING.md#guidelines

---

📁 test/extension/io/png_read_test.cpp

```diff
 761 |+         // the description passd is "png_is_invalid"
 762 |+         // the exact error message is thus not checked
 763 |+         return;
```

> Username: mloskot  
> Created_at: 2020-01-08 18:16:53 UTC  
> Updated_at: 2020-01-14 16:15:17 UTC  
> Url: https://github.com/boostorg/gil/pull/414#discussion_r364372414  

For now, this is fine to not to check exact error.  
  
I think the `setjmp` and `longjmp` needs some reviewing and unification across the library in near future, e.g. address [TODO-s](https://github.com/boostorg/gil/blob/master/include/boost/gil/extension/io/jpeg/detail/read.hpp#L162-L174).  
  
Especially, to hunt for potential undefined behaviours, for example, here is detailed discussions of the jumping issues potential in GIL IO:  
  
1. https://lists.boost.org/Archives/boost//2010/03/163676.php  
2. https://lists.boost.org/Archives/boost//2010/03/163679.php  
3. https://lists.boost.org/Archives/boost//2010/03/163724.php  
4. https://lists.boost.org/Archives/boost//2010/03/163788.php  
  
I continued on those in https://github.com/boostorg/gil/pull/414#issuecomment-572201988  
  
(/cc @stefanseefeld any insights appreciated)


---

## Comment 3

> Username: mloskot  
> Created_at: 2020-01-08 18:42:29 UTC  
> Updated_at: 2020-01-08 19:32:16 UTC  
> Url: https://github.com/boostorg/gil/pull/414#issuecomment-572201988  

@simmplecoder Apart from the minor comments above, there is an important issue to investigate that appeared from the old discussions I referenced in https://github.com/boostorg/gil/pull/414#discussion_r364372414  
  
At the bottom of https://lists.boost.org/Archives/boost//2010/03/163788.php response from @chhenning to Phil Endecott's suggestions, there is this note:  
  
```  
>> My recollection is that one of the other libraries also had a setjmp error  
>> handler, but that it could be configured to instead return error codes while  
>> libjpeg can't.  Not sure if that's accurate though.  
>  
> Do you mean libpng? I believe I have to update my code for png, as well.   
```  
  
Since that question of @chhenning has never been followed up with any response, I think, we can assume no conclusion was made or action taken. I think this PR is a good opportunity to review this dangerous potential.  
  
Now, the libpng has this function `png_set_error_fn` and we should supply a replacement error function and use `png_set_error_fn` to replace the default error function at run-time.  
  
/cc @stefanseefeld, @simmplecoder  Thoughts?  
  
### Example  
  
```cpp  
template< typename View >  
void apply( const View& view )  
{  
    // TODO: Add handler functions e.g. handle_error, handle_warning  
    png_set_error_fn(this->get_struct(), png_jmpbuf(this->get_struct()), handle_error, handle_warning);  
    if (setjmp(png_jmpbuf(this->get_struct())))  
    {  
        // TODO: Any resources to clean up?  
        io_error("png is invalid");  
    }  
...  
}  
```  
  
Next, as per https://sourceforge.net/p/libpng/code/ci/301f7a14295a3bdfaf406dbb5004d0784dc137ea/tree/pngerror.c#l828  
  
```c  
/* This function is called when the application wants to use another method  
 * of handling errors and warnings.  Note that the error function MUST NOT  
 * return to the calling routine or serious problems will occur.  The return  
 * method used in the default routine calls longjmp(png_ptr->jmpbuf, 1)  
 */  
void PNGAPI  
png_set_error_fn(png_structp png_ptr, png_voidp error_ptr,  
   png_error_ptr error_fn, png_error_ptr warning_fn)  
```  
  
custom handler would have to respect that like so:  
  
```cpp  
static void handle_error(png_structp png_ptr, char const* error_message)  
{  
    // no need to dump error_message to stderr as png_default_error does  
    if (auto * p = reinterpret_cast<jmp_buf*>(png_get_error_ptr(png_ptr)))  
        longjmp(p, 1);  
}  
```  
  
which in turn would `setjmp` return into `io_error` throwing an exception.  
  
------  
  
The custom handlers could be static members like  
https://github.com/boostorg/gil/blob/40e158b26bcd15dde04f89df4189a1af87b11b16/include/boost/gil/extension/io/png/detail/reader_backend.hpp#L628  
https://github.com/boostorg/gil/blob/40e158b26bcd15dde04f89df4189a1af87b11b16/include/boost/gil/extension/io/png/detail/reader_backend.hpp#L643

---

## Comment 4

> Username: simmplecoder  
> Created_at: 2020-01-09 16:57:01 UTC  
> Url: https://github.com/boostorg/gil/pull/414#issuecomment-572654478  

@mloskot, that's what I mentioned by the error handler. I believe it provides little benefit and deviates from already set standard that is used in libjpeg. I believe consistency in this case outweighs any gain in performance, clarity, nor future potential. One benefit can be setting a custom handler, but then that will be a good footshooter since the function cannot return to the caller. So the way to escape is either goto, longjmp, or exception (perhaps some other unstructured control flow). Perhaps returning the error code as part of the exception might be nice, but otherwise I don't see much point in improving customization.  
  
By the way, if we decide to go the customization route, I believe it is better to set the function when initializing the read struct, in [`png_create_read_struct`](https://refspecs.linuxbase.org/LSB_3.1.0/LSB-Desktop-generic/LSB-Desktop-generic/libpng12.png.create.read.struct.1.html).

---

## Comment 5

> Username: mloskot  
> Created_at: 2020-01-09 17:35:03 UTC  
> Url: https://github.com/boostorg/gil/pull/414#issuecomment-572669754  

@simmplecoder Okey, I take your points. I might have been asking for more explicit statements and it may feel we've made a circle, but I think it's beneficial for better understanding of the issue and selected solution, and archive it here in case bus factor hits us in future.  
  
Summary, let's keep it simple and let's stick to currently established pattern as in the JPEG extension and, I think, we still need to take into account the latest insights from @misos1 in https://github.com/boostorg/gil/issues/401#issuecomment-572653639).

---

## Comment 6

> Username: simmplecoder  
> Created_at: 2020-01-11 15:26:51 UTC  
> Url: https://github.com/boostorg/gil/pull/414#issuecomment-573326217  

@mloskot, I investigated the possibility of having a guard function/object like `std::lock_guard`, but unfortunately that will not work, as `std::longjmp` will not jump directly upwards the call stack. Let me illustrate this with an example:  
  
```  
#include <csetjmp>  
  
template <typename Callable>  
void create_checkpoint(std::jmp_buf buf, Callable&& callable)  
{  
    if (setjmp(buf) != 0)  
    {  
        callable();  
    }  
}  
  
#include <iostream>  
  
struct announcer {  
    int id;  
    announcer(int id):  
        id{id}  
    {  
        std::cout << "created announcer with id " << id << '\n';  
    }  
    ~announcer() {  
        std::cout << "destructing announcer with id " << id << '\n';   
    }  
};  
  
void oopsie(std::jmp_buf buf, bool shouldJump)  
{  
    if (shouldJump)  
    {  
        //std::cout << "performing jump...\n";  
        std::longjmp(buf, 1);  
    }  
}  
  
void test1()   
{  
    std::jmp_buf buf;  
    announcer a1{1};  
    create_checkpoint(buf, []() {throw std::exception();});  
    oopsie(buf, true);  
}  
  
void test2()  
{  
    std::jmp_buf buf;  
    announcer a1{1};  
    create_checkpoint(buf, []() {throw std::exception();});  
    oopsie(buf, false);  
      
      
    announcer a2{2};  
    create_checkpoint(buf, []() {throw std::exception();});  
    oopsie(buf, true);  
}  
  
int main()  
{  
    try   
    {  
        test1();  
    }  
    catch (...)  
    {}  
      
    try   
    {  
        test2();  
    }  
    catch (...)  
    {}  
}  
```  
  
So the call stack is something like this:  
```  
entry-point  
|  
                   resource allocation (first goes right)  
                /                      \       <-- returns and goes left  
libpng function calls        guard function call   
      |  
    longjmp  
```  
  
As you can see the jump will not be directly upwards.  
  
[Macro does solve the problem](https://wandbox.org/permlink/vNFz3A6sND5Nvo9W), but I guess it will be just exchanging one problem for another. So the only way is to manually write the guarding code.

---

## Comment 7

> Username: chhenning  
> Created_at: 2020-01-12 20:12:26 UTC  
> Url: https://github.com/boostorg/gil/pull/414#issuecomment-573452334  

@mloskot, have you ever taken a look how other libraries solve the problem? For instance, `imagemagick` might have some good ideas. [link](https://github.com/ImageMagick/ImageMagick/blob/master/coders/png.c)   
  
Just wondering.

---

## Comment 8

> Username: mloskot  
> Created_at: 2020-01-13 12:10:37 UTC  
> Url: https://github.com/boostorg/gil/pull/414#issuecomment-573633723  

@chhenning I looked at what https://github.com/OSGeo/gdal does, it's basically what GIL does. The ImageMagick seems to be more careful about scopes, it's interesting. If anyone is interested to investigate that further and solidify GIL's implementation, I'm sure it would be useful.  
  
I'm busy with other areas and I can just offer some ad-hoc commentary on the issue.

---

## Comment 9

> Username: misos1  
> Created_at: 2020-01-13 21:22:40 UTC  
> Updated_at: 2020-01-13 21:23:39 UTC  
> Url: https://github.com/boostorg/gil/pull/414#issuecomment-573877218  

From https://lists.boost.org/Archives/boost//2010/03/163724.php:  
  
> 4. Try to throw an exception from the error handler. This would be  
> ideal, if you could be sure that the exception would propagate through  
> the C code. I believe that gcc has an option to control that. I have  
> a feeling that it is enabled for some libraries in Debian precisely to  
> support this sort of thing. I know nothing about other compilers, and  
> even on Linux it's probably not reasonable to expect that the user will  
> use a libjpeg compiled in this way.  
  
I am just curious. Is it really such problem to throw exception from error handler? I would like to know more about this but I was not able to find more details except one or two mentions like this.

---

## Comment 10

> Username: mloskot  
> Created_at: 2020-01-13 21:35:44 UTC  
> Url: https://github.com/boostorg/gil/pull/414#issuecomment-573882206  

@misos1   
>  Is it really such problem to throw exception from error handler?  
  
I can't tell from top of my head, without investigating. May be worth asking on the list.  
  
@simmplecoder & @misos1 I think this PR can be merged as is. Then, we can follow with more investigation and further improve the PNG and JPEG implementation. Would you agree?

---

## Comment 11

> Username: misos1  
> Created_at: 2020-01-13 21:39:37 UTC  
> Updated_at: 2020-01-13 21:39:49 UTC  
> Url: https://github.com/boostorg/gil/pull/414#issuecomment-573883753  

Yes as it fixes some problems it is better than nothing. Leaks and other things can be solved later.

---

## Comment 12

> Username: mloskot  
> Created_at: 2020-01-13 21:50:45 UTC  
> Updated_at: 2020-01-13 21:51:49 UTC  
> Url: https://github.com/boostorg/gil/pull/414#issuecomment-573888473  

@misos1 Thanks!

---

## Review 13 [Approved]

> Username: mloskot  
> Created_at: 2020-01-13 21:51:40 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/gil/pull/414#pullrequestreview-342164059  

@simmplecoder Please, merge whenever you are ready

---
