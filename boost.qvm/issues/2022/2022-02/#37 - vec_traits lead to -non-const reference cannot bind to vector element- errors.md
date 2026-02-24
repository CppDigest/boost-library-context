# #37 - vec_traits lead to "non-const reference cannot bind to vector element" errors [Closed]

> Username: octopus-prime  
> Created at: 2022-02-05 12:08:02 UTC  
> Updated at: 2022-04-13 21:05:59 UTC  
> Closed at: 2022-04-13 21:05:59 UTC  
> Url: https://github.com/boostorg/qvm/issues/37  

Broken:  
```  
using vector_t = __v4sf;  
  
namespace boost { namespace qvm {  
  
  template <>  
  struct vec_traits<vector_t> {  
  
    static int const dim=4;  
  
    typedef float scalar_type;  
  
    template <int I>  
    static inline scalar_type & write_element(vector_t & v ) {  
      return v[I]; <-- ERROR 'non-const reference cannot bind to vector element'  
    }  
  
    template <int I>  
    static inline scalar_type read_element(vector_t const & v ) {  
      return v[I];  
    }  
  };  
  
} }  
```  
All because of 'by-ref' result...  
  
```  
    template <int I>  
    static inline void write_element(vector_t & v, scalar_type s) {  
      v[I] = s;  
    }  
```  
Would compile without errors...  
  
Same problems with mat_traits!  
  
BTW - why I have to adapt SIMD? There should be an out-of-the-box support for SIMD in QVM?!

---

## Comment 1

> Username: octopus-prime  
> Created at: 2022-02-05 14:28:45 UTC  
> Updated at: 2022-02-05 14:41:23 UTC  
> Url: https://github.com/boostorg/qvm/issues/37#issuecomment-1030634627  

```  
using vector_t = __v4sf;  
using matrix_t = std::array<vector_t, 4>;  
  
namespace v2 {  
  
using namespace boost::qvm;  
  
using temp_t = mat<scalar_t, 4, 4>;  
  
union foo_t {  
    matrix_t as_matrix;  
    temp_t as_temp;  
};  
  
matrix_t  
invert(const matrix_t &matrix) {  
    const temp_t& m = *(const temp_t *) &matrix;  
    foo_t f;  
    f.as_temp = inverse(m);  
    return f.as_matrix;  
}  
  
}  
  
```  
  
So, this is the best way to use?! Or is there a way to properly adapt?

---

## Comment 2

> Username: zajo  
> Created at: 2022-02-07 06:40:47 UTC  
> Url: https://github.com/boostorg/qvm/issues/37#issuecomment-1031125594  

This is a difficult issue, because QVM needs to support operations on individual elements of vectors and matrices. For example, it is not possible to make the following work without mutable references to elements:  
  
```  
float3 v1 = ....;  
float2 v2 = ....;  
ZX(v1) += v2;  
```  
  
Another example:  
  
```  
float33 m = ....;  
float3 v = ....;  
col<2>(transposed(m)) += v;  
```  
  
Note that in both cases we're working with lvalues.

---

## Comment 3

> Username: zajo  
> Created at: 2022-02-07 07:06:36 UTC  
> Url: https://github.com/boostorg/qvm/issues/37#issuecomment-1031138519  

Actually I'm wrong about that, it is possible to implement. I'll think about it.

---

## Comment 4

> Username: zajo  
> Created at: 2022-04-13 21:05:59 UTC  
> Url: https://github.com/boostorg/qvm/issues/37#issuecomment-1098486816  

This is done, see latest `master` branch. Unfortunately it did not make it in the 1.79 release.
