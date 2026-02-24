# #138 - Assertion inside scatterv failed when input == nullptr [Closed]

> Username: vehlwn  
> Created at: 2021-12-02 17:42:38 UTC  
> Updated at: 2023-09-18 15:18:13 UTC  
> Closed at: 2023-09-18 15:18:13 UTC  
> Url: https://github.com/boostorg/mpi/issues/138  

Open MPI v4.1.2  
Boost 1.76.0  
Arch Linux  
c++ (GCC) 11.1.0  
  
Open MPI C API work well. Debug build with Boost API crashes at runtime due to assertion:  
  
```  
boost-example: /usr/include/boost/mpi/collectives/scatterv.hpp:32:  
void boost::mpi::detail::scatterv_impl(const boost::mpi::communicator&, const T*, T*, int, const int*, const int*, int, mpl_::true_) [with  
 T = int; mpl_::true_ = mpl_::bool_<true>]:  
Assertion `bool(sizes) == bool(in_values)' failed.  
*** Process received signal ***  
Signal: Aborted (6)  
Signal code:  (-6)  
```  
Abort occurs in [this file](https://github.com/boostorg/mpi/blob/ace80263884ee2af3cef2ab3644ff4b41d154e49/include/boost/mpi/collectives/scatterv.hpp#L32).  
Does this assert conform MPI 3.1 [standard](https://www.mpi-forum.org/docs/mpi-3.1/mpi31-report.pdf)? Page 161 par. 5.6. SCATTER says:  
  
> MPI_SCATTERV  
> IN -- sendbuf -- address of send buffer (choice, significant only at root)  
  
Later in page 162:  
  
> The send buffer is ignored for all non-root processes.  
  
Minimal source files and logs attached.  
  
main.cpp:  
  
<details>  
  <summary>Spoiler warning</summary>  
    
```c++  
#include <algorithm>  
#include <iostream>  
#include <mpi.h>  
#include <numeric>  
#include <vector>  
  
std::vector<int> get_sendcounts(int N, int m) {  
  if (m == N) {  
    std::vector<int> ret;  
    ret.resize(m, 1);  
    return ret;  
  } else if (m < N) {  
    std::vector<int> ret;  
    const int q = N / m;  
    const int r = N % m;  
    ret.resize(m, q);  
    std::fill(ret.begin(), ret.begin() + r, q + 1);  
    return ret;  
  } else {  
    std::vector<int> ret;  
    ret.resize(m, 0);  
    std::fill(ret.begin(), ret.begin() + N, 1);  
    return ret;  
  }  
}  
  
std::vector<int> get_displs(const std::vector<int> &sendcounts) {  
  std::vector<int> displs;  
  displs.reserve(sendcounts.size());  
  displs.push_back(0);  
  std::partial_sum(sendcounts.begin(), sendcounts.end() - 1,  
                   std::back_inserter(displs));  
  return displs;  
}  
  
int main(int argc, char *argv[]) {  
  MPI_Init(&argc, &argv);  
  
  int rank = 0, size = 0;  
  MPI_Comm_rank(MPI_COMM_WORLD, &rank);  
  MPI_Comm_size(MPI_COMM_WORLD, &size);  
  
  int *input = nullptr;  
  constexpr int INPUT_SIZE = 10;  
  if (rank == 0) {  
    input = new int[INPUT_SIZE];  
    std::iota(input, input + INPUT_SIZE, 0);  
  }  
  const std::vector<int> sendcounts = get_sendcounts(INPUT_SIZE, size);  
  const std::vector<int> displs = get_displs(sendcounts);  
  
  std::vector<int> local_data(sendcounts[rank]);  
  MPI_Scatterv(input, sendcounts.data(), displs.data(), MPI_INT,  
               local_data.data(), local_data.size(), MPI_INT, 0,  
               MPI_COMM_WORLD);  
  std::cout << "Process " << rank << " local_data = ";  
  for (auto x : local_data)  
    std::cout << x << ' ';  
  std::cout << std::endl;  
  
  MPI_Finalize();  
  return 0;  
}  
  
```  
    
</details>  
  
main-boost.cpp:  
  
<details>  
  <summary>Spoiler warning</summary>  
    
```c++  
#include <algorithm>  
#include <boost/mpi/collectives.hpp>  
#include <boost/mpi/communicator.hpp>  
#include <boost/mpi/environment.hpp>  
#include <iostream>  
#include <numeric>  
#include <vector>  
  
