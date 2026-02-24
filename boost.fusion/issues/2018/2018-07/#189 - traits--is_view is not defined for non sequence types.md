# #189 - traits::is_view is not defined for non sequence types [Closed]

> Username: Kojoley  
> Created at: 2018-07-27 16:49:59 UTC  
> Updated at: 2018-08-03 00:46:43 UTC  
> Closed at: 2018-08-03 00:46:43 UTC  
> Url: https://github.com/boostorg/fusion/issues/189  

I am not sure why it is so, but behavior between traits is not consistent. I think that every trait should be defined for any type so `traits::is_xxx<T>::value` could be used in constexpr expressions.  
  
```cpp  
#include <boost/fusion/include/is_sequence.hpp>  
#include <boost/fusion/include/is_view.hpp>  
  
int main()  
{  
    static_assert(!boost::fusion::traits::is_sequence<int>::value); // fine  
    static_assert(!boost::fusion::traits::is_view<int>::value); // tries int::is_view  
    return 0;  
}  
```  
  
It looks like this solves the problem for `is_view`:  
```cpp  
namespace boost { namespace fusion  
{  
    namespace extension  
    {  
        template <>  
        struct is_view_impl<non_fusion_tag>  
        {  
            template <typename T>  
            struct apply : mpl::false_ {};  
        };  
	}  
}}  
```
