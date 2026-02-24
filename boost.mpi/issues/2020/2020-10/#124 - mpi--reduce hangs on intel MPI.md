# #124 - mpi::reduce hangs on intel MPI [Open]

> Username: francescopt  
> Created at: 2020-10-09 08:51:23 UTC  
> Updated at: 2020-10-09 08:51:23 UTC  
> Url: https://github.com/boostorg/mpi/issues/124  

The following program hangs on calling ```boost::mpi::reduce``` when run on an Intel MPI environment.  
  
```  
#include <algorithm>  
#include <iostream>  
#include <vector>  
#include <boost/mpi/collectives.hpp>  
#include <boost/mpi/operations.hpp>  
#include <boost/serialization/vector.hpp>  
  
struct sum_vec_vec {  
  std::vector<double> operator()(const std::vector<double>& a, const std::vector<double>&b) const  
  {  
    std::vector<double> res(a.size());  
    std::transform(a.begin(), a.end(), b.begin(), res.begin(), [](double x, double y) { return x + y; });  
    return res;  
  }  
};  
  
namespace boost {  
  namespace mpi {  
    template <>  
    struct is_commutative<sum_vec_vec, std::vector<double>> : mpl::true_ { };  
  }  
}  
  
int main()  
{  
  namespace mpi = boost::mpi;  
  
  mpi::environment env;  
  mpi::communicator world;  
  
  std::size_t size = 1000;  
  std::size_t L = 32;  
  
  std::vector<std::vector<double>> correlations;  
  
  // Fill up with some data  
  for (std::size_t i = 0; i < size; ++i)  
    {  
      int l = 0;  
  
      std::vector<double> corr(L);  
      for (auto&x : corr)  
        x = l++;  
      correlations.emplace_back(std::move(corr));  
    }  
  
  std::vector<std::vector<double>> av_correlations(correlations.size());  
  std::cout << "Ready for mpi::reduce" << std::endl;  
  boost::mpi::reduce(world, &correlations.front(), correlations.size(), &av_correlations.front(), sum_vec_vec{}, 0);  
  
  return 0;  
}  
  
```  
  
The specific of the MPI environment are:  
  
```  
MPI_Get_library_version: Intel(R) MPI Library 2019 Update 6 for Linux* OS  
MPI_VERSION: 3  
I_MPI_NUMVERSION: 20190006300  
```  
  
Boost version is 1.74.0, and it defines:  
  
```  
BOOST_MPI_VERSION: 3  
BOOST_MPI_USE_IMPROBE: 1  
```  
  
The program above very often hangs when run for example with >6 tasks, and always hangs when, say, Ntasks=192.  
  
The reason apparently lies in the use of the ```MPI_Mprobe``` routines in [point_to_point.cpp](https://github.com/boostorg/mpi/blob/070c3c707d50fee0515e00d2e2c9a5744f10bbba/src/point_to_point.cpp#L76).  
On recompiling the library with the flag ```BOOST_MPI_USE_IMPROBE``` disabled in [config.hpp](https://github.com/boostorg/mpi/blob/ace80263884ee2af3cef2ab3644ff4b41d154e49/include/boost/mpi/config.hpp#L137), the program ends without issues. Also, the program runs without problems, on openmpi and enabled ```BOOST_MPI_USE_IMPROBE```.  
  
All in all, I suspect that the bug mentioned [here](https://github.com/boostorg/mpi/issues/63#issuecomment-415905771) is still there.
