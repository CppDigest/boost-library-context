# #424 - Missing alignment default value in image constructor [Closed]

> Username: mloskot  
> Created at: 2020-01-19 13:52:35 UTC  
> Updated at: 2020-02-01 17:02:44 UTC  
> Closed at: 2020-02-01 17:02:44 UTC  
> Url: https://github.com/boostorg/gil/issues/424  

### Actual behavior  
  
<!-- A clear description of what happens -->  
  
Unlike all the other constructors, this one does not receive default value `alignment = 0`  
  
https://github.com/boostorg/gil/blob/291b27721af7b0c6e803049acd59bfa14c6a4bad/include/boost/gil/image.hpp#L78-L84  
  
```cpp  
#include <boost/gil.hpp>  
namespace gil = boost::gil;  
int main()  
{  
    gil::point_t dims{32, 32};  
    gil::bgr8_pixel_t p{32, 64, 128};  
    gil::bgr8_image_t i(dims, p);  
      // image(const point_t& dimensions, const Pixel& p_in, std::size_t alignment, const Alloc alloc_in = Alloc())  
      // constructor is not picked up, compilation fails  
}  
```  
  
### Expected  behavior  
  
`gil::bgr8_image_t i(dims, p);` should pick the expected constructor.  
  
### References  
  
- This bug causes failures of the newly proposed `image` tests #423  
  
### Environment  
  
<!-- Any details that may help GIL maintainers help you -->  
  
- Compiler version: GCC 9.2, clang 9.0, VS 2017, VS 2019

---

## Comment 1

> Username: parthsarthi1  
> Created at: 2020-01-19 19:42:29 UTC  
> Updated at: 2020-01-19 19:49:09 UTC  
> Url: https://github.com/boostorg/gil/issues/424#issuecomment-576039224  

@mloskot can I try to look into the issue to learn more about GIL. thanks.

---

## Comment 2

> Username: mloskot  
> Created at: 2020-01-20 10:14:20 UTC  
> Updated at: 2020-01-20 10:15:02 UTC  
> Url: https://github.com/boostorg/gil/issues/424#issuecomment-576203565  

@parthsarthi1 You're welcome to take care of it and submit PR. No tests need to be added for it because #423 will add some (I'll update that PR as soon as your PR is merged).  
  
You should try to compile the minimal example from the description to see if your fix works though.

---

## Comment 3

> Username: ArbitCode  
> Created at: 2020-01-21 19:28:08 UTC  
> Url: https://github.com/boostorg/gil/issues/424#issuecomment-576840980  

@mloskot I think this issue is resolved as while I'm trying to find the issue. I haven't seen any issues.

---

## Comment 4

> Username: mloskot  
> Created at: 2020-01-21 19:36:21 UTC  
> Url: https://github.com/boostorg/gil/issues/424#issuecomment-576844370  

@ArbitCode What compiler did you use? What C++ standard version?

---

## Comment 5

> Username: ArbitCode  
> Created at: 2020-01-21 19:48:10 UTC  
> Updated at: 2020-01-21 19:58:06 UTC  
> Url: https://github.com/boostorg/gil/issues/424#issuecomment-576849781  

sorry, Actually that time it does not show the error when I was finding bugs, Now it shows error.  
 FYI gcc 9.2. This is the right compiler, isn't it?

---

## Comment 6

> Username: mloskot  
> Created at: 2020-01-21 19:57:15 UTC  
> Updated at: 2020-01-21 20:04:41 UTC  
> Url: https://github.com/boostorg/gil/issues/424#issuecomment-576853688  

@ArbitCode So, you confirm the issue. Good.   
  
I added list of compilers that I tried to the description.
