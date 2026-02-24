# #679 refactor: Make packed_pixel trivially copyable and assignable [Merged]

> Username: marco-langer  
> Created at: 2022-05-28 06:52:11 UTC  
> Updated at: 2022-05-28 09:07:54 UTC  
> Merged at: 2022-05-28 09:07:54 UTC  
> Closed at: 2022-05-28 09:07:54 UTC  
> Url: https://github.com/boostorg/gil/pull/679  

<!-- Pull Requests MUST come from topic branch based on develop, and NEVER on `master) --->  
  
### Description  
  
Following the Rule of Zero, this PR removes the user-defined copy constructor and copy assignment operator from `packed_pixel`. The type is now trivially copyable and trivially assignable:  
  
```C++  
#include <boost/gil.hpp>  
#include <boost/mp11.hpp>  
#include <type_traits>  
  
namespace gil = boost::gil;  
namespace mp11 = boost::mp11;  
  
int main()  
{  
  using packed_channel_references_3 = typename gil::detail::packed_channel_references_vector_type  
  <  
      std::uint8_t,  
      mp11::mp_list_c<int, 3>  
  >::type;  
  
  using packed_pixel_gray3 = gil::packed_pixel  
  <  
      std::uint8_t,  
      packed_channel_references_3,  
      gil::gray_layout_t  
  >;  
  
  static_assert(std::is_trivially_copyable<packed_pixel_gray3>::value);  
  static_assert(std::is_trivially_assignable<packed_pixel_gray3, packed_pixel_gray3>::value);  
  
  return 0;  
}  
```  
  
### Tasklist  
  
- [x] Ensure all CI builds pass  
- [x] Review and approve

---

## Review 1 [Approved]

> Username: mloskot  
> Created_at: 2022-05-28 09:06:00 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/gil/pull/679#pullrequestreview-988441607  

LGTM, thanks

---
