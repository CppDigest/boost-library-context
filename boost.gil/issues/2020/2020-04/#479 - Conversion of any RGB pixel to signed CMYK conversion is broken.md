# #479 - Conversion of any RGB pixel to signed CMYK conversion is broken [Closed]

> Username: mloskot  
> Created at: 2020-04-10 17:09:57 UTC  
> Updated at: 2021-01-22 17:00:13 UTC  
> Closed at: 2020-10-10 13:19:04 UTC  
> Url: https://github.com/boostorg/gil/issues/479  

RGB pixels based on **unsigned** integral channels are not correctly translated to CMYK space with full range of **signed** integral channels.  
  
The original implementation did not handle properly signed CMYK pixels, so this issue is independent from #406.  
  
### C++ Minimal Working Example  
  
<!-- C++ program or snippet that can be compiled and used to reproduce your problem -->  
```cpp  
#include <boost/gil.hpp>  
namespace gil = boost::gil;  
int main()  
{  
    // black  
    {  
        gil::rgb8_pixel_t src{0, 0, 0};  
        gil::cmyk8s_pixel_t dst;  
        gil::color_convert(src, dst); // dst={0, 0, 0, 0}  
                             // expected dst={-128, -128, -128, 127}  
    }  
    // white  
    {  
        gil::rgb8_pixel_t src{255, 255, 255};  
        gil::cmyk8s_pixel_t dst;  
        gil::color_convert(src, dst); // dst={0, 0, 0, -128}  
                             // expected dst={-128, -128, -128, -128}  
    }  
    // blue  
    {  
        gil::rgb8_pixel_t src{0, 0, 255};  
        gil::cmyk8s_pixel_t dst;  
        gil::color_convert(src, dst); // dst={127, 127, 0, -128}  
                             // expected dst={127, 127, -128, -128}  
        print(1, src, dst);  
    }  
    // yellow  
    {  
        gil::rgb8_pixel_t src{255, 255, 0};  
        gil::cmyk8s_pixel_t dst;  
        gil::color_convert(src, dst); // dst={0, 0, 127, -128}  
                             // expected dst={-128, -128, 127, -128}  
    }  
}  
```  
  
### References  
  
- Confirmed by #470 while testing bug in #406  
  
### Environment  
  
<!-- Any details that may help GIL maintainers help you -->  
  
- Version (Git ref or `<boost/version.hpp>`): <=1.72, current develop

---

## Comment 1

> Username: Vivekyadavgithub  
> Created at: 2020-08-14 06:21:42 UTC  
> Url: https://github.com/boostorg/gil/issues/479#issuecomment-673909271  

this is my first time . Can u give me some pointers to fix this bug

---

## Comment 2

> Username: mloskot  
> Created at: 2020-08-14 08:10:00 UTC  
> Url: https://github.com/boostorg/gil/issues/479#issuecomment-673949515  

> Can u give me some pointers to fix this bug  
  
I'm not sure what kind of pointers you may need.  
The bug is described quite clearly.  
The bug description is accompanied with minimal, reproducible example which clearly points where is the problem (actual output vs expected output).  
  
The task list is quite obvious, for example, it could be:  
  
1. Compile the program  
2. Run it, observe the actual vs expected output.  
3. Add test case(s) confirm the bug (it can be based on the minimal example provided).  
4. Start debugging it and reading the source code of the implementation  
5. Find out why the conversion does not output expected results.  
5. Fix it.  
6. Confirm the tests pass, add more test cases...  
7. Submit PR and request review  
8. Address reviewers' comments and requests  
9. Get the PR merged  
10. Go for a coffee or a pint of ale and celebrate the great achievement!

---

## Comment 3

> Username: mloskot  
> Created at: 2020-10-10 13:18:51 UTC  
> Url: https://github.com/boostorg/gil/issues/479#issuecomment-706548167  

Fixed by #522
