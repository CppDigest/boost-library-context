# #173 - boost::pfr::get_name fails if a structurehas a member with a void pointer type [Open]

> Username: StefanS52u1  
> Created at: 2024-07-16 08:52:48 UTC  
> Updated at: 2024-07-16 11:50:28 UTC  
> Url: https://github.com/boostorg/pfr/issues/173  

VS2022 does not compile the following code snippet ,  if any member is a void pointer. Are there any workaround?. I did not check, this behavior with clang or gcc.  
  
BOOST 1.84  
VS 17.10.3  
  
Example:  
  
#include "stdafx.h"  
#include <boost/pfr/core.hpp>  
#include <boost/pfr/core_name.hpp>  
#include <boost/pfr/traits.hpp>  
  
struct LongPointerTest {  
    long* OwningThread;  
};  
  
struct VoidPointerTestFailsToCompile {  
    void* OwningThread;  
};  
  
int main()  
{  
    static_assert(boost::pfr::get_name<0, LongPointerTest>() == "OwningThread");  
    auto name = boost::pfr::get_name<0, VoidPointerTestFailsToCompile>();  
}
