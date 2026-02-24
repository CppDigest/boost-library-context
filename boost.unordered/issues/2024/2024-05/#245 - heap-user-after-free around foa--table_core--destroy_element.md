# #245 - heap-user-after-free around foa::table_core::destroy_element [Closed]

> Username: indiosmo  
> Created at: 2024-05-02 15:39:58 UTC  
> Updated at: 2024-05-02 20:31:00 UTC  
> Closed at: 2024-05-02 20:30:59 UTC  
> Url: https://github.com/boostorg/unordered/issues/245  

Hi,  
  
Running the attached `stress_test.cpp` under ASAN I get heap-use-after-free around `boost/unordered/detail/foa/core.hpp:1796`. This happens in 1.85 and 1.84 as well. It happens for both `unordered_flat_map` and `unordered_node_map`.  Using  `std::unordered_map` I get no errors.  
  
The full ASAN output is attached.  
[asan.log.26931.txt](https://github.com/boostorg/unordered/files/15190456/asan.log.26931.txt)  
  
I'm attaching the relevant headers and implementations from the application as I can't seem to reproduce the issue outside of my application (`thread_local_timer_wheel::schedule()` is where the crash happens).  
  
The `stress_test.cpp` file is the minimal code I could write that replicates the problem, but has lots of depencies on other internal code which I can't publish here.  
  
The `reproduction_attempt.cpp` file is my attempt to reproduce without the dependencies, but it seems to run ok.  
  
To summarize, there's a `static thread_local flat_unordered_map`.  
In the `thread_local_timer_wheel::schedule()` function I emplace into the map, and push a callback to a queue that is read by the clock thread.  
When the timer elapses, the clock thread calls the callback, which gets marshalled back to the original thread that called `schedule()`, and in that original thread, erases the entry from the map.  
  
Running under TSAN shows no warnings, and replacing the map with `std::unordered_map` also runs without issues.  
The offending read and write also both happen on the same thread as seen in the ASAN output, so it doesn't look like a concurrency problem on my part.  
  
[reproduction_attempt.cpp.txt](https://github.com/boostorg/unordered/files/15190611/reproduction_attempt.cpp.txt)  
[stress_test.cpp.txt](https://github.com/boostorg/unordered/files/15190612/stress_test.cpp.txt)  
[thread_local_timer_wheel.cpp.txt](https://github.com/boostorg/unordered/files/15190613/thread_local_timer_wheel.cpp.txt)  
[thread_local_timer_wheel.hpp.txt](https://github.com/boostorg/unordered/files/15190614/thread_local_timer_wheel.hpp.txt)

---

## Comment 1

> Username: cmazakas  
> Created at: 2024-05-02 17:44:51 UTC  
> Url: https://github.com/boostorg/unordered/issues/245#issuecomment-2091155325  

Before we go ahead and invest the time into looking into this, we're going to need at least a reliable reproduction of the issue in question.  
  
If you're using the unordered_flat_map and unordered_node_maps, any bugs should be reproducible in a single-threaded context, otherwise it indicates an ordering problem in the consuming application. You've mentioned tsan but there's also tools like valgrind's `helgrind` which you can try as well.  
  
It also helps us out a lot more if your reproduction is put inside a github repo or something else we can just view in-browser without having to individually download and open locally.

---

## Comment 2

> Username: indiosmo  
> Created at: 2024-05-02 19:18:08 UTC  
> Url: https://github.com/boostorg/unordered/issues/245#issuecomment-2091354519  

Hi Christian, I'll try coming up with a simple reproducible case and put it in a repo. As well as running under valgrind.  
  
In any case, ASAN shows that all 3 of allocation, read and free happened on thread T2, so doesn't that indicate that it can't be a concurrency problem?

---

## Comment 3

> Username: pdimov  
> Created at: 2024-05-02 19:34:34 UTC  
> Url: https://github.com/boostorg/unordered/issues/245#issuecomment-2091405164  

One way to test this assumption is to run the program, limiting it to a single logical core (using process affinity).

---

## Comment 4

> Username: indiosmo  
> Created at: 2024-05-02 19:41:11 UTC  
> Url: https://github.com/boostorg/unordered/issues/245#issuecomment-2091417292  

> One way to test this assumption is to run the program, limiting it to a single logical core (using process affinity).  
  
Doing that right now.

---

## Comment 5

> Username: indiosmo  
> Created at: 2024-05-02 19:48:07 UTC  
> Url: https://github.com/boostorg/unordered/issues/245#issuecomment-2091433434  

It does happen under a single thread, I'm pasting the code below for now and I'll try and set up a repo without having my internal dependencies.  
  
```cpp  
#include "boost/unordered/unordered_flat_map.hpp"  
#include "tbb/concurrent_queue.h"  
#include <csignal>  
#include "mil/timer.hpp"  
#include "mil/timer/timer.hpp"  
#include <random>  
#include "mil/result.hpp"  
#include "mil/fmt.hpp"  
#include "mil/log.hpp"  
  
struct driver  
{  
  driver()  
  {  
    for (int i = 0; i < 250; ++i) {  
      timers.push_back(std::make_unique<mil::timer::timer>());  
    }  
  }  
  
  void start()  
  {  
    timer.expires_after(std::chrono::microseconds{1}, [this] { elapsed(); }, mil::timer::timer::mode_enum::repeating);  
  }  
  
  void stop()  
  {  
    timer.cancel();  
    MIL_LOG_INFO("ops {} | elapsed {}", op_counter_, elapsed_counter_);  
  }  
  
  void elapsed()  
  {  
    // MIL_LOG_INFO("elasped {}", std::this_thread::get_id());  
    auto index = timer_dist(gen);  
    auto interval = std::chrono::milliseconds(interval_dist(gen));  
    bool op_start = index % 2 == 0;  
    ++op_counter_;  
  
    if (op_start) {  
      timers.at(index)->expires_after(interval, [this] {  
        // MIL_LOG_INFO("inner elapsed {}", std::this_thread::get_id());  
        ++elapsed_counter_;  
      });  
    } else {  
      // MIL_LOG_INFO("cancel {}", std::this_thread::get_id());  
      timers.at(index)->cancel();  
    }  
  }  
  
  mil::timer::timer timer;  
  
  std::random_device rd{};  
  std::mt19937 gen{rd()};  
  std::uniform_int_distribution<int> timer_dist{0, 249};  
  std::uniform_int_distribution<int> interval_dist{1, 60000};  
  
  std::vector<std::unique_ptr<mil::timer::timer>> timers;  
  std::uint64_t op_counter_ = 0;  
  std::uint64_t elapsed_counter_ = 0;  
};  
  
std::atomic<bool> running_ = true;  
  
void signal_handler(int /* signal */)  
{  
  running_ = false;  
}  
  
int main()  
{  
  mil::timer::context_tag = mil::timer::thread_local_timer_wheel_tag{};  
  auto& timer_wheel = mil::timer::context_config<mil::timer::thread_local_timer_wheel_tag>;  
  MIL_EXIT_ON_ERROR(timer_wheel.set_tick(std::chrono::microseconds{1}));  
  
  driver d{};  
  d.start();  
  
  while(running_) {  
    timer_wheel.poll_clock_thread();  
    timer_wheel.poll_local_thread();  
  }  
  
  d.stop();  
  
}  
```

---

## Comment 6

> Username: pdimov  
> Created at: 2024-05-02 20:06:11 UTC  
> Url: https://github.com/boostorg/unordered/issues/245#issuecomment-2091463414  

```  
void thread_local_timer_wheel::schedule(operation_token token, types::tick_duration delta, types::elapsed_callback_t callback)  
{  
  operations_.try_emplace(token.operation_id(), callback); // line 49 frees  
  
  auto marshalled = [callback_queue = from_wheel_](types::operation_id operation_id) {  
    callback_queue->push([operation_id] {  
      if (auto it = operations_.find(operation_id); it != std::end(operations_)) {  
        it->second.callback(operation_id); // line 54  
        operations_.erase(it); // line 55 uses after free  
      }  
    });  
  };  
  
  to_wheel_.emplace([=] { wheel_.schedule(token, delta, marshalled); });  
}  
```  
What this ASAN log says to me is that the callback invocation in line 54 calls `schedule`, which causes a reallocation in line 49, so when execution continues to line 55, the original bucket array is no longer there.  
  
That's of course just a guess, but I'm not sure what else could explain it.  
  
If that's true, the fix should be something like changing  
```  
        it->second.callback(operation_id);  
        operations_.erase(it);  
```  
to  
```  
    auto callback = it->second.callback;  
    operations_.erase(it);  
    callback(operation_id);  
```

---

## Comment 7

> Username: indiosmo  
> Created at: 2024-05-02 20:30:59 UTC  
> Url: https://github.com/boostorg/unordered/issues/245#issuecomment-2091502702  

I was just putting up the reproduction repo (https://github.com/indiosmo/unordered_flat_repro) when I saw your response.  
  
`schedule()` is reentrant so, indeed, even in a single thread the sequence can be lines 54 -> 49 -> 55 and the iterator gets invalidated.  
  
Your proposed change fixes it. We could also do `operations_.erase(operation_id)` and it also works.  
  
Thank you.
