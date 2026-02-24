# #293 - child::wait_for switches process context when being used with boost.fiber and musl [Open]

> Username: AFriendlyGuy  
> Created at: 2023-01-06 10:26:16 UTC  
> Updated at: 2023-01-06 10:28:32 UTC  
> Url: https://github.com/boostorg/process/issues/293  

Here is minimal test case:  
It starts a process (`sleep 3` (seconds) as a shell command) in a second fiber and increments a counter in the main fiber every second. Furthermore, it prints process id and counter each time, the counter is incremented.  
```c++  
#include <boost/fiber/fiber.hpp>  
#include <boost/fiber/operations.hpp>  
#include <boost/process.hpp>  
  
using namespace std::chrono_literals;  
  
static void startProcess() {  
  boost::this_fiber::sleep_for(3s);  
  std::cout << "starting child process" << std::endl;  
  boost::process::child childProcess("/bin/sh -c \"sleep 3\"");  
  boost::this_fiber::yield();  
  while(!childProcess.wait_for(100ms)) {  
    boost::this_fiber::yield();  
  }  
//  while(childProcess.running()) { // this alternative works as expected  
//    boost::this_fiber::sleep_for(100ms);  
//  }  
//  childProcess.wait();  
  std::cout << "child process finished" << std::endl;  
}  
  
int main() {  
  boost::fibers::fiber startProcessFiber(startProcess);  
  for(auto i =0; i< 9; ++i) {  
    std::cout << boost::this_process::get_id() << " " << i << std::endl;  
    boost::this_fiber::sleep_for(1s);  
  }  
  startProcessFiber.join();  
}  
```  
This is the output when run on an Alpine 17.0 docker image:  
```  
1 0  
1 1  
1 2  
1 3  
starting child process  
3 4  
3 5  
3 6  
child process finished  
1 4  
1 5  
1 6  
1 7  
1 8  
```  
This is the expected output, which I get when using `childProcess.running()` instead of `childProcess.wait_for()` combined with ` boost::this_fiber::sleep_for()`:   
```  
1 0  
1 1  
1 2  
starting child process  
1 3  
1 4  
1 5  
1 6  
child process finished  
1 7  
1 8  
```  
`childProcess.wait_for()` worked when I used an opensuse 15.4 docker image (with gcc 11). OpenSuse uses glibc whereas Alpine uses musl for the  C standard library implementation. So I assume, the behaviour is related to this. The issues also occures on Alpine 16.3, which uses gcc 11, so it should not depend on the fact, that Alpine 17.0 uses gcc 12.  
  
This kind of process context switch was so totally unexpected for me, it took me ages to boil it down to the test case above.
