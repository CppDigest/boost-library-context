# #258 Add example of canny edge detection algorithm [Open]

> Username: Antropovi  
> Created at: 2019-03-15 14:26:35 UTC  
> Updated at: 2022-10-27 07:19:14 UTC  
> Url: https://github.com/boostorg/gil/pull/258  

Add example of canny edge detection using gil as my competency test for GSoC 2019  
  
### References  
  
Find more about Canny edge detection on https://en.wikipedia.org/wiki/Canny_edge_detector  
  
### Tasklist  
  
- [ ] Review  
- [ ] Adjust for comments  
- ~~All CI builds and checks have passed~~ ATM, examples are not built by CI jobs

---

## Review 1 [Changes requested]

> Username: mloskot  
> Created_at: 2019-03-15 17:59:56 UTC  
> State: CHANGES_REQUESTED  
> Url: https://github.com/boostorg/gil/pull/258#pullrequestreview-215162562  

Thank you for this contribution.   
  
I've done quick review and made  a few comments. LGTM.

📁 example/canny.cpp

```diff
   6 |+ #include <boost/gil/extension/numeric/convolve.hpp>
   7 |+ 
   8 |+ #include <math.h>
```

> Username: mloskot  
> Created_at: 2019-03-15 17:30:55 UTC  
> Updated_at: 2019-03-23 20:11:14 UTC  
> Url: https://github.com/boostorg/gil/pull/258#discussion_r266080037  

Should read `<cmath>`

---

📁 example/canny.cpp

```diff
  16 |+ void smoothing(const SrcView &src)
  17 |+ {
  18 |+     float gaussian[] = {0.06136f, 0.24477f, 0.38774f, 0.24477f, 0.06136f};
```

> Username: mloskot  
> Created_at: 2019-03-15 17:39:05 UTC  
> Updated_at: 2019-03-23 20:11:14 UTC  
> Url: https://github.com/boostorg/gil/pull/258#discussion_r266083048  

Since this is an example, it would be very useful to explain these, add comment about source and purpose of the constants, i.e. 1-D gaussian kernel, size of kernel, what it is used for.

---

📁 example/canny.cpp

```diff
 138 |+ 
 139 |+ void canny(const rgb8c_view_t &src, const gray8_view_t &dst, int minVal, int maxVal)
 140 |+ {
```

> Username: mloskot  
> Created_at: 2019-03-15 17:44:38 UTC  
> Updated_at: 2019-03-23 20:11:14 UTC  
> Url: https://github.com/boostorg/gil/pull/258#discussion_r266085129  

It would be helpful if there is comment about steps performed by `canny`, i.e. steps of the algorithm.  
Alternatively, rename functions to make clearer indications of what they do  
or add link to reference paper the implementation follows.

---

📁 example/canny.cpp

```diff
  61 |+ }
  62 |+ 
  63 |+ void suppression_threshold(const gray8c_view_t &magnitude, const gray32fc_view_t &angle,
```

> Username: mloskot  
> Created_at: 2019-03-15 17:46:41 UTC  
> Updated_at: 2019-03-23 20:11:14 UTC  
> Url: https://github.com/boostorg/gil/pull/258#discussion_r266085881  

`non_maximal_suppression` wouldn't it be a better name?

> Username: Antropovi  
> Created_at: 2019-03-15 18:10:28 UTC  
> Updated_at: 2019-03-23 20:11:14 UTC  
> Url: https://github.com/boostorg/gil/pull/258#discussion_r266094641  

This function makes suppression and threshold. Full name should be like `non_maximal_suppression_and_thresholding`, but it is too long, isn't it?

> Username: mloskot  
> Created_at: 2019-03-15 18:18:57 UTC  
> Updated_at: 2019-03-23 20:11:14 UTC  
> Url: https://github.com/boostorg/gil/pull/258#discussion_r266097736  

But, isn't the thresholding a technique, a mean, to perform the suppression?  
  
I wouldn't worry about too long names. This is an example, it should be clear, self-descriptive, raise no questions in someone who reads the code and expects to learn from the code.

