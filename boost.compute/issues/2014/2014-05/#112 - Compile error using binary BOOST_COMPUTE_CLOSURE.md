# #112 - Compile error using binary BOOST_COMPUTE_CLOSURE [Closed]

> Username: johner  
> Created at: 2014-05-14 15:28:24 UTC  
> Updated at: 2014-05-14 19:12:36 UTC  
> Closed at: 2014-05-14 19:12:36 UTC  
> Url: https://github.com/boostorg/compute/issues/112  

I'm running into an issue using a  BOOST_COMPUTE_CLOSURE along with binary transform, and I'm wondering if this is a bug or user error.  The following code is just attempting to compute b = b \* s + a.  The unary version (b = b \* s) compiles and runs fine.  The error occurs with g++ 4.6 and clang 3.3 and (for g++) begins:  
  
```  
compute/include/boost/compute/functional/detail/unpack.hpp:48:75: error: no match for ‘operator<<’ in ‘k << boost::compute::closure<Signature, CaptureTuple>::operator()(const Arg1&, const Arg2&) const   
```  
  
```  
#include <iostream>  
#include <iterator>  
#include <boost/compute.hpp>  
  
namespace compute = boost::compute;  
  
int main()  
{  
    static const int N = 10;  
    float s = 4.5;  
    compute::vector<float> a(N), b(N);  
    a.assign(N, 1.0f);  
    b.assign(N, 2.0f);  
  
#if 1  
    BOOST_COMPUTE_CLOSURE(float, scaleAddVec, (float b, float a), (s),  
            {  
                return b * s + a;  
            });           
    compute::transform(b.begin(), b.end(), a.begin(), b.begin(), scaleAddVec);  
#else  
    BOOST_COMPUTE_CLOSURE(float, scaleVec, (float b), (s),  
            {  
                return b * s;  
            });           
    compute::transform(b.begin(), b.end(), b.begin(), scaleVec);  
#endif  
    std::cout << "b = [ ";  
    compute::copy(b.begin(), b.end(),   
        std::ostream_iterator<float>(std::cout, ", "));  
    std::cout << "]" << std::endl;    
    return 0;  
}  
```

---

## Comment 1

> Username: kylelutz  
> Created at: 2014-05-14 17:52:37 UTC  
> Url: https://github.com/boostorg/compute/issues/112#issuecomment-43114291  

Yup, this is a bug. Thanks for reporting it!  
  
I've fixed it in the `develop` branch. This commit is here b41ec2b1cbe043bd3921fc7997a45c83798de231. I've also added a test based on the code you provided so this shouldn't happen again in the future.  
  
Thanks,  
Kyle

---

## Comment 2

> Username: johner  
> Created at: 2014-05-14 19:12:36 UTC  
> Url: https://github.com/boostorg/compute/issues/112#issuecomment-43124297  

Great, that fixed it for me. Thanks!
