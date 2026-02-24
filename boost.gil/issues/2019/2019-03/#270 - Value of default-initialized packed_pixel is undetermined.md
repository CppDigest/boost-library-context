# #270 - Value of default-initialized packed_pixel is undetermined [Closed]

> Username: mloskot  
> Created at: 2019-03-25 15:39:35 UTC  
> Updated at: 2019-04-01 16:22:49 UTC  
> Closed at: 2019-04-01 16:19:23 UTC  
> Url: https://github.com/boostorg/gil/issues/270  

### Minimal Working Example (in C++)  
  
```cpp  
#include <boost/gil.hpp>  
#include <boost/mpl/vector_c.hpp>  
namespace gil = boost::gil;  
  
using gray3bits_pixel = gil::packed_pixel  
<  
    std::uint8_t,  
    typename gil::detail::packed_channel_references_vector_type  
    <  
        std::uint8_t,  
        boost::mpl::vector1_c<int, 3>  
    >::type,  
    gil::gray_layout_t  
>;  
  
int main()  
{  
    gray3bits_pixel p1;                   // packed_pixel()  
    gray3bits_pixel p2{std::uint8_t{0}};  // packed_pixel(BitField const&)  
  
    int const new_channel_value = 3;  
    p1 = new_channel_value; // It does not clear any existing `_bitfield` value  
    p2 = new_channel_value;  
  
    assert(p1._bitfield == p2._bitfield); // Reasonable assumption, isn't it  
}  
```  
  
### Actual behavior  
  
The assertion fails because default-initialized `packed_pixel` results in random (garbage) value of `_bitfield`.  
  
### Expected  behavior  
  
Default-initialized `packed_pixel` value should be determined and reproducible (e.g. Zero)  
  
### References  
  
* [Default value of pixel channel (homogeneous_color_base)](https://lists.boost.org/boost-gil/2019/03/0140.php)  
* https://github.com/boostorg/gil/pull/248#issuecomment-468964512

---

## Comment 1

> Username: mloskot  
> Created at: 2019-03-25 15:41:51 UTC  
> Url: https://github.com/boostorg/gil/issues/270#issuecomment-476254109  

@stefanseefeld @chhenning Following my [post on the list](https://lists.boost.org/boost-gil/2019/03/0140.php), is the lack of members initialization in pixel and channel classes intentional? Is this for performance/optimization purposes? If you have any knowledge, please share.  
  
If there is no reasonable rationale behind the lack of initialization, I'd prefer to have the members zero-initialized.

---

## Comment 2

> Username: stefanseefeld  
> Created at: 2019-03-25 15:45:32 UTC  
> Url: https://github.com/boostorg/gil/issues/270#issuecomment-476255794  

I agree. In case where we don't want member initialization, say, because the storage is write-only, we should find a way to indicate that explicitly. The default should definitely be to initialize members.

---

## Comment 3

> Username: mloskot  
> Created at: 2019-03-25 16:46:23 UTC  
> Url: https://github.com/boostorg/gil/issues/270#issuecomment-476283365  

I agree. I will submit related PRs soon.
