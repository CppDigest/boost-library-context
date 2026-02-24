# #649 fix: Memory leak in image class for empty dimensions [Merged]

> Username: marco-langer  
> Created at: 2022-04-24 17:21:34 UTC  
> Updated at: 2022-06-27 21:30:55 UTC  
> Merged at: 2022-06-27 20:59:52 UTC  
> Closed at: 2022-06-27 20:59:52 UTC  
> Url: https://github.com/boostorg/gil/pull/649  

### Description  
  
Fixes #561  
  
Invoking `image::allocate_a` with an empty dimension (i.e. zero bytes to allocate) causes a memory leak on implementations which return a non-nullptr for zero allocated bytes. In this case `image::deallocate` will not invoke the deallocation method on the allocator.  
  
Examples, for which `image::allocate_a` tries to allocate zero bytes:  
```C++  
boost::gil::rgb8_pixel_t pixel(42);  
boost::gil::rgb8_image_t image(0, 0, pixel);  
```  
```C++  
boost::gil::rgb8_image_t image1;  
boost::gil::rgb8_image_t image2(image1);  
```  
  
In the above mentioned issue the leak happens during the call of `read_image` overloaded for any_image: An image is copy-constructed from a default-constructed image. The other overloads of `read_image` do not leak memory.  
  
The issue is not related to the jpeg io extension, it occurs as well with the png extension in combination with any_image.   
  
### References  
  
<!-- Any links related to this PR: issues, other PRs, mailing list threads, StackOverflow questions, etc. -->  
  
- #702  
  
### Tasklist  
  
<!-- Add YOUR OWN TASK(s), especially if your PR is a work in progress -->  
  
- [x] Add test case(s)  
- [x] Ensure all CI builds pass  
- [x] Review and approve

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2022-04-25 10:28:09 UTC  
> Updated_at: 2022-06-26 13:14:12 UTC  
> Url: https://github.com/boostorg/gil/pull/649#issuecomment-1108387059  

