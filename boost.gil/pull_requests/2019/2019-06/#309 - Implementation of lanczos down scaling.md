# #309 Implementation of lanczos down scaling [Merged]

> Username: simmplecoder  
> Created at: 2019-06-07 14:21:16 UTC  
> Updated at: 2020-08-01 20:08:56 UTC  
> Merged at: 2019-06-23 10:14:24 UTC  
> Closed at: 2019-06-23 10:14:24 UTC  
> Url: https://github.com/boostorg/gil/pull/309  

### Description  
  
This is milestone merge after implementing lanczos scaling  
  
### Environment  
  
Everything that supports Boost 1.70  
  
### References  
  
https://en.wikipedia.org/wiki/Lanczos_resampling  
  
### Tasklist  
  
- [X] Lanczos scaling  
- [X] Add test case(s)  
- [X] Ensure all CI builds pass  
- [x] Review and approve

---

## Review 1 [Commented]

> Username: mloskot  
> Created_at: 2019-06-07 14:51:58 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/gil/pull/309#pullrequestreview-247141561  

📁 .ci/get-boost.sh

```diff
  43 |     libs/integer \
  44 |     libs/iterator \
  45 |+     libs/math \
```

> Username: mloskot  
> Created_at: 2019-06-07 14:51:58 UTC  
> Updated_at: 2019-06-22 21:08:36 UTC  
> Url: https://github.com/boostorg/gil/pull/309#discussion_r291627598  

This seems like remains from previous round of reviews in https://github.com/BoostGSoC19/gil-olzhas/pull/1

> Username: simmplecoder  
> Created_at: 2019-06-07 14:54:43 UTC  
> Updated_at: 2019-06-22 21:08:36 UTC  
> Url: https://github.com/boostorg/gil/pull/309#discussion_r291628828  

Indeed, I guess I did reset incorrectly, should've double checked ... Was it previously at transitive requirements? EDIT: it seems so. Should be fixed now.

> Username: mloskot  
> Created_at: 2019-06-07 15:46:04 UTC  
> Updated_at: 2019-06-22 21:08:36 UTC  
> Url: https://github.com/boostorg/gil/pull/309#discussion_r291650521  

Yes, it is transitive  
  
https://github.com/boostorg/gil/blob/0fe841f2bf3f8ba25891bc0b3d1fac25ac5cd050/.ci/get-boost.sh#L52-L69


---

## Review 2 [Changes requested]

> Username: mloskot  
> Created_at: 2019-06-07 15:50:57 UTC  
> State: CHANGES_REQUESTED  
> Url: https://github.com/boostorg/gil/pull/309#pullrequestreview-247174264  

📁 test/core/image_processing/lanczos_scaling.cpp

```diff
   4 |+ #include <boost/gil/extension/io/jpeg.hpp>
   5 |+ #include <boost/gil/io/read_image.hpp>
   6 |+ #include <boost/gil/io/write_view.hpp>
```

> Username: mloskot  
> Created_at: 2019-06-07 15:50:51 UTC  
> Updated_at: 2019-06-22 21:08:36 UTC  
> Url: https://github.com/boostorg/gil/pull/309#discussion_r291652471  

All IO headers need to be removed.  
They are not used anyway.  
This should fix the CI builds too.  
  
