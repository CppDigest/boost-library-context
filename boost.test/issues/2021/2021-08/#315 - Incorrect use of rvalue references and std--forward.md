# #315 - Incorrect use of rvalue references and std::forward [Open]

> Username: jonesmz  
> Created at: 2021-08-16 19:35:33 UTC  
> Updated at: 2022-03-05 20:43:40 UTC  
> Url: https://github.com/boostorg/test/issues/315  

See: https://github.com/boostorg/test/blob/11a8d379b473ebe67dcc7792b6411ba312f60398/include/boost/test/data/monomorphic/grid.hpp#L96  
  
Which boils down to  
  
```cpp  
template<typename DataSet1, typename DataSet2>  
class grid {  
    //! Constructor  
    grid( DataSet1&& ds1, DataSet2&& ds2 )  
    : m_ds1( std::forward<DataSet1>( ds1 ) )  
    , m_ds2( std::forward<DataSet2>( ds2 ) )  
    {}  
};  
```  
  
Here `DataSet1` and `DataSet2`, while template parameters to the class, are not template parameters to the function.  
  
As a result, this formulation of the constructor *always* takes these two parameters as rvalue-reference.  
  
This can be corrected in 3 ways  
  
1. Take both parameters by value  
2. Make the constructor a template function  
3. Make additional constructors that represent the cross-product of const& and && for each parameter. In this case, resulting in 4 functions.  
  
  
  
I see that this same mistake is done in more than one place, it's not only grid.hpp where it happens.