std::vector<int> get_sendcounts(int N, int m) {  
  if (m == N) {  
    std::vector<int> ret;  
    ret.resize(m, 1);  
    return ret;  
  } else if (m < N) {  
    std::vector<int> ret;  
    const int q = N / m;  
    const int r = N % m;  
    ret.resize(m, q);  
    std::fill(ret.begin(), ret.begin() + r, q + 1);  
    return ret;  
  } else {  
    std::vector<int> ret;  
    ret.resize(m, 0);  
    std::fill(ret.begin(), ret.begin() + N, 1);  
    return ret;  
  }  
}  
  
std::vector<int> get_displs(const std::vector<int> &sendcounts) {  
  std::vector<int> displs;  
  displs.reserve(sendcounts.size());  
  displs.push_back(0);  
  std::partial_sum(sendcounts.begin(), sendcounts.end() - 1,  
                   std::back_inserter(displs));  
  return displs;  
}  
  
int main(int argc, char *argv[]) {  
  boost::mpi::environment env{argc, argv};  
  boost::mpi::communicator world;  
  
  int *input = nullptr;  
  constexpr int INPUT_SIZE = 10;  
  if (world.rank() == 0) {  
    input = new int[INPUT_SIZE];  
    std::iota(input, input + INPUT_SIZE, 0);  
  }  
  const std::vector<int> sendcounts = get_sendcounts(INPUT_SIZE, world.size());  
  const std::vector<int> displs = get_displs(sendcounts);  
  
  std::vector<int> local_data(sendcounts[world.rank()]);  
  boost::mpi::scatterv(world, input, sendcounts, displs, local_data.data(),  
                       local_data.size(), 0);  
  
  std::cout << "Process " << world.rank() << " local_data = ";  
  for (auto x : local_data)  
    std::cout << x << ' ';  
  std::cout << std::endl;  
  
  MPI_Finalize();  
  return 0;  
}  
  
```  
    
</details>  
  
  
  
CMakeLists.txt:  
```  
cmake_minimum_required(VERSION 3.5)  
  
project(a LANGUAGES CXX)  
  
find_package(MPI REQUIRED)  
add_executable(openmpi-example main.cpp)  
target_link_libraries(openmpi-example ${MPI_CXX_LIBRARIES})  
  
find_package(Boost REQUIRED COMPONENTS mpi)  
add_executable(boost-example main-boost.cpp)  
target_link_libraries(boost-example ${Boost_LIBRARIES})  
```  
  
```  
$ cmake -GNinja .. -DCMAKE_BUILD_TYPE=Debug -DCMAKE_VERBOSE_MAKEFILE=1  
-- The CXX compiler identification is GNU 11.1.0  
-- Detecting CXX compiler ABI info  
-- Detecting CXX compiler ABI info - done  
-- Check for working CXX compiler: /usr/bin/c++ - skipped  
-- Detecting CXX compile features  
-- Detecting CXX compile features - done  
-- Found MPI_CXX: /usr/lib/openmpi/libmpi_cxx.so (found version "3.1")  
-- Found MPI: TRUE (found version "3.1")  
-- Found Boost: /usr/lib64/cmake/Boost-1.76.0/BoostConfig.cmake (found version "1.76.0") found components: mpi  
-- Configuring done  
-- Generating done  
-- Build files have been written to: /tmp/test-boost-mpi/build  
  
$ cmake --build .  
[1/4] /usr/bin/c++   -g -MD -MT CMakeFiles/openmpi-example.dir/main.cpp.o -MF CMakeFiles/openmpi-example.dir/main.cpp.o.d -o CMakeFiles/openmpi-example.dir/main.cpp.o -c /tmp/test-boost-mpi/main.cpp  
[2/4] : && /usr/bin/c++ -g  CMakeFiles/openmpi-example.dir/main.cpp.o -o openmpi-example  -Wl,-rpath,/usr/lib/openmpi  /usr/lib/openmpi/libmpi_cxx.so  /usr/lib/openmpi/libmpi.so && :  
[3/4] /usr/bin/c++ -DBOOST_ALL_NO_LIB -DBOOST_MPI_DYN_LINK -DBOOST_SERIALIZATION_DYN_LINK  -g -pthread -MD -MT CMakeFiles/boost-example.dir/main-boost.cpp.o -MF CMakeFiles/boost-example.dir/main-boost.cpp.o.d -o CMakeFiles/boost-example.dir/main-boost.cpp.o -c /tmp/test-boost-mpi/main-boost.cpp  
[4/4] : && /usr/bin/c++ -g -Wl,-rpath -Wl,/usr/lib/openmpi -Wl,--enable-new-dtags -pthread CMakeFiles/boost-example.dir/main-boost.cpp.o -o boost-example  -Wl,-rpath,/usr/lib/openmpi  /usr/lib/libboost_mpi.so.1.76.0  /usr/lib/libboost_serialization.so.1.76.0  /usr/lib/openmpi/libmpi_cxx.so  /usr/lib/openmpi/libmpi.so && :  
  