---

📁 example/canny.cpp

```diff
  14 |+ 
  15 |+ template <typename SrcView>
  16 |+ void smoothing(const SrcView &src)
```

> Username: mloskot  
> Created_at: 2019-03-15 17:47:30 UTC  
> Updated_at: 2019-03-23 20:11:14 UTC  
> Url: https://github.com/boostorg/gil/pull/258#discussion_r266086209  

`gaussian_blur` or `gaussian_smoothing` wouldn't be a clearer name?  
  
It is an example with learning as primary purpose, isn't it.

---

📁 example/canny.cpp

```diff
 118 |+ }
 119 |+ 
 120 |+ void hysteresis(const gray8_view_t &dst)
```

> Username: mloskot  
> Created_at: 2019-03-15 17:52:11 UTC  
> Updated_at: 2019-03-23 20:11:14 UTC  
> Url: https://github.com/boostorg/gil/pull/258#discussion_r266087921  

`hystereris_thresholding`

---

📁 example/canny.cpp

```diff
 124 |+         for (int j = 1; j < dst.width() - 1; ++j, ++dst_loc.x()) {
 125 |+             if (dst_loc(0, 0) == 150) {
 126 |+                 if (dst_loc(-1, -1) == 255 || dst_loc(-1, 0) == 255 ||
```

> Username: mloskot  
> Created_at: 2019-03-15 17:52:36 UTC  
> Updated_at: 2019-03-23 20:11:14 UTC  
> Url: https://github.com/boostorg/gil/pull/258#discussion_r266088078  

Please, add comment explaining where do the `150` and `255` come from.

---

📁 example/canny.cpp

```diff
  23 |+ }
  24 |+ 
  25 |+ void sobel_filtering(const gray8c_view_t &src,
```

> Username: mloskot  
> Created_at: 2019-03-15 17:57:29 UTC  
> Updated_at: 2019-03-23 20:11:14 UTC  
> Url: https://github.com/boostorg/gil/pull/258#discussion_r266089933  

Comment on purpose, i.e. calculates approx. of intensity gradient

---

📁 example/canny.cpp

```diff
 154 |+ }
 155 |+ 
 156 |+ int main()
```

> Username: mloskot  
> Created_at: 2019-03-15 17:59:21 UTC  
> Updated_at: 2019-03-23 20:11:14 UTC  
> Url: https://github.com/boostorg/gil/pull/258#discussion_r266090665  

Could you make it possible to specify input image via command line argument?


---

## Review 2 [Changes requested]

> Username: mloskot  
> Created_at: 2019-03-18 18:34:19 UTC  
> State: CHANGES_REQUESTED  
> Url: https://github.com/boostorg/gil/pull/258#pullrequestreview-215779671  

@Antropovi This looks very good! Just a couple of minor comments.

📁 example/canny.cpp

```diff
  12 |+ 
  13 |+ // TOP_VALUE - value of strong edge pixel
  14 |+ #define TOP_VALUE 255
```

> Username: mloskot  
> Created_at: 2019-03-18 18:32:18 UTC  
> Updated_at: 2019-03-23 20:11:14 UTC  
> Url: https://github.com/boostorg/gil/pull/258#discussion_r266584016  

Please, avoid macros and prefer C++ qualities like `const`, `constexpr`.

---

📁 example/canny.cpp

```diff
  11 |+ using namespace boost::gil;
  12 |+ 
  13 |+ // TOP_VALUE - value of strong edge pixel
```

> Username: mloskot  
> Created_at: 2019-03-18 18:32:33 UTC  
> Updated_at: 2019-03-23 20:11:14 UTC  
> Url: https://github.com/boostorg/gil/pull/258#discussion_r266584123  

No need to repeat constant name


---

## Comment 3

> Username: mloskot  
> Created_at: 2019-03-29 20:38:04 UTC  
> Url: https://github.com/boostorg/gil/pull/258#issuecomment-478141809  

For complete records and references, here is @Antropovi  confirmation this is intended as competency test:  
  
