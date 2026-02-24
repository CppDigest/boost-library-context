# #704 fix: Unused variable warning [Merged]

> Username: marco-langer  
> Created at: 2022-07-02 07:23:30 UTC  
> Updated at: 2022-07-03 06:09:01 UTC  
> Merged at: 2022-07-02 09:00:09 UTC  
> Closed at: 2022-07-02 09:00:09 UTC  
> Url: https://github.com/boostorg/gil/pull/704  

### Description  
  
This PR fixes an unused variable warning.  
  
### Minimal Working Example  
  
Compile this with `-Wall -Wextra -Wpedantic`  
  
```C++  
#include <boost/gil/extension/io/jpeg.hpp>  
#include <boost/gil/extension/dynamic_image/dynamic_image_all.hpp>  
  
namespace gil = boost::gil;  
  
int main()  
{  
    using my_image = gil::any_image<gil::gray8_image_t>;  
    my_image img;  
  
    gil::read_image("test.jpeg", img, gil::jpeg_tag{});  
  
    return 0;  
}  
```  
  
Edit: fixed a second unused variable. This did not show up as a warning, I just saw it as I was browsing the algorithm.hpp file.  
  
### Tasklist  
- [x] Ensure all CI builds pass  
- [x] Review and approve

---
