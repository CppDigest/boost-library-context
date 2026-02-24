# #44 - Swizzling regression since Boost 1.80 [Closed]

> Username: federkamm  
> Created at: 2023-06-06 01:27:53 UTC  
> Updated at: 2023-06-06 20:36:56 UTC  
> Closed at: 2023-06-06 20:36:55 UTC  
> Url: https://github.com/boostorg/qvm/issues/44  

The following code compiled in Boost 1.79 but fails in 1.80 and 1.82 for variable c:  
  
    #include <boost/qvm/all.hpp>  
    using namespace boost::qvm;  
    void f() {  
        auto && a = diag_mat(_0X(0));  
        mat<int, 2, 2> b = diag_mat(X0(0));  
        mat<int, 2, 2> c = diag_mat(_0X(0));  
    }  
  
Tested using https://godbolt.org: "x86_64 gcc 13.1" with no compile options and only "Libraries": Boost.

---

## Comment 1

> Username: zajo  
> Created at: 2023-06-06 20:36:55 UTC  
> Url: https://github.com/boostorg/qvm/issues/44#issuecomment-1579412215  

Thanks. Fixed on develop: https://github.com/boostorg/qvm/commit/fa272cb0b07ce85b5d8d53fb3dfade389e9cc4df.
