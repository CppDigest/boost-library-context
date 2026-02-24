# #531 - EOF to stdin when launching process asynchronously on macOS [Open]

> Username: atraastrum  
> Created at: 2025-12-15 19:09:20 UTC  
> Updated at: 2025-12-17 12:19:28 UTC  
> Url: https://github.com/boostorg/process/issues/531  

On macOS 15.6.1 M3 the program below exits due to EOF flag being raised on `std::cin` after subprocess finishes.   
```cpp  
#include <boost/process.hpp>  
#include <boost/asio.hpp>  
#include <cstdio>  
#include <string>  
#include <iostream>  
#include <future>  
#include <vector>  
  
namespace asio = boost::asio;  
namespace bp = boost::process::v2;  
  
void runCommand(const std::string &cmd);  
  
int main(int argc, char *argv[]) {  
  std::vector<std::future<void>> tasks;  
  std::string line;  
  while(true) {  
    if (!std::getline(std::cin, line)) {  
      if (std::cin.eof()) {  
        break;  
      }  
      std::printf("Error reading input?\n");  
      std::cin.clear();  
      continue;  
    }  
  
    if (line == "r") {  
      tasks.push_back(std::async(std::launch::async, []() {  
        runCommand("/bin/ls");  
      }));  
    }  
  }  
  
  return 0;  
}  
  
void runCommand(const std::string &cmd) {  
  try {  
    std::printf("Running subprocess: %s\n", cmd.c_str());  
    asio::io_context ctx;  
    bp::process subProcess(ctx, cmd, {}, bp::process_stdio{nullptr});  
    subProcess.wait();  
  
    std::printf("Finished running subprocess with exit code %d\n", subProcess.exit_code());  
  } catch (const std::exception &ex) {  
    std::printf("Error running subprocess: %s\n", ex.what());  
  }  
}  
```  
  
Compiler   
```  
Apple clang version 17.0.0 (clang-1700.0.13.5)  
Target: arm64-apple-darwin24.6.0  
Thread model: posix  
InstalledDir: /Library/Developer/CommandLineTools/usr/bin  
```   
  
Boos version (from vcpkg)  
```  
1.89.0  
```  
  
Example run:  
```  
boost-process git:(cpp-boost-process) ✗ ./build/main  
r  
Running subprocess: /bin/ls  
CMakeLists.txt	README.md	build		main.cpp	vcpkg.json	vcpkg_installed  
Finished running subprocess with exit code 0  
```

---

## Comment 1

> Username: klemens-morgenstern  
> Created at: 2025-12-16 09:03:23 UTC  
> Url: https://github.com/boostorg/process/issues/531#issuecomment-3659500136  

What do you expect to read from std::cin ?

---

## Comment 2

> Username: atraastrum  
> Created at: 2025-12-16 18:19:17 UTC  
> Url: https://github.com/boostorg/process/issues/531#issuecomment-3661825831  

> What do you expect to read from std::cin ?  
  
I expect not to get EOF after reading `r\n` from stdin and running the command. The program should continue waiting for another line instead of exiting.

---

## Comment 3

> Username: klemens-morgenstern  
> Created at: 2025-12-17 00:37:05 UTC  
> Url: https://github.com/boostorg/process/issues/531#issuecomment-3663056250  

Well, you're reading from the parent processes `std::cin` and redirecting the child process's `stdout` to the null device.  
  
Every process has it's own set of stdio handles, so you're using the wrong one.

---

## Comment 4

> Username: atraastrum  
> Created at: 2025-12-17 05:48:31 UTC  
> Url: https://github.com/boostorg/process/issues/531#issuecomment-3663764050  

You probably meant redirecting `stdin` to null device.  
  
I am reading command from stdin of parent process e.g. command r to run the `ls` as child process. Yes correct.  
  
 I don't actually care for stdin of child process. I am redirecting it null device just to make sure nothing comes from child process to main process.   
  
https://github.com/user-attachments/assets/2433cff1-a40e-4394-8a27-ec78b82bfb82

---

## Comment 5

> Username: klemens-morgenstern  
> Created at: 2025-12-17 09:53:30 UTC  
> Url: https://github.com/boostorg/process/issues/531#issuecomment-3664566768  

OK, so why is this a question related to boost.process?

---

## Comment 6

> Username: atraastrum  
> Created at: 2025-12-17 11:21:05 UTC  
> Url: https://github.com/boostorg/process/issues/531#issuecomment-3664896093  

Using boost.process to run any child process using `std::async` with async launcher on macOS results in writing EOF to stdin.   
  
This does not happen on Linux or Windows.  
  
This might be a bug in boost.process.

---

## Comment 7

> Username: klemens-morgenstern  
> Created at: 2025-12-17 11:49:24 UTC  
> Url: https://github.com/boostorg/process/issues/531#issuecomment-3664987943  

It might be, even though I don't see how. What happens if you don't redirect the child's `stdin` to `null`?

---

## Comment 8

> Username: atraastrum  
> Created at: 2025-12-17 12:05:44 UTC  
> Updated at: 2025-12-17 12:05:55 UTC  
> Url: https://github.com/boostorg/process/issues/531#issuecomment-3665044960  

> What happens if you don't redirect the child's stdin to null?  
  
Nothing changes. EOF is written to stdin.  
  
If child process is launched from main thread no EOF is written and program behaves as expected:  
```diff  
-      tasks.push_back(std::async(std::launch::async, []() {  
-        runCommand("/bin/ls");  
-      }));  
+      runCommand("/bin/ls");  
     }  
   }  
```

---

## Comment 9

> Username: klemens-morgenstern  
> Created at: 2025-12-17 12:12:06 UTC  
> Url: https://github.com/boostorg/process/issues/531#issuecomment-3665067649  

That is odd, it could be asio related. Are you sure the program does exit properly, i.e. reach the line with `break`?

---

## Comment 10

> Username: atraastrum  
> Created at: 2025-12-17 12:19:27 UTC  
> Url: https://github.com/boostorg/process/issues/531#issuecomment-3665096662  

Yes it does  
  
https://github.com/user-attachments/assets/2567e9af-30f7-418b-82b4-0b7df3f020c2
