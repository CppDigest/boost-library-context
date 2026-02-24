# #26 - Missing include: boost/qvm/detail/mat_assign.hpp not included in boost/qvm/detail/transp_impl.hpp [Closed]

> Username: KABoissonneault  
> Created at: 2020-09-16 02:21:59 UTC  
> Updated at: 2020-09-17 00:24:26 UTC  
> Closed at: 2020-09-17 00:24:26 UTC  
> Url: https://github.com/boostorg/qvm/issues/26  

If `boost::qvm::transposed` is used on its own, we get a compilation error.  
  
```  
#include <boost/qvm/mat_traits.hpp>  
#include <boost/qvm/mat_traits_defaults.hpp>  
  
#include <boost/qvm/map_mat_mat.hpp>  
  
struct float33  
{  
    float elements[9];  
};  
  
template<>  
struct boost::qvm::is_mat<float33>  
{  
    static bool const value = true;  
};  
  
template<>  
struct boost::qvm::mat_traits<float33> : boost::qvm::mat_traits_defaults<float33, float, 3, 3>  
{  
    template<int R, int C>  
    static float& write_element(float33& m)  
    {  
        return m.elements[R*3+C];  
    }  
};  
  
float33 transpose(float33 const& m)  
{  
    return boost::qvm::transposed(m);  
}  
```  
The result is  
```  
In file included from /opt/compiler-explorer/libs/boost_1_74_0/boost/qvm/map_mat_mat.hpp:13,  
  
                 from <source>:4:  
  
/opt/compiler-explorer/libs/boost_1_74_0/boost/qvm/detail/transp_impl.hpp: In instantiation of 'boost::qvm::qvm_detail::transposed_<OriginalMatrix>::operator R() const [with R = float33; OriginalMatrix = float33]':  
  
<source>:29:36:   required from here  
  
/opt/compiler-explorer/libs/boost_1_74_0/boost/qvm/detail/transp_impl.hpp:46:27: error: 'assign' was not declared in this scope  
  
   46 |                     assign(r,*this);  
  
      |                     ~~~~~~^~~~~~~~~  
```  
  
(URL for convenience: https://gcc.godbolt.org/z/7918sb)  
  
boost/qvm/detail/transp_impl.hpp should probably include boost/qvm/detail/mat_assign.hpp, the same way mat_operations.hpp does for `identity_mat_`

---

## Comment 1

> Username: zajo  
> Created at: 2020-09-16 02:57:48 UTC  
> Url: https://github.com/boostorg/qvm/issues/26#issuecomment-693139807  

All QVM headers (that aren't in the `detail` directory) are designed to not cause compilation errors if included on their own (e.g. if they're the first `#include` in a compilation unit). This does not mean that you can instantiate every single template they define; this may require including additional headers, as is the case here. Consider that the call to `assign` is not qualified, which means that depending on the types used, a different overload may be needed -- and the correct overload may require including a user header.  
  
In this case:  
  
* If you want to minimize your header dependencies, you should `#include <boost/qvm/mat_operations3.hpp>` (which won't introduce 2D or 4D overloads): https://gcc.godbolt.org/z/8Ed9j3.  
  
* If you don't care about header dependencies, you can just `#include <boost/qvm/all.hpp>`.