IMHO, all core tests should be free from dependency on the IO extension. There are many ways to have small samples of images w/o depending on the IOs (generate, draw, [define in source](https://github.com/boostorg/gil/blob/develop/test/legacy/sample_image.cpp).  
The core tests are supposed to be isolated and to run as fast as possible.

> Username: simmplecoder  
> Created_at: 2019-06-07 16:03:52 UTC  
> Updated_at: 2019-06-22 21:08:36 UTC  
> Url: https://github.com/boostorg/gil/pull/309#discussion_r291657351  

Thanks for catching that!


---

## Review 3 [Commented]

> Username: mloskot  
> Created_at: 2019-06-07 16:08:03 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/gil/pull/309#pullrequestreview-247182623  

📁 include/boost/gil/image_processing/scaling.hpp

```diff
  53 |+                                  [](channel_t) {
  54 |+                                      return static_cast<channel_t>(0);
  55 |+                                     });
```

> Username: mloskot  
> Created_at: 2019-06-07 16:08:02 UTC  
> Updated_at: 2019-06-22 21:08:36 UTC  
> Url: https://github.com/boostorg/gil/pull/309#discussion_r291658808  

style nitpick: this could just read  
  
```  
    boost::gil::static_transform(result_pixel, result_pixel,   
        [](channel_t) { return static_cast<channel_t>(0); });  
```  
without the blank field to the left that makes left-to-right reader's eyes travel before they see anything relevant ;)


---

## Review 4 [Commented]

> Username: mloskot  
> Created_at: 2019-06-07 16:09:58 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/gil/pull/309#pullrequestreview-247183490  

📁 include/boost/gil/image_processing/scaling.hpp

```diff
  56 |+     
  57 |+     for (long int y_i = std::max(source_y - a + 1, 0l); 
  58 |+          y_i <= std::min(source_y + a, input_view.height() - 1);
```

> Username: mloskot  
> Created_at: 2019-06-07 16:09:58 UTC  
> Updated_at: 2019-06-22 21:08:36 UTC  
> Url: https://github.com/boostorg/gil/pull/309#discussion_r291659485  

@simmplecoder I think you need some casts here.  
  
@stefanseefeld Here comes my rant 😄  If `ptrdiff_t` was used consistently across the GIL code base and this code, this line would have not failed to compile due to `scaling.hpp(58): error C2672: 'std::min': no matching overloaded function found`

> Username: simmplecoder  
> Created_at: 2019-06-07 16:15:53 UTC  
> Updated_at: 2019-06-22 21:08:36 UTC  
> Url: https://github.com/boostorg/gil/pull/309#discussion_r291661537  

Is there a piece of long int arithmetics that I don't understand? I believe I'm not mixing types, except that + 1 and -1.

> Username: mloskot  
> Created_at: 2019-06-07 16:34:42 UTC  
> Updated_at: 2019-06-22 21:08:36 UTC  
> Url: https://github.com/boostorg/gil/pull/309#discussion_r291667673  

This is more about operand types mismatch while there is single `T` expected. For visual example:  
  
```  
#include <algorithm>  
#include <cstddef>  
#include <type_traits>  
  
template <typename T>  
void foo(T const& a, T const& b) {}  
  
int main()  
{  
    long int a;  
    std::ptrdiff_t b;  
  
    // Checks based on MSVC 15, 16. For GCC and clang, types may be different.  
    static_assert(!std::is_same<long int, std::ptrdiff_t>::value);  
    static_assert(std::is_same<decltype(b + 1), std::ptrdiff_t>::value);  
    static_assert(std::is_same<decltype(a + a), long int>::value);  
  
    std::min(a, b);    // T=long != T=int or T=long != T=long long // depends on WIN64 or not  
    foo(a + a, b - 1); // equivalent of std::min/max case  
}  
```

> Username: simmplecoder  
> Created_at: 2019-06-11 11:38:48 UTC  
> Updated_at: 2019-06-22 21:08:36 UTC  
> Url: https://github.com/boostorg/gil/pull/309#discussion_r292410161  

Could you please tell cast to what exactly? I'm still not getting what is going wrong. I read integer promotion page on cppref completely but it doesn't say anything about this. I looked at the error messages, but I am not sure if it is replacing `std::ptrdiff_t` with direct type.

> Username: mloskot  
> Created_at: 2019-06-11 12:00:01 UTC  
> Updated_at: 2019-06-22 21:08:36 UTC  
> Url: https://github.com/boostorg/gil/pull/309#discussion_r292417437  

- the operands are of different types, while `std::min` requires both are of the same type `T`  
- you need to cast either  
- prefer to cast to the one larger capacity i.e.  `b` is a type of size `>=` of type of `a`, so you need  
  
```  
std::min(static_cast<std::ptrdiff_t>(a), b);  
foo(static_cast<std::ptrdiff_t>(a + a), b - 1);  
```  
  
In other words, since `height()` and `width()` return `ptrdiff_t`, I insist to ensure that the other operands are unified to `ptrdiff_t`.   
  
I also pointed that out during discussion with @stefanseefeld, consistent use of types does matter and helps to avoid sitautions like above. Even if the `ptrdiff_t` in particular is not entirely correct choice, it is a good choice given the current contenxt of the current GIL interface (i.e. `height()` and `width()` , etc.)

> Username: simmplecoder  
> Created_at: 2019-06-11 12:11:36 UTC  
> Updated_at: 2019-06-22 21:08:36 UTC  
> Url: https://github.com/boostorg/gil/pull/309#discussion_r292421362  

Sorry for bothering you even more, but could you please explain where did `std::ptrdiff_t` come from? Last time I checked the return type of `width()` and `height()`  was `long int`, am I wrong?

> Username: simmplecoder  
> Created_at: 2019-06-11 12:27:55 UTC  
> Updated_at: 2019-06-22 21:08:36 UTC  
> Url: https://github.com/boostorg/gil/pull/309#discussion_r292427338  

I see, I missed the PR that uses `std::ptrdiff_t`, sorry.

> Username: mloskot  
> Created_at: 2019-06-11 12:35:43 UTC  
> Updated_at: 2019-06-22 21:08:36 UTC  
> Url: https://github.com/boostorg/gil/pull/309#discussion_r292430471  

Wherever you usee `x_coord_t` or `y_coord_t`, those are most likely `ptrdiff_t` as they are derived from iterator's `difference_type` which in both, standard library and Boost.Iterator, default to `ptrdiff_t`. So, these are `ptrdiff_t`:  
  
```  
    x_coord_t        width()                 const { return dimensions().x; }  
    y_coord_t        height()                const { return dimensions().y; }  
```  
  
Then, GIL sets default `point_t` as `point<ptrdiff_t>`, so  this is `ptrdiff_t` as well:  
  
```  
    const point_t&   dimensions()            const { return _dimensions; }  
```  
  
> Sorry for bothering you even more  
  
No need. You ask valid questions.

> Username: simmplecoder  
> Created_at: 2019-06-11 12:54:00 UTC  
> Updated_at: 2019-06-22 21:08:36 UTC  
> Url: https://github.com/boostorg/gil/pull/309#discussion_r292438043  

Hm, it seems like that was not enough. Should I try to cast both sides? Here is new version:  
  
    std::min(static_cast<std::ptrdiff_t>(source_x + a), input_view.width() - 1)  
  
I believe I casted the right argument

> Username: mloskot  
> Created_at: 2019-06-11 20:24:22 UTC  
> Updated_at: 2019-06-22 21:08:36 UTC  
> Url: https://github.com/boostorg/gil/pull/309#discussion_r292643647  

@simmplecoder I've taken the liberty to `ptrdiff_t`-improve your code. Here is my `git diff` output for your branch with my modifications:  
  
```  
diff --git a/include/boost/gil/image_processing/scaling.hpp b/include/boost/gil/image_processing/scaling.hpp  
index 562486f27..24b91b2a0 100644  
--- a/include/boost/gil/image_processing/scaling.hpp  
+++ b/include/boost/gil/image_processing/scaling.hpp  
@@ -29,13 +29,13 @@ namespace boost{ namespace gil{  
 /// artifacts.  
 template <typename ImageView>  
 void lanczos_at(  
-    long int source_x,   
-    long int source_y,   
-    long int target_x,   
-    long int target_y,   
+    std::ptrdiff_t source_x,   
+    std::ptrdiff_t source_y,   
+    std::ptrdiff_t target_x,   
+    std::ptrdiff_t target_y,   
     ImageView input_view,   
     ImageView output_view,   
-    long int a)   
+    std::ptrdiff_t a)   
 {  
     using pixel_t = typename std::remove_reference<  
                       decltype(std::declval<ImageView>()(0, 0))  
@@ -51,13 +51,13 @@ void lanczos_at(  
     pixel_t result_pixel;  
     boost::gil::static_transform(result_pixel, result_pixel,   
         [](channel_t) { return static_cast<channel_t>(0); });  
-      
-    for (long int y_i = std::max(source_y - a + 1l, 0l);   
-         y_i <= std::min(static_cast<std::ptrdiff_t>(source_y + a), input_view.height() - 1l);  
+  
+    for (std::ptrdiff_t y_i = std::max(source_y - a + 1, std::ptrdiff_t{0});   
+         y_i <= std::min(source_y + a, input_view.height() - 1);  
          ++y_i)   
         {  
-        for (long int x_i = std::max(source_x - a + 1l, 0l);   
-             x_i <= std::min(static_cast<std::ptrdiff_t>(source_x + a), input_view.width() - 1l);   
+        for (std::ptrdiff_t x_i = std::max(source_x - a + 1, std::ptrdiff_t{0});   
+             x_i <= std::min(source_x + a, input_view.width() - 1);   
              ++x_i)   
         {  
             double lanczos_response = boost::gil::lanczos(source_x - x_i, a)   
@@ -88,16 +88,16 @@ void lanczos_at(  
 /// https://en.wikipedia.org/wiki/Lanczos_resampling  
 /// with standardinzed cardinal sin (sinc)  
 template <typename ImageView>  
-void scale_lanczos(ImageView input_view, ImageView output_view, long int a)   
+void scale_lanczos(ImageView input_view, ImageView output_view, std::ptrdiff_t a)   
 {  
     double scale_x = (static_cast<double>(output_view.width()))   
                      / static_cast<double>(input_view.width());  
     double scale_y = (static_cast<double>(output_view.height()))   
                      / static_cast<double>(input_view.height());  
   
-    for (long int y = 0; y < output_view.height(); ++y)   
+    for (std::ptrdiff_t y = 0; y < output_view.height(); ++y)   
     {  
-        for (long int x = 0; x < output_view.width(); ++x)   
+        for (std::ptrdiff_t x = 0; x < output_view.width(); ++x)   
         {  
             boost::gil::lanczos_at(  
                 x / scale_x,   
```  
  
The integer suffixes also introduced some inconsistencies depending on target architecture.  
  
It compiles for me using VS2019 for 32/64 bit.

> Username: mloskot  
> Created_at: 2019-06-11 20:42:40 UTC  
> Updated_at: 2019-06-22 21:08:36 UTC  
> Url: https://github.com/boostorg/gil/pull/309#discussion_r292650977  

One more comment, the patch above also illustrates the signature could read  
  
- `void lanczos_at(x_coord_t src_x, y_coord_t src_y, x_coord_t dst_x, y_coord_t dst_y, ...)`  
- `void lanczos_at(point_t src, point_t dst,...)`  
  
which all will resolve to `ptrdiff_t`.   
  
That is a not uncommon pattern used in GIL code. That is also why maintaining the consistency of the type representing image or view dimensions, row and pixel indexes, etc. does pay off.


---

## Review 5 [Commented]

> Username: mloskot  
> Created_at: 2019-06-11 20:27:48 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/gil/pull/309#pullrequestreview-248419191  

📁 include/boost/gil/image_processing/scaling.hpp

```diff
  30 |+ template <typename ImageView>
  31 |+ void lanczos_at(
  32 |+     long int source_x,
```

> Username: mloskot  
> Created_at: 2019-06-11 20:27:48 UTC  
> Updated_at: 2019-06-22 21:08:36 UTC  
> Url: https://github.com/boostorg/gil/pull/309#discussion_r292645071  

@simmplecoder I noticed lots of trailing whitespaces.  
  
Would it be possible for you to keep them trimmed?  
I recommend to enable the `.editorconfig` for your editor, this should take care of it automatically.  
See near end of bullet list here https://github.com/boostorg/gil/blob/develop/CONTRIBUTING.md#guidelines


---

## Review 6 [Approved]

> Username: stefanseefeld  
> Created_at: 2019-06-20 15:39:31 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/gil/pull/309#pullrequestreview-252372094  

Olzhas, great to see your first PR here. I'll let you do the merge yourself. (please squash to reduce noise.)

---

## Comment 7

> Username: mloskot  
> Created_at: 2019-06-20 19:15:22 UTC  
> Url: https://github.com/boostorg/gil/pull/309#issuecomment-504148559  

@stefanseefeld I don't think @simmplecoder has commit access to perform the merge.

---

## Comment 8

> Username: mloskot  
> Created_at: 2019-06-20 19:42:31 UTC  
> Updated_at: 2019-06-20 19:43:04 UTC  
> Url: https://github.com/boostorg/gil/pull/309#issuecomment-504156730  

@simmplecoder You now have commit access to *this* repository!  
  
> with great power comes great responsibility  :)

---

## Review 9 [Commented]

> Username: mloskot  
> Created_at: 2019-06-22 21:44:35 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/gil/pull/309#pullrequestreview-253108862  

📁 test/core/image_processing/CMakeLists.txt

```diff
  30 |-   set(_test t_image_processing_${_name})
  31 |-   set(_target test_image_processing_${_name})
  31 |+   set(_test t_${_name})
```

> Username: mloskot  
> Created_at: 2019-06-22 21:44:35 UTC  
> Url: https://github.com/boostorg/gil/pull/309#discussion_r296458096  

FYI, I 'invented' and followed the convention of prefixing  
- core tests with `t_core_`   
- extension tests with `t_ext_<extension name>_`  
I personally like this distinction of core vs extension stuff.  
  
However, don't bother about such trivial issues. I/We will run a unifying clean up at one point later. Just keep your test names unique.

> Username: simmplecoder  
> Created_at: 2019-06-22 21:46:32 UTC  
> Updated_at: 2019-06-22 21:46:33 UTC  
> Url: https://github.com/boostorg/gil/pull/309#discussion_r296458128  

Should I resolve this then?

> Username: mloskot  
> Created_at: 2019-06-22 21:55:26 UTC  
> Url: https://github.com/boostorg/gil/pull/309#discussion_r296458321  

Yes, certainly. This I added as single comment, just FYI, not review, no request for changes.


---