# [Codecov](https://codecov.io/gh/boostorg/gil/pull/649?src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
> Merging [#649](https://codecov.io/gh/boostorg/gil/pull/649?src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (323a621) into [develop](https://codecov.io/gh/boostorg/gil/commit/adddbec8961a152db5751802307cc4ee977fd15d?el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (adddbec) will **decrease** coverage by `0.02%`.  
> The diff coverage is `50.00%`.  
  
```diff  
@@             Coverage Diff             @@  
##           develop     #649      +/-   ##  
===========================================  
- Coverage    80.69%   80.67%   -0.03%       
===========================================  
  Files          116      116                
  Lines         5072     5076       +4       
===========================================  
+ Hits          4093     4095       +2       
- Misses         979      981       +2       
```

---

## Comment 2

> Username: striezel  
> Created_at: 2022-05-04 20:11:46 UTC  
> Url: https://github.com/boostorg/gil/pull/649#issuecomment-1117848226  

These might be silly questions, but I will ask anyway:  
  
* How can it be verified that the leak is fixed?  
* And once that is clear: Shouldn't there be a regression test to make sure the leak does not get reintroduced in the future?

---

## Comment 3

> Username: marco-langer  
> Created_at: 2022-05-05 04:46:17 UTC  
> Updated_at: 2022-05-28 08:40:01 UTC  
> Url: https://github.com/boostorg/gil/pull/649#issuecomment-1118163989  

To observe the memory leak in the current develop branch, run either this minimal example, or the example in the linked issue, with enabled address sanitizer:  
  
```C++  
#include <boost/gil.hpp>  
  
namespace gil = boost::gil;  
  
int main()  
{  
	gil::rgb32_image_t image(0, 0);  
	return 0;  
}  
```  
  
In Linux, just add `-fsanitize=address` to the compile flags. In Windows, [MSVC supports Asan since Visual Studio 2019](https://devblogs.microsoft.com/cppblog/addresssanitizer-asan-for-windows-with-msvc/).  
  
Running the same examples against my branch from this PR do not show the leak anymore.  
  
The second question about regression testing is a valid concern. I am not very familiar with the boost unit testing framework, but my naive suggestion is to add a dedicated Asan-enabled job to our CI matrix and run the complete test suite with Asan enabled. It would be a good idea to check how / if other boost libraries also include Asan in their CI, does anyone know?

---

## Comment 4

> Username: sdebionne  
> Created_at: 2022-05-18 07:15:11 UTC  
> Updated_at: 2022-05-18 07:16:08 UTC  
> Url: https://github.com/boostorg/gil/pull/649#issuecomment-1129655987  

Thanks for reporting this corner case. The memory leak [can we reproduced](https://godbolt.org/z/7GhM3nnPx) with  
  
```cpp  
#include <memory>  
  
int main() {  
  std::allocator<char> alloc;  
  alloc.allocate(0);  
}  
```  
  
Zero size is a valid size and `new`/ `std::allocator` are required to return a non nullptr (that needs to be deleted). A better fix IMO is:  
  
```diff  
diff --git a/include/boost/gil/image.hpp b/include/boost/gil/image.hpp  
index 23763d412..d4ffb7725 100644  
--- a/include/boost/gil/image.hpp  
+++ b/include/boost/gil/image.hpp  
@@ -392,7 +392,7 @@ private:  
  
     void deallocate()  
     {  
-        if (_memory && _allocated_bytes > 0)  
+        if (_memory)  
             _alloc.deallocate(_memory, _allocated_bytes);  
     }  
```

---

## Review 5 [Changes requested]

> Username: mloskot  
> Created_at: 2022-05-18 07:46:44 UTC  
> State: CHANGES_REQUESTED  
> Url: https://github.com/boostorg/gil/pull/649#pullrequestreview-976500691  

@marco-langer Would it be possible to change this to @sdebionne 's suggestion?

---

## Comment 6

> Username: marco-langer  
> Created_at: 2022-05-18 15:19:15 UTC  
> Updated_at: 2022-05-18 15:59:16 UTC  
> Url: https://github.com/boostorg/gil/pull/649#issuecomment-1130154765  

You are right, there are two possible solutions to avoid this bug: either not allocating at all zero bytes (as done in my PR), or allocating zero bytes and freeing this data pointer in the destructor.  
  
In this PR I decided to bail out early in the constructor in order to not allocate zero bytes. The non-nullptr returned by the allocator shall not be dereferenced according to the standard. Quoting the behaviour of malloc, which might - or might not (implementation defined) -  be used internally by `std::allocator`:  
  
>   
> The C standard (C17 7.22.3/1) says:  
>   
> If the size of the space requested is zero, the behavior is implementation defined: either a null pointer is returned, or the behavior is as if the size were some nonzero value, except that the returned pointer shall not be used to access an object.  
>   
  
What is the point about carrying such a "toxic" pointer from the point of construction until the point of destruction and risking the chance of invoking undefined behaviour while the image instance exists? Wouldn't it be better to keep the data pointer as nullptr during object lifetime?  
  
But of cause I can change my PR accordingly if you still think it is a good idea.  
  
Edit: [Here on stack overflow](https://stackoverflow.com/a/1087066) is the behaviour of `operator new` explained. Deferencing the pointer returned from `new` is undefined behaviour in C++ as well as it is in C.

---

## Comment 7

> Username: mloskot  
> Created_at: 2022-05-18 21:42:01 UTC  
> Url: https://github.com/boostorg/gil/pull/649#issuecomment-1130578198  

@marco-langer   
> What is the point about carrying such a "toxic" pointer from the point of construction until the point of destruction and risking the chance of invoking undefined behaviour while the image instance exists?  
  
A very good question. I've taken this from Scott Meyers' "Effective C++"  
  
  
  
> _"C++ requires that operator new return a legitimate pointer even when zero bytes are requested. (Requiring this odd-sounding behavior **simplifies** things elsewhere in the language.)"_  
  
Ensuring a non-null pointer invariant makes things simpler w.r.t. expected state of object.  
But, it can bite with the undefined behaviour indeed.  
  
Could you update this PR with the latest `develop` (either merge or rebase & force-push is fine), so we can see if the CI jobs succeed eventually?

---

## Comment 8

> Username: marco-langer  
> Created_at: 2022-05-19 06:09:18 UTC  
> Updated_at: 2022-05-19 07:43:26 UTC  
> Url: https://github.com/boostorg/gil/pull/649#issuecomment-1131255809  

I usually learn a lot by reading Scott Meyers, but I think this specific quote is not worded correctly: The pointer returned from allocating zero bytes is not _legitimate_ as it shall not be dereferenced according to the standard (please correct me if I am wrong).  
  
Thereby the invariant is broken anyway, because the invariant of a non-null pointer is that one can safely dereference it.  
The consequence of both solutions, either carrying a null pointer or this non-null-but-not-dereferencable pointer during image lifetime, is that the data pointer shall not be derefenced.  
  
I would argue that the risk of accidentally dereferencing a null pointer is less than the risk of derefencing a non-null-but-not-dereferencable pointer. The former can easily be checked via `ìf (ptr)`, whereas the latter requires the additional context information that the pointer was obtained from a call to `new T[0]`.

---

## Comment 9

> Username: mloskot  
> Created_at: 2022-05-19 07:30:58 UTC  
> Url: https://github.com/boostorg/gil/pull/649#issuecomment-1131331728  

@marco-langer   
> I would argue that the risk of accidentally dereferencing a null pointer is less than the risk of derefencing a non-null-but-not-dereferencable pointer.   
  
I agree.  
  
If @sdebionne does not object with any insights that we have been missing, then I think we should take your PR as is.

---

## Comment 10

> Username: sdebionne  
> Created_at: 2022-05-19 07:33:18 UTC  
> Url: https://github.com/boostorg/gil/pull/649#issuecomment-1131334420  

The codecov failure is interesting: adding a new code path (early `return`  statement) but no test case results in pipeline failure. The other option does not have this drawback.

---

## Comment 11

> Username: mloskot  
> Created_at: 2022-05-19 07:40:31 UTC  
> Url: https://github.com/boostorg/gil/pull/649#issuecomment-1131349574  

> The codecov failure is interesting: adding a new code path (early return statement) but no test case results in pipeline failure.  
  
Yes, I've been just wondering what's happening there...

---

## Review 12 [Approved]

> Username: mloskot  
> Created_at: 2022-06-27 20:58:52 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/gil/pull/649#pullrequestreview-1020774388  

Since time for Boost 1.80 is pressing on, I'm merging this one as is.  
  
If we keep observing this fix triggers codecov (and alike) annoyances, then we will resort to @sdebionne 's way of fixing it and we will agree on the discused trade-offs of non-dereferencable non-nullptr.  
  
Thanks @marco-langer !

---