$ mpirun -n 5 ./openmpi-example  
Process 0 local_data = 0 1  
Process 1 local_data = 2 3  
Process 2 local_data = 4 5  
Process 4 local_data = 8 9  
Process 3 local_data = 6 7  
  
$ mpirun -n 5 ./boost-example  
boost-example: /usr/include/boost/mpi/collectives/scatterv.hpp:32: void boost::mpi::detail::scatterv_impl(const boost::mpi::communicator&, const T*, T*, int, const int*, const int*, int, mpl_::true_) [with  
 T = int; mpl_::true_ = mpl_::bool_<true>]: Assertion `bool(sizes) == bool(in_values)' failed.  
*** Process received signal ***  
Signal: Aborted (6)  
Signal code:  (-6)  
...  
(call stack truncated)  
  
```  
  
Both release versions run as expected:  
  
```  
$ cmake -GNinja .. -DCMAKE_BUILD_TYPE=Release -DCMAKE_VERBOSE_MAKEFILE=1  
$ cmake --build .  
[1/4] /usr/bin/c++   -O3 -DNDEBUG -MD -MT CMakeFiles/openmpi-example.dir/main.cpp.o -MF CMakeFiles/openmpi-example.dir/main.cpp.o.d -o CMakeFiles/openmpi-example.dir/main.cpp.o -c /tmp/test-boost-mpi/main.cpp  
[2/4] : && /usr/bin/c++ -O3 -DNDEBUG  CMakeFiles/openmpi-example.dir/main.cpp.o -o openmpi-example  -Wl,-rpath,/usr/lib/openmpi  /usr/lib/openmpi/libmpi_cxx.so  /usr/lib/openmpi/libmpi.so && :  
[3/4] /usr/bin/c++ -DBOOST_ALL_NO_LIB -DBOOST_MPI_DYN_LINK -DBOOST_SERIALIZATION_DYN_LINK  -O3 -DNDEBUG -pthread -MD -MT CMakeFiles/boost-example.dir/main-boost.cpp.o -MF CMakeFiles/boost-example.dir/main-boost.cpp.o.d -o CMakeFiles/boost-example.dir/main-boost.cpp.o -c /tmp/test-boost-mpi/main-boost.cpp  
[4/4] : && /usr/bin/c++ -O3 -DNDEBUG -Wl,-rpath -Wl,/usr/lib/openmpi -Wl,--enable-new-dtags -pthread CMakeFiles/boost-example.dir/main-boost.cpp.o -o boost-example  -Wl,-rpath,/usr/lib/openmpi  /usr/lib/libboost_mpi.so.1.76.0  /usr/lib/libboost_serialization.so.1.76.0  /usr/lib/openmpi/libmpi_cxx.so  /usr/lib/openmpi/libmpi.so && :  
  
```  
  
(Mpirun output very similar to the above one.)

---

## Comment 1

> Username: hirschsn  
> Created at: 2022-12-30 21:20:49 UTC  
> Updated at: 2022-12-30 21:21:34 UTC  
> Url: https://github.com/boostorg/mpi/issues/138#issuecomment-1368093954  

Strictly speaking, the MPI standard seems to allow the user to provide no sendbuf but sendcounts on non-root processes.  
That's also the issue where the assert comes from. For a quick fix in your application: Pass a nullptr as senscounts on non-root processes.  
I guess this assert is nice to catch some unintended calls but as we see here also causes false-positives.

---

## Comment 2

> Username: vehlwn  
> Created at: 2022-12-31 14:53:05 UTC  
> Url: https://github.com/boostorg/mpi/issues/138#issuecomment-1368225565  

Do you think this is not a Boost.MPI's problem and the issue can be closed because it won't be fixed?

---

## Comment 3

> Username: hirschsn  
> Created at: 2022-12-31 15:22:39 UTC  
> Updated at: 2022-12-31 15:23:03 UTC  
> Url: https://github.com/boostorg/mpi/issues/138#issuecomment-1368241119  

I would actually propose to change the assertion from equality to implication, i.e.:  
replace `bool(sizes) == bool(in_values)` by `!bool(in_values) || bool(sizes)`  
Then your example program should not run into the assert anymore.