https://lists.boost.org/Archives/boost/2019/03/245630.php  
> As my competencytest, I have implement a simple Canny edge detection algorithm using GIL. https://github.com/boostorg/gil/pull/258

---

## Comment 4

> Username: simmplecoder  
> Created_at: 2019-04-02 13:43:19 UTC  
> Updated_at: 2019-04-02 13:46:54 UTC  
> Url: https://github.com/boostorg/gil/pull/258#issuecomment-479001603  

I believe there is a bug in the last hysteresis step: it will only detect Г shaped edges, and not horizontally flipped L shaped edges. Let me give an example (the values are somewhere in the image padded by zeroes on all sides, the values are intensity of the pixel's luminosity):  
  
Case which current code will detect:  
  
```  
1 0.5 0.5               1 1 1  
0.5 0 0         ====>   1 0 0  
05. 0 0                 1 0 0  
```  
  
Case which current code will not detect  
  
```  
0.5 0.5 0.5            1 1 1  
0.5 0 0.5        ====> 1 0 1  
0.5 0.5 1              1 1 1  
```  
  
Here are the lines of code which are connected to the issues:  
  
```  
    // Weak pixels check  
    // If weak pixel has a strong neighbor, then it is strong one  
    // otherwise it is definitely not-edge pixel  
    auto dst_loc = dst.xy_at(1, 1);  
    for (int i = 1; i < dst.height() - 1; ++i) {  
        for (int j = 1; j < dst.width() - 1; ++j, ++dst_loc.x()) {  
            if (dst_loc(0, 0) == MIDDLE_VALUE) {  
                if (dst_loc(-1, -1) == TOP_VALUE || dst_loc(-1, 0) == TOP_VALUE ||  
                        dst_loc(-1, 1) == TOP_VALUE || dst_loc(1, -1) == TOP_VALUE ||  
                        dst_loc(1, 0) == TOP_VALUE || dst_loc(1, 1) == TOP_VALUE ||  
                        dst_loc(0, -1) == TOP_VALUE || dst_loc(0, 1) == TOP_VALUE)  
                    dst_loc(0, 0) = TOP_VALUE;  
                else  
                    dst_loc(0, 0) = BOTTOM_VALUE;  
            }  
        }  
        dst_loc += point2<std::ptrdiff_t>(-dst.width() + 2, 1);  
    }  
```  
  
Instead, I believe there should be connected components search. The search process basically tries to hop over weak edge or strong edge and reach all of the reachable pixels from start location. If there is at least one strong pixel in the connected component, all should be marked as strong too.  
  
Could you please verify if my theory is correct? I believe a good input image for this test case should be one of the O shaped Unicode characters with upper diagonal part blurred out.

---

## Comment 5

> Username: Antropovi  
> Created_at: 2019-04-03 19:14:30 UTC  
> Updated_at: 2019-04-03 19:17:42 UTC  
> Url: https://github.com/boostorg/gil/pull/258#issuecomment-479622045  

This isn't a bug and there are some reasons, why I chose this implementation.   
First and most important - this is an example. It should be easy to understand. There are a lot of guides and blog posts about this specific implementation.  
Second - this is simple Canny algorithm. There are more precise and complex algorithms to detect edges. This one dont even construct a list of all edges.  
Third - you are right. If you use image and rotated on 180 copy of it, output will be slightly different, but the main point still how to choose best threshold values. And btw hysteresis step is not about detection edges. It's more about connection neighbour edges.

---

## Comment 6

> Username: simmplecoder  
> Created_at: 2019-04-03 20:24:40 UTC  
> Url: https://github.com/boostorg/gil/pull/258#issuecomment-479645084  

Sorry for choosing confusing wording. I meant that weak edges that should've been included in the output will not be included. Thanks for clarification on the decisions.

---

## Comment 7

> Username: mloskot  
> Created_at: 2019-04-09 13:22:15 UTC  
> Updated_at: 2019-04-09 13:22:27 UTC  
> Url: https://github.com/boostorg/gil/pull/258#issuecomment-481248991  

@Antropovi Thank you very much again for your example and explanation. I'd like to incorporate it into GIL's collection in `example/`. Hopefully, I'll be able to do it in near future. So, let's keep this PR open.

---

## Comment 8

> Username: mloskot  
> Created_at: 2019-04-28 01:11:02 UTC  
> Updated_at: 2020-01-31 16:44:20 UTC  
> Url: https://github.com/boostorg/gil/pull/258#issuecomment-487331513  

@stefanseefeld Shall we add those as they are (optionally, with minor stylistic refactoring) to `example/` folder? I think, it's never too many examples showing how to actually use GIL. What do you think?  
  
OTOH, perhaps this could be reworked as a library feature, a callable algorithm, not just an example. Any takers?

---

## Comment 9

> Username: Bockeman  
> Created_at: 2022-10-26 16:44:08 UTC  
> Url: https://github.com/boostorg/gil/pull/258#issuecomment-1292321704  

With boost 1.80.0 and -std=c++20  
  
I get the following compile errors:  
```  
/usr/include/boost/gil/extension/numeric/kernel.hpp:16:1: note: ?#pragma message: This header is deprecated. Use <boost/gil/image_processing/kernel.hpp> instead.?  
   16 | BOOST_HEADER_DEPRECATED("<boost/gil/image_processing/kernel.hpp>")  
      | ^~~~~~~~~~~~~~~~~~~~~~~  
/usr/include/boost/gil/extension/numeric/convolve.hpp:15:1: note: ?#pragma message: This header is deprecated. Use <boost/gil/image_processing/convolve.hpp> instead.?  
   15 | BOOST_HEADER_DEPRECATED("<boost/gil/image_processing/convolve.hpp>")  
      | ^~~~~~~~~~~~~~~~~~~~~~~  
In file included from src/rgw16_edge_align.cpp:32:  
src/rgw16_edge_detect.hpp: In function ?void gaussian_blur(const SrcView&)?:  
src/rgw16_edge_detect.hpp:49:52: error: ?convolve_option_output_ignore? was not declared in this scope  
   49 |   convolve_rows<gray32f_pixel_t>(src, kernel, src, convolve_option_output_ignore);  
      |                                                    ^~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
src/rgw16_edge_detect.hpp: In function ?void sobel_filtering(const boost::gil::gray8c_view_t&, const boost::gil::gray8_view_t&, const boost::gil::gray32f_view_t&)?:  
src/rgw16_edge_detect.hpp:76:75: error: ?convolve_option_output_zero? was not declared in this scope  
   76 |   convolve_rows<gray32f_pixel_t>(src, first_sobel_kernel, view(vertical), convolve_option_output_zero);  
      |                                                                           ^~~~~~~~~~~~~~~~~~~~~~~~~~~  
src/rgw16_edge_detect.hpp: In function ?int main_detect(int, char**)?:  
src/rgw16_edge_detect.hpp:229:35: error: ISO C++ forbids converting a string constant to ?char*? [-Werror=write-strings]  
  229 |   char* input                   = "test.jpg";  
      |                                   ^~~~~~~~~~  
src/rgw16_edge_detect.hpp:230:35: error: ISO C++ forbids converting a string constant to ?char*? [-Werror=write-strings]  
  230 |   char* output                  = "canny.jpg";  
      |                                   ^~~~~~~~~~~  
src/rgw16_edge_detect.hpp: In function ?void non_maximal_suppression(const boost::gil::gray8c_view_t&, const boost::gil::gray32fc_view_t&, const boost::gil::gray8_view_t&)?:  
src/rgw16_edge_detect.hpp:120:10: error: ?r? may be used uninitialized [-Werror=maybe-uninitialized]  
  120 |   int q, r;  
      |          ^  
src/rgw16_edge_detect.hpp:120:7: error: ?q? may be used uninitialized [-Werror=maybe-uninitialized]  
  120 |   int q, r;  
      |       ^  
```  
All of which are trivially fixed.  
  
It would be nice if example code did not contain compile errors.

---

## Comment 10

> Username: mloskot  
> Created_at: 2022-10-26 17:12:47 UTC  
> Url: https://github.com/boostorg/gil/pull/258#issuecomment-1292354321  

@Bockeman  it would be nice if you made it clear that your comment relates to this particular PR in any way. Otherwise, it would be nice if you posted it to discussion thread or report a bug or it would be even nicer if you could submit PR with the trivial fixes. Thanks in advance.

---

## Comment 11

> Username: Bockeman  
> Created_at: 2022-10-26 21:03:52 UTC  
> Updated_at: 2022-10-26 21:05:15 UTC  
> Url: https://github.com/boostorg/gil/pull/258#issuecomment-1292650270  

@mloskot Thanks for taking notice!  
_(I have not yet figured out what PR stands for.  Not doubt as soon as I submit this comment, it will dawn on me.)_  
  
My comment above relates to the example/canny.cpp code, the only one in the "files changed" tab above.  
_(Forgive me, I had assumed this was obvious)_  
  
Here are my trivial fixes:  
_(But I am fairly certain these are not in the most appropriate format, and I am nowhere near confident enough to attempt to edit/annoate the source code, as others have done beautifully [with coloured code segments, etc.] above.)_  
```  
/usr/include/boost/gil/extension/numeric/kernel.hpp:16:1: note: ?#pragma message: This header is deprecated. Use <boost/gil/image_processing/kernel.hpp> instead.?  
   16 | BOOST_HEADER_DEPRECATED("<boost/gil/image_processing/kernel.hpp>")  
      | ^~~~~~~~~~~~~~~~~~~~~~~  
```  
my fix:  
```  
//#include <boost/gil/extension/numeric/kernel.hpp>		// #pragma message: This header is deprecated.  
#include <boost/gil/image_processing/kernel.hpp>  
```  
  
```  
/usr/include/boost/gil/extension/numeric/convolve.hpp:15:1: note: ?#pragma message: This header is deprecated. Use <boost/gil/image_processing/convolve.hpp> instead.?  
   15 | BOOST_HEADER_DEPRECATED("<boost/gil/image_processing/convolve.hpp>")  
      | ^~~~~~~~~~~~~~~~~~~~~~~  
```  
my fix:  
```  
//#include <boost/gil/extension/numeric/convolve.hpp>		// #pragma message: This header is deprecated.  
#include <boost/gil/image_processing/convolve.hpp>  
```  
  
```  
In file included from src/rgw16_edge_align.cpp:32:  
src/rgw16_edge_detect.hpp: In function ?void gaussian_blur(const SrcView&)?:  
src/rgw16_edge_detect.hpp:49:52: error: ?convolve_option_output_ignore? was not declared in this scope  
   49 |   convolve_rows<gray32f_pixel_t>(src, kernel, src, convolve_option_output_ignore);  
      |                                                    ^~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
```  
my fix:  
```  
  convolve_rows<gray32f_pixel_t>(src, kernel, src  
  , boundary_option::output_ignore);  
  //, convolve_option_output_ignore);	// error: ?convolve_option_output_ignore? was not declared in this scope  
  convolve_cols<gray32f_pixel_t>(src, kernel, src  
  , boundary_option::output_ignore);  
  //, convolve_option_output_ignore);	// error: ?convolve_option_output_ignore? was not declared in this scope  
```  
  
```  
src/rgw16_edge_detect.hpp: In function ?void sobel_filtering(const boost::gil::gray8c_view_t&, const boost::gil::gray8_view_t&, const boost::gil::gray32f_view_t&)?:  
src/rgw16_edge_detect.hpp:76:75: error: ?convolve_option_output_zero? was not declared in this scope  
   76 |   convolve_rows<gray32f_pixel_t>(src, first_sobel_kernel, view(vertical), convolve_option_output_zero);  
      |                                                                           ^~~~~~~~~~~~~~~~~~~~~~~~~~~  
```  
my fix:  
```  
  convolve_rows<gray32f_pixel_t>(src, first_sobel_kernel, view(vertical)  
  , boundary_option::output_ignore);  
  //, convolve_option_output_zero);	// error: ?convolve_option_output_ignore? was not declared in this scope  
  convolve_cols<gray32f_pixel_t>(const_view(vertical), second_sobel_kernel, view(vertical)  
  , boundary_option::output_ignore);  
  //, convolve_option_output_zero);	// error: ?convolve_option_output_ignore? was not declared in this scope  
  
  convolve_rows<gray32f_pixel_t>(src, second_sobel_kernel, view(horizontal)  
  , boundary_option::output_ignore);  
  //, convolve_option_output_zero);	// error: ?convolve_option_output_ignore? was not declared in this scope  
  convolve_cols<gray32f_pixel_t>(const_view(horizontal), first_sobel_kernel, view(horizontal)  
  , boundary_option::output_ignore);  
  //, convolve_option_output_zero);	// error: ?convolve_option_output_ignore? was not declared in this scope  
```  
  
```  
src/rgw16_edge_detect.hpp: In function ?int main_detect(int, char**)?:  
src/rgw16_edge_detect.hpp:229:35: error: ISO C++ forbids converting a string constant to ?char*? [-Werror=write-strings]  
  229 |   char* input                   = "test.jpg";  
      |                                   ^~~~~~~~~~  
src/rgw16_edge_detect.hpp:230:35: error: ISO C++ forbids converting a string constant to ?char*? [-Werror=write-strings]  
  230 |   char* output                  = "canny.jpg";  
      |                                   ^~~~~~~~~~~  
```  
my fix:  
```  
  //char* input			= "test.jpg";	// error: ISO C++ forbids converting a string constant to ?char*? [-Werror=write-strings]  
  //char* output		= "canny.jpg";	// error: ISO C++ forbids converting a string constant to ?char*? [-Werror=write-strings]  
  //char  input[]			= "test.jpg";	// below: error: incompatible types in assignment of ?char*? to ?char [9]?  
  //char  output[]		= "canny.jpg";	// below: error: incompatible types in assignment of ?char*? to ?char [10]?  
  std::string	input		= "test.jpg";  
  std::string	output		= "canny.jpg";  
```  
  
```  
src/rgw16_edge_detect.hpp: In function ?void non_maximal_suppression(const boost::gil::gray8c_view_t&, const boost::gil::gray32fc_view_t&, const boost::gil::gray8_view_t&)?:  
src/rgw16_edge_detect.hpp:120:10: error: ?r? may be used uninitialized [-Werror=maybe-uninitialized]  
  120 |   int q, r;  
      |          ^  
src/rgw16_edge_detect.hpp:120:7: error: ?q? may be used uninitialized [-Werror=maybe-uninitialized]  
  120 |   int q, r;  
      |       ^  
```  
  
```        
  //int q, r;		// error: ?r? may be used uninitialized [-Werror=maybe-uninitialized]  
  int q(0);  
  int r(0);  
```  
  
Thanks @Antropovi for contributing this code.  It works well for me and saved me a lot of time and effort in painstakingly constucting such from scratch.  
_(Aside: I am currently attempting some tweaks, because unwanted artefacts are also being detected as edges)_

---

## Comment 12

> Username: mloskot  
> Created_at: 2022-10-27 07:19:13 UTC  
> Url: https://github.com/boostorg/gil/pull/258#issuecomment-1293098579  

@Bockeman   
> (I have not yet figured out what PR stands for.   
  
PR = [Pull Request](https://docs.github.com/en/pull-requests/collaborating-with-pull-requests/proposing-changes-to-your-work-with-pull-requests/about-pull-requests)  
  
> My comment above relates to the example/canny.cpp code, the only one in the "files changed" tab above.  
  
I see. Well, this PR is a proposal and it has not been accepted and merged into the GIL sources. So it should be considered as a work in progress.  
  
> (Aside: I am currently attempting some tweaks, because unwanted artefacts are also being detected as edges)  
  
If you have an improved version of this example here, then please, feel free to contribute it to GIL and submit your version as new PR.

---
