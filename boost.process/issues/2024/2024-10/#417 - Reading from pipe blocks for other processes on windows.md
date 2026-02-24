# #417 - Reading from pipe blocks for other processes on windows [Open]

> Username: guusw  
> Created at: 2024-10-29 13:19:11 UTC  
> Updated at: 2024-10-30 08:56:23 UTC  
> Url: https://github.com/boostorg/process/issues/417  

Hey, maybe I'm doing something wrong but I can't get the pipes to read correctly with the following example:  
  
The output is something like this:  
```log  
[2024-10-29 21:16:11.569] [info] [main.cpp:41] Started both processed  
[2024-10-29 21:16:11.584] [info] [main.cpp:18] Finished process: echo "hello" (0)  
[2024-10-29 21:16:11.584] [info] [main.cpp:18] Finished process: false (1)  
[2024-10-29 21:16:21.593] [info] [main.cpp:29] Output:   
[2024-10-29 21:16:21.593] [info] [main.cpp:29] Output: hello  
[2024-10-29 21:16:21.596] [info] [main.cpp:18] Finished process: sleep 10.0 (0)  
[2024-10-29 21:16:21.596] [info] [main.cpp:29] Output:   
[2024-10-29 21:16:21.597] [info] [main.cpp:47] Both processes finished successfully  
```  
NOTE: that the `output:` is only printent after ALL processes finish, which I don't understand because the pipe should've been closed when each individual process ends. I used boost process 1.86 (latest)  
  
```cpp  
#include <boost/process.hpp>  
#include <future>  
#include <spdlog/spdlog.h>  
  
namespace bp {  
using namespace boost::process;  
}  
  
int main(int argc, char **argv) {  
  try {  
    auto run_child = [](const std::string &command) {  
      try {  
        bp::ipstream pipe_stream;  
        {  
          // Redirect stdout to our pipe  
          bp::child c(command, bp::std_out > pipe_stream);  
          c.wait();  
          SPDLOG_INFO("Finished process: {} ({})", command, c.exit_code());  
        } // child destructor runs here, closing its end of the pipe  
  
        // Read the entire output from the pipe  
        std::string output;  
        std::string line;  
        while (std::getline(pipe_stream, line)) {  
          if (!output.empty()) output += "\n";  
          output += line;  
        }  
  
        SPDLOG_INFO("Output: {}", output);  
        return true;  
      } catch (const std::exception &e) {  
        SPDLOG_ERROR("Exception in child: {}", e.what());  
        return false;  
      }  
    };  
  
    auto future0 = std::async(std::launch::async, run_child, "sleep 10.0");  
    auto future1 = std::async(std::launch::async, run_child, "false");  
    auto future2 = std::async(std::launch::async, run_child, "echo \"hello\"");  
  
    SPDLOG_INFO("Started both processed");  
  
    bool result0 = future0.get();  
    bool result1 = future1.get();  
  
    if (result0 && result1) {  
      SPDLOG_INFO("Both processes finished successfully");  
    } else {  
      SPDLOG_ERROR("One or both processes failed");  
    }  
  
  } catch (std::exception &e) {  
    SPDLOG_ERROR("Exception: {}", e.what());  
  }  
  return 0;  
}  
```

---

## Comment 1

> Username: klemens-morgenstern  
> Created at: 2024-10-29 15:26:43 UTC  
> Url: https://github.com/boostorg/process/issues/417#issuecomment-2444602521  

I don't understand what you're expecting. The pipe's have a 4k (usually) buffer, that will get drained before you get an EOF.

---

## Comment 2

> Username: guusw  
> Created at: 2024-10-30 07:15:11 UTC  
> Url: https://github.com/boostorg/process/issues/417#issuecomment-2446044055  

These are 3 individual processes, I expect the individual pipes to close when their associated process ends, but this only seems to happen after all 3 have ended. Where all the pipes are magically interlinked and unblock once the last process ends.  
Note the log timestamps where the "output" lines are delayed

---

## Comment 3

> Username: klemens-morgenstern  
> Created at: 2024-10-30 08:56:23 UTC  
> Url: https://github.com/boostorg/process/issues/417#issuecomment-2446229482  

Oh right, you open the pipes first. All processes inherit the pipes by default. You need to pass `limit_handles()` or better yet switch to `process::v2`, which does that automatically.
