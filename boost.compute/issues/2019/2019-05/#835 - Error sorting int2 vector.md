# #835 - Error sorting int2 vector [Closed]

> Username: ymhsieh  
> Created at: 2019-05-25 03:46:28 UTC  
> Updated at: 2019-05-25 13:55:14 UTC  
> Closed at: 2019-05-25 13:55:14 UTC  
> Url: https://github.com/boostorg/compute/issues/835  

Hello, I've gotten wrong results doing sorting with the following code using NVIDIA or Intel GPU.  I've tried both the master branch or develop branch and they both give me incorrect results.    
Would you please look into this issue?  Thanks.  
```cpp  
#include <iostream>  
using namespace std;  
#include <boost/compute.hpp>  
namespace compute = boost::compute;  
using compute::uint2_;  
  
BOOST_COMPUTE_FUNCTION(uint2_, myGen, (), {  
  uint2 out;  
  out.x = get_global_id(0);  
  out.y = out.x * 4;  
  return out;  
});  
  
BOOST_COMPUTE_FUNCTION(bool, myComp, (uint2_ a, uint2_ b), {  
  return a.x > b.x;  
});  
  
int main() {  
  cout << "\nBoost compute version: "  
    << BOOST_COMPUTE_VERSION_MAJOR << "."  
    << BOOST_COMPUTE_VERSION_MINOR << "."  
    << BOOST_COMPUTE_VERSION_PATCH << endl;  
  auto device = compute::system::default_device();  
  cout << "\nDevice: " << device.name();  
  compute::vector<uint2_> array(64);  
  compute::generate(array.begin(), array.end(), myGen);  
  
  for(const auto& i: array) {  
    cout << i << " ";  
  }  
  compute::sort( array.begin(), array.end(), myComp);  
  
  cout << endl;  
  for(const auto& i: array) {  
    cout << i << " ";  
  }  
  
  
  return 0;  
}  
```

---

## Comment 1

> Username: jszuppe  
> Created at: 2019-05-25 07:35:58 UTC  
> Url: https://github.com/boostorg/compute/issues/835#issuecomment-495877767  

Try replacing  
```cpp  
 for(const auto& i: array) {  
    cout << i << " ";  
  }  
```  
with  
```cpp  
for(auto i = array.begin; i != array.end(); i++)  
{  
    cout << i.read(compute::system::default_queue()) << " ";  
}  
```  
Alternatively, you can also add `compute::system::default_queue().finish()` after sorting.   
  
I'm not 100% sure, but the problem might be that  
```cpp  
 for(const auto& i: array) {  
    cout << i << " ";  
  }  
```  
uses different queue to read values than sorting operations, therefore they are not synchronised.

---

## Comment 2

> Username: ymhsieh  
> Created at: 2019-05-25 13:55:14 UTC  
> Url: https://github.com/boostorg/compute/issues/835#issuecomment-495920282  

Thank you for your quick advise, and it did solve the problem...
