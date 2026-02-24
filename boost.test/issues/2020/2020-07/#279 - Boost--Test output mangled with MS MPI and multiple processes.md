# #279 - Boost::Test output mangled with MS MPI and multiple processes [Closed]

> Username: wperkins  
> Created at: 2020-07-10 16:39:24 UTC  
> Updated at: 2020-07-10 18:17:55 UTC  
> Closed at: 2020-07-10 18:17:55 UTC  
> Url: https://github.com/boostorg/test/issues/279  

I'm having trouble with MPI-based unit tests that use more than 1 process _on Windows._  MacOS and Linux give me no problems. A complete example, with output, is in  [boost-test-mpi.zip](https://github.com/boostorg/test/files/4904301/boost-test-mpi.zip).  
  
In short, with _Windows MPI_, the phrase `No errors detected` appears to be output one character per line when multiple processes are used. This is inconvenient because I use the `No errors detected` phrase to check for test success.   
  
I have a very simple unit test that I use as a pattern for others:  
```C++  
#include <boost/mpi/environment.hpp>  
#include <boost/mpi/communicator.hpp>  
#include <boost/mpi/collectives.hpp>  
#define BOOST_TEST_NO_MAIN  
#define BOOST_TEST_ALTERNATIVE_INIT_API  
#include <boost/test/included/unit_test.hpp>  
  
BOOST_AUTO_TEST_SUITE ( test )  
  
BOOST_AUTO_TEST_CASE( test )  
{  
  boost::mpi::communicator world;  
  int i(1), sumi(0);  
  boost::mpi::all_reduce(world, i, sumi, std::plus<int>());  
  BOOST_CHECK_EQUAL(sumi, static_cast<int>(world.size()));  
}  
  
BOOST_AUTO_TEST_SUITE_END( )  
  
bool init_function()  
{  
  return true;  
}  
  
int  
main(int argc, char **argv)  
{  
  boost::mpi::environment env(argc, argv);  
  return ::boost::unit_test::unit_test_main( &init_function, argc, argv );  
}  
```  
When run with one process, output to the terminal is fine:  
```  
mpiexec -n 1 Release/mpi_test.exe  
Running 1 test case...  
  
*** No errors detected  
```  
However, if more than one process is used, output is mangled:  
```  
mpiexec -np 2 Release/mpi_test.exe  
Running 1 test case...  
Running 1 test case...  
  
  
*  
*  
*  
*  
*  
*  
  
  
N  
N  
o  
o  
  
  
e  
e  
r  
r  
r  
r  
o  
o  
r  
r  
s  
s  
  
  
d  
d  
e  
e  
t  
t  
e  
e  
c  
c  
t  
t  
e  
e  
d  
d  
  
```  
  
Other messages from Boost::Test do not seem to be affected, e.g.:  
```  
mpiexec -np 2 Release/mpi_test.exe --log_level=all  
Running 1 test case...  
Entering test module "Master Test Suite"  
C:\Users\D3G096\gridpack\src\sandbox\boost-test-mpi\mpi_test.cpp(23): Entering test suite "test"  
C:\Users\D3G096\gridpack\src\sandbox\boost-test-mpi\mpi_test.cpp(25): Entering test case "test"  
Running 1 test case...  
Entering test module "Master Test Suite"  
C:\Users\D3G096\gridpack\src\sandbox\boost-test-mpi\mpi_test.cpp(23): Entering test suite "test"  
C:\Users\D3G096\gridpack\src\sandbox\boost-test-mpi\mpi_test.cpp(25): Entering test case "test"  
C:/Users/D3G096/gridpack/src/sandbox/boost-test-mpi/mpi_test.cpp(30): info: check sumi == static_cast<int>(world.size()) has passed  
C:/Users/D3G096/gridpack/src/sandbox/boost-test-mpi/mpi_test.cpp(30): info: check sumi == static_cast<int>(world.size()) has passed  
C:\Users\D3G096\gridpack\src\sandbox\boost-test-mpi\mpi_test.cpp(25): Leaving test case "test"; testing time: 541us  
C:\Users\D3G096\gridpack\src\sandbox\boost-test-mpi\mpi_test.cpp(25): Leaving test case "test"; testing time: 446us  
C:\Users\D3G096\gridpack\src\sandbox\boost-test-mpi\mpi_test.cpp(23): Leaving test suite "test"; testing time: 651us  
C:\Users\D3G096\gridpack\src\sandbox\boost-test-mpi\mpi_test.cpp(23): Leaving test suite "test"; testing time: 611us  
Leaving test module "Master Test Suite"; testing time: 788us  
  
Leaving test module "Master Test Suite"; testing time: 707us  
  
*  
*  
*  
*  
*  
*  
  
  
N  
N  
o  
o  
  
  
e  
e  
r  
r  
r  
r  
o  
o  
r  
r  
s  
s  
  
  
d  
d  
e  
e  
t  
t  
e  
e  
c  
c  
t  
t  
e  
e  
d  
d  
  
```  
System description:  
- Windows Server 2012 R2   
- most recent MSMPI   
- MSVC 2015  
- Boost 1.73  
  
I hope I'm not just doing something stupid.  Any help would be appreciated.  
Thanks.

---

## Comment 1

> Username: wperkins  
> Created at: 2020-07-10 18:17:54 UTC  
> Url: https://github.com/boostorg/test/issues/279#issuecomment-656815529  

Nevermind. I think it's just the difference between stdout and stderr.  So, not a Boost problem.
