# #83 - Iterator zero incrementing leads to assert on empty vector. [Closed]

> Username: Klayflash  
> Created at: 2018-09-11 08:31:21 UTC  
> Updated at: 2018-11-10 22:14:01 UTC  
> Closed at: 2018-11-10 22:14:00 UTC  
> Url: https://github.com/boostorg/container/issues/83  

This construction leads to assert:  
```  
  auto it = v.begin(); // empty vector  
  it += 0; // <---- assert here  
```  
See also https://github.com/boostorg/container/commit/b56cbb60e05cc06fbebed03f8b359b116e788f2a  
Full code:  
  
```  
  
#include <boost/container/vector.hpp>  
#include <vector>  
#include <assert.h>  
  
#define TEST_ASSERT(x) assert((x))  
  
typedef int StorableType;  
  
typedef std::vector<StorableType> StdVector;  
typedef boost::container::vector<StorableType> BoostVector;  
  
template<typename Vector>  
void insertToPosition(Vector& v,size_t pos,StorableType val)  
{  
  auto it = v.begin();  
  it += pos;  
  v.insert(it,val);  
}  
  
template<typename Vector>  
void test()  
{  
  // insert to begin and non empty  
  {  
    Vector v = {11,12};  
    insertToPosition(v,0,10);  
    TEST_ASSERT((v == Vector{10,11,12}));  
  }  
  // insert to empty  
  {  
    Vector v;  
    insertToPosition(v,0,10);  
    TEST_ASSERT((v == Vector{10}));  
  }  
}  
  
  
int main()  
{  
  test<StdVector>();  
  test<BoostVector>();  
  
  return 0;  
}  
```

---

## Comment 1

> Username: AL1ve1T  
> Created at: 2018-10-17 09:43:48 UTC  
> Url: https://github.com/boostorg/container/issues/83#issuecomment-430562594  

Please, can you give more detailed description about that issue?

---

## Comment 2

> Username: Klayflash  
> Created at: 2018-10-17 09:54:28 UTC  
> Updated at: 2018-10-17 09:55:16 UTC  
> Url: https://github.com/boostorg/container/issues/83#issuecomment-430566005  

If you run this code then   
  
`test<BoostVector>();`  
  
 call will give you boost assert.  
  
`test<StdVector>();`  
  
 runs without assert.

---

## Comment 3

> Username: igaztanaga  
> Created at: 2018-11-10 22:14:00 UTC  
> Url: https://github.com/boostorg/container/issues/83#issuecomment-437625631  

Many thanks for the report. Fixed in the following commit, to be released in Boost 1.69:  
  
https://github.com/boostorg/container/commit/10a618afe88ed16a9662316d2f5ff51d6041f4c9
