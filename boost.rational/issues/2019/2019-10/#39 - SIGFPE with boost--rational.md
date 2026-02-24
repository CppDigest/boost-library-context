# #39 - SIGFPE with boost::rational [Open]

> Username: caolanm  
> Created at: 2019-10-05 18:11:08 UTC  
> Updated at: 2019-10-09 02:02:39 UTC  
> Url: https://github.com/boostorg/rational/issues/39  

The following crashes at runtime with SIGFPE with boost 1.69.0  
  
#include <boost/rational.hpp>  
#include <numeric>  
  
int main()  
{  
    auto i = std::numeric_limits<int>::min();  
    boost::rational<int> val(i, i);  
    return 0;  
}
