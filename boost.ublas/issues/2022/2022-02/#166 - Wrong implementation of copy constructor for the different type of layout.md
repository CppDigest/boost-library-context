# #166 - Wrong implementation of copy constructor for the different type of layout [Open]

> Username: amitsingh19975  
> Created at: 2022-02-12 12:10:44 UTC  
> Updated at: 2022-02-12 12:15:25 UTC  
> Url: https://github.com/boostorg/ublas/issues/166  

The dynamic and static rank tensors have a constructor, which takes the tensor of different layouts, implemented incorrectly with an unknown or unimplemented member function. To fix the issue, we have to use the `copy` algorithm, but instead, we are just copying the whole container, without considering the layout. Furthermore, we are not asserting or checking the type of the tensor if it matches the current tensor.  
  
Current Implementation  
  
```cpp  
  template<typename OTE>  
  explicit inline tensor_core (const tensor_core<OTE> &other)  
    : tensor_expression_type<self_type>{}  
    , _extents  (ublas::begin(other.extents  ()), ublas::end (other.extents  ()))  
    , _strides  (ublas::begin(other.extents  ()), ublas::end (other.extents  ()))  
    , _container(  std::begin(other.container()),   std::end (other.container()))  
  {  
  }  
```  
To fix:  
- use the method `tensor::base` instead of unknown method `tensor::container`  
- use the `copy` algorithm from the `boost/numeric/ublas/tensor/algorithm.hpp`  
- static assert to check if the types are the same or not.  
  
After the fix, enable the test that tests the construction of the other layout.
