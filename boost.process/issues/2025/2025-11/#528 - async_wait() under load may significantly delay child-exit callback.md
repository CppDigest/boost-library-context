# #528 - [v2][Linux] async_wait() under load may significantly delay child-exit callback [Open]

> Username: Zeratul-Aiur  
> Created at: 2025-11-26 11:03:05 UTC  
> Updated at: 2025-12-22 04:31:52 UTC  
> Url: https://github.com/boostorg/process/issues/528  

Hi,  
  
When using Boost.Process v2 + Boost.Asio on Linux (with `pidfd_open` support), in a design where we simultaneously asynchronously monitor child‑process exit (via `async_wait()`) and child stderr (or stdout) pipes (via `async_read_until()`), we observed that child‑exit notifications (i.e. `async_wait()` handlers) are sometimes delayed by several seconds — even though the child process has already exited and pipe EOF / closure has occurred, and no heavy work remains.  
  
This breaks the assumption that `async_wait()` will “soon” notify after the process terminates, and makes logic relying on “immediate reaction to child death” (for example: killing other sibling processes) unreliable and flaky under some conditions (especially under load or with multiple concurrent I/O / processes).  
  
It works correctly most of the time — only occasionally does a problem occur. I haven’t run into any issues on Windows so far. I want to know whether this is caused by a load issue, or it's a problem with my implementation. Here’s my relevant implementation.  
  
```cpp  
namespace {  
  
// NOLINTNEXTLINE(misc-no-recursion)  
void read_pipe(uint32_t pid,  
               net::readable_pipe &pipe,  
               net::streambuf &buffer,  
               std::function<void(uint32_t, const std::string &)> &call_back,  
               std::string name) {  
  net::async_read_until(  
      pipe,  
      buffer,  
      '\n',  
      // NOLINTNEXTLINE(misc-no-recursion)  
      [pid, &pipe, &buffer, &call_back, pipe_name = std::move(name)](  
          const bp::error_code &error_code, size_t /*bytes*/) {  
        if (error_code.failed()) {  
#ifdef _WIN32  
          if (error_code == net::error::broken_pipe)  
#elif __linux__  
          if (error_code == net::error::eof)  
#endif  
          {  
            std::istream is(&buffer);  
            std::string line;  
            if (std::getline(is, line)) {  
              call_back(pid, locale_to_utf8(line));  
            }  
  
            // NOLINTNEXTLINE(bugprone-lambda-function-name)  
            VFLOW_DEBUG("The remaining data in the {} pipe of the process with "  
                        "PID {} is {} bytes.",  
                        pipe_name,  
                        pid,  
                        line.size());  
            return;  
          }  
  
          // NOLINTNEXTLINE(bugprone-lambda-function-name)  
          VFLOW_ERROR("Failed to read the contents of the {} pipe of the "  
                      "process with PID {}. Error message: {}",  
                      pipe_name,  
                      pid,  
                      locale_to_utf8(error_code.message()));  
          return;  
        }  
  
        std::istream is(&buffer);  
        std::string line;  
        std::getline(is, line);  
  
#ifdef _WIN32  
        if (!line.empty() && line.back() == '\r') {  
          line.pop_back();  
        }  
#endif  
  
        call_back(pid, locale_to_utf8(line));  
        // NOLINTNEXTLINE(misc-no-recursion)  
        read_pipe(pid, pipe, buffer, call_back, pipe_name);  
      });  
}  
  
} // namespace  
  
uint32_t ProcessMonitor::start() {  
  ...  
  
  if (stdout_callback_) {  
#ifdef _WIN32  
    process_.emplace(ioc_,  
                     utf8_to_locale(exe_),  
                     args_,  
                     bp::process_environment(process_env),  
                     bp::process_stdio{nullptr, stdout_pipe_, stderr_pipe_});  
#elif __linux__  
    bp::process_stdio process_stdio{nullptr, stdout_pipe_, stderr_pipe_};  
  
    VFLOW_DEBUG("stdin FD: {}, stdout FD: {}, stderr FD: {}",  
                process_stdio.in.fd,  
                process_stdio.out.fd,  
                process_stdio.err.fd);  
  
    process_.emplace(ioc_,  
                     utf8_to_locale(exe_),  
                     args_,  
                     bp::process_environment(process_env),  
                     std::move(process_stdio));  
#endif  
    read_pipe(process_->id(),  
              stdout_pipe_,  
              stdout_buffer_,  
              stdout_callback_,  
              "stdout");  
  } else {  
#ifdef _WIN32  
    process_.emplace(ioc_,  
                     utf8_to_locale(exe_),  
                     args_,  
                     bp::process_environment(process_env),  
                     bp::process_stdio{nullptr, nullptr, stderr_pipe_});  
#elif __linux__  
    bp::process_stdio process_stdio{nullptr, nullptr, stderr_pipe_};  
  
    VFLOW_DEBUG("stdin FD: {}, stdout FD: {}, stderr FD: {}",  
                process_stdio.in.fd,  
                process_stdio.out.fd,  
                process_stdio.err.fd);  
  
    process_.emplace(ioc_,  
                     utf8_to_locale(exe_),  
                     args_,  
                     bp::process_environment(process_env),  
                     std::move(process_stdio));  
#endif  
  }  
  
  read_pipe(  
      process_->id(), stderr_pipe_, stderr_buffer_, stderr_callback_, "stderr");  
  
  process_->async_wait([this](const bp::error_code &error_code, int exit_code) {  
#ifdef _WIN32  
    if (error_code.failed())  
#elif __linux__  
    if (error_code.failed() && error_code.value() != ECHILD)  
#endif  
    {  
      // NOLINTNEXTLINE(bugprone-lambda-function-name)  
      VFLOW_ERROR("The process with PID {} exited with an error message: {}",  
                  process_->id(),  
                  locale_to_utf8(error_code.message()));  
    }  
  
    exit_callback_(process_->id(), exit_code);  
  });  
  
  return process_->id();  
}  
```  
  
```cpp  
void MultiProcessManager::start_all() {  
  if (impl_->processes_.empty())  
    return;  
  
  if (impl_->state_ == Impl::State::kStopped) {  
    impl_->ioc_.restart();  
    impl_->state_ = Impl::State::kInitialized;  
  }  
  
  for (size_t i = 0; i < impl_->processes_.size(); ++i) {  
    impl_->pid_[impl_->processes_.at(i)->start()] = i;  
  }  
  
  impl_->thread_ = std::thread([this] {  
    boost::system::error_code error_code;  
    impl_->ioc_.run(error_code);  
    if (error_code.failed()) {  
      // NOLINTNEXTLINE(bugprone-lambda-function-name)  
      VFLOW_ERROR("IO context stopped with error: {}",  
                  locale_to_utf8(error_code.message()));  
    }  
  });  
  
  impl_->state_ = Impl::State::kRunning;  
  
  if (impl_->ioc_.stopped()) {  
    stop_all();  
  
    SPDLOG_ERROR_AND_THROW(excepts::ProcessError,  
                           "MultiProcessManager failed to start.");  
  }  
  
  for (size_t i = 0; i < impl_->processes_.size(); ++i) {  
    VFLOW_INFO("The executable path of process {} is {}, and its PID is {}.",  
               i,  
               impl_->processes_[i]->exe(),  
               impl_->processes_[i]->pid());  
  }  
}  
  
void MultiProcessManager::on_read_stdout(uint32_t pid, const std::string &log) {  
  VFLOW_INFO("Process {}: {}", impl_->pid_[pid], log);  
}  
  
void MultiProcessManager::on_read_stderr(uint32_t pid,  
                                         const std::string &exception) {  
  impl_->exception_[pid] += exception;  
}  
  
void MultiProcessManager::on_handle_exit(uint32_t pid, int exit_code) {  
  VFLOW_INFO(  
      "Process {} has exited with exit code {}.", impl_->pid_[pid], exit_code);  
  
  if (exit_code != 0) {  
    if (!impl_->exit_failtrue) {  
      impl_->exit_failtrue = true;  
      stop_all();  
    }  
  }  
}  
```  
  
Here’s my tests.  
  
```cpp  
    if (strcmp(argv[1], "return_1_after_1s") == 0) {  
      std::this_thread::sleep_for(std::chrono::seconds(1));  
  
      return 1;  
    }  
  
    if (strcmp(argv[1], "get_argv_from_stderr_and_wait_2s") == 0) {  
      for (int i = 2; i < argc; ++i) {  
        std::cerr << argv[i] << '\n';  
      }  
  
      std::this_thread::sleep_for(std::chrono::seconds(2));  
  
      return 1;  
    }  
  
    if (strcmp(argv[1], "get_argv_from_stderr_after_2s") == 0) {  
      std::this_thread::sleep_for(std::chrono::seconds(2));  
  
      for (int i = 2; i < argc; ++i) {  
        std::cerr << argv[i] << '\n';  
      }  
  
      return 1;  
    }  
```  
  
```cpp  
    util::MultiProcessManager multi_process_manager;  
    multi_process_manager.add_process(exe, {"return_1_after_1s"}, {}, false);  
  
    std::string argv_wait_2s;  
    auto wait_2s_callback = [&argv_wait_2s](uint32_t /*pid*/,  
                                            const std::string &exception) {  
      argv_wait_2s += exception;  
    };  
    multi_process_manager.add_process(  
        exe,  
        {"get_argv_from_stderr_and_wait_2s", "Only", "for", "test"},  
        {},  
        false,  
        {},  
        wait_2s_callback);  
  
    std::string argv_after_2s;  
    auto after_2s_callback = [&argv_after_2s](uint32_t /*pid*/,  
                                              const std::string &exception) {  
      argv_after_2s += exception;  
    };  
    multi_process_manager.add_process(  
        exe,  
        {"get_argv_from_stderr_after_2s", "Only", "for", "test"},  
        {},  
        false,  
        {},  
        after_2s_callback);  
  
    CHECK_NOTHROW(multi_process_manager.start_all());  
    CHECK_THROWS_AS(multi_process_manager.wait_all(), excepts::ProcessError);  
    CHECK(argv_wait_2s == "Onlyfortest");  
    CHECK(argv_after_2s.empty());  
```  
  
Correct:  
  
```cpp  
[vflow] [2025-11-25 08:15:00.312] [debug] [process_monitor.cpp:165] stdin FD: 7, stdout FD: 8, stderr FD: 10  
[vflow] [2025-11-25 08:15:00.320] [debug] [process_monitor.cpp:165] stdin FD: 7, stdout FD: 8, stderr FD: 12  
[vflow] [2025-11-25 08:15:00.340] [debug] [process_monitor.cpp:165] stdin FD: 7, stdout FD: 8, stderr FD: 14  
[vflow] [2025-11-25 08:15:00.358] [info] [multi_process_manager.cpp:138] The executable path of process 0 is /root/.cache/bazel/_bazel_root/1d5b955b5bfc5b7922b7d1a4d4feb60d/sandbox/linux-sandbox/884/execroot/_main/bazel-out/aarch64-fastbuild/bin/test/catch2_utils.runfiles/_main/test/utils/utils/process_example, and its PID is 69.  
[vflow] [2025-11-25 08:15:00.358] [info] [multi_process_manager.cpp:138] The executable path of process 1 is /root/.cache/bazel/_bazel_root/1d5b955b5bfc5b7922b7d1a4d4feb60d/sandbox/linux-sandbox/884/execroot/_main/bazel-out/aarch64-fastbuild/bin/test/catch2_utils.runfiles/_main/test/utils/utils/process_example, and its PID is 70.  
[vflow] [2025-11-25 08:15:00.358] [info] [multi_process_manager.cpp:138] The executable path of process 2 is /root/.cache/bazel/_bazel_root/1d5b955b5bfc5b7922b7d1a4d4feb60d/sandbox/linux-sandbox/884/execroot/_main/bazel-out/aarch64-fastbuild/bin/test/catch2_utils.runfiles/_main/test/utils/utils/process_example, and its PID is 71.  
[vflow] [2025-11-25 08:15:01.323] [debug] [process_monitor.cpp:44] The remaining data in the stderr pipe of the process with PID 69 is 0 bytes.  
[vflow] [2025-11-25 08:15:01.323] [info] [multi_process_manager.cpp:218] Process 0 has exited with exit code 1.  
[vflow] [2025-11-25 08:15:01.323] [debug] [process_monitor.cpp:225] The process with PID 70 is starting to terminate.  
[vflow] [2025-11-25 08:15:01.324] [debug] [process_monitor.cpp:246] The process with PID 70 has been terminated. Exit Code: 9  
[vflow] [2025-11-25 08:15:01.324] [debug] [process_monitor.cpp:225] The process with PID 71 is starting to terminate.  
[vflow] [2025-11-25 08:15:01.324] [debug] [process_monitor.cpp:246] The process with PID 71 has been terminated. Exit Code: 9  
[vflow] [2025-11-25 08:15:01.324] [debug] [process_monitor.cpp:44] The remaining data in the stderr pipe of the process with PID 70 is 0 bytes.  
[vflow] [2025-11-25 08:15:01.324] [info] [multi_process_manager.cpp:218] Process 1 has exited with exit code 0.  
[vflow] [2025-11-25 08:15:01.324] [debug] [process_monitor.cpp:44] The remaining data in the stderr pipe of the process with PID 71 is 0 bytes.  
[vflow] [2025-11-25 08:15:01.324] [info] [multi_process_manager.cpp:218] Process 2 has exited with exit code 0.  
  
```  
  
Wrong:  
  
```cpp  
[vflow] [2025-11-25 09:46:14.421] [debug] [process_monitor.cpp:165] stdin FD: 7, stdout FD: 8, stderr FD: 10  
[vflow] [2025-11-25 09:46:14.430] [debug] [process_monitor.cpp:165] stdin FD: 7, stdout FD: 8, stderr FD: 12  
[vflow] [2025-11-25 09:46:14.439] [debug] [process_monitor.cpp:165] stdin FD: 7, stdout FD: 8, stderr FD: 14  
[vflow] [2025-11-25 09:46:14.448] [info] [multi_process_manager.cpp:138] The executable path of process 0 is /root/.cache/bazel/_bazel_root/6a47789cd43c6f5e4bd298439cf4c1d9/sandbox/linux-sandbox/14150/execroot/_main/bazel-out/aarch64-fastbuild/bin/test/catch2_utils.runfiles/_main/test/utils/utils/process_example, and its PID is 53.  
[vflow] [2025-11-25 09:46:14.448] [info] [multi_process_manager.cpp:138] The executable path of process 1 is /root/.cache/bazel/_bazel_root/6a47789cd43c6f5e4bd298439cf4c1d9/sandbox/linux-sandbox/14150/execroot/_main/bazel-out/aarch64-fastbuild/bin/test/catch2_utils.runfiles/_main/test/utils/utils/process_example, and its PID is 54.  
[vflow] [2025-11-25 09:46:14.448] [info] [multi_process_manager.cpp:138] The executable path of process 2 is /root/.cache/bazel/_bazel_root/6a47789cd43c6f5e4bd298439cf4c1d9/sandbox/linux-sandbox/14150/execroot/_main/bazel-out/aarch64-fastbuild/bin/test/catch2_utils.runfiles/_main/test/utils/utils/process_example, and its PID is 55.  
[vflow] [2025-11-25 09:46:15.432] [debug] [process_monitor.cpp:44] The remaining data in the stderr pipe of the process with PID 53 is 0 bytes.  
[vflow] [2025-11-25 09:46:20.814] [info] [multi_process_manager.cpp:218] Process 0 has exited with exit code 1.  
[vflow] [2025-11-25 09:46:20.815] [debug] [process_monitor.cpp:44] The remaining data in the stderr pipe of the process with PID 54 is 0 bytes.  
[vflow] [2025-11-25 09:46:20.815] [info] [multi_process_manager.cpp:218] Process 1 has exited with exit code 0.  
[vflow] [2025-11-25 09:46:20.816] [info] [multi_process_manager.cpp:218] Process 2 has exited with exit code 0.  
[vflow] [2025-11-25 09:46:20.816] [debug] [process_monitor.cpp:44] The remaining data in the stderr pipe of the process with PID 55 is 0 bytes.  
```  
  
```cpp  
[vflow] [2025-11-21 05:22:21.687] [debug] [process_monitor.cpp:165] stdin FD: 7, stdout FD: 8, stderr FD: 10  
[vflow] [2025-11-21 05:22:21.693] [debug] [process_monitor.cpp:165] stdin FD: 7, stdout FD: 8, stderr FD: 12  
[vflow] [2025-11-21 05:22:21.703] [debug] [process_monitor.cpp:165] stdin FD: 7, stdout FD: 8, stderr FD: 14  
[vflow] [2025-11-21 05:22:21.712] [info] [multi_process_manager.cpp:138] The executable path of process 0 is /root/.cache/bazel/_bazel_root/1d5b955b5bfc5b7922b7d1a4d4feb60d/sandbox/processwrapper-sandbox/80/execroot/_main/bazel-out/k8-fastbuild/bin/test/catch2_utils.runfiles/_main/test/utils/utils/process_example, and its PID is 6479.  
[vflow] [2025-11-21 05:22:21.712] [info] [multi_process_manager.cpp:138] The executable path of process 1 is /root/.cache/bazel/_bazel_root/1d5b955b5bfc5b7922b7d1a4d4feb60d/sandbox/processwrapper-sandbox/80/execroot/_main/bazel-out/k8-fastbuild/bin/test/catch2_utils.runfiles/_main/test/utils/utils/process_example, and its PID is 6480.  
[vflow] [2025-11-21 05:22:21.712] [info] [multi_process_manager.cpp:138] The executable path of process 2 is /root/.cache/bazel/_bazel_root/1d5b955b5bfc5b7922b7d1a4d4feb60d/sandbox/processwrapper-sandbox/80/execroot/_main/bazel-out/k8-fastbuild/bin/test/catch2_utils.runfiles/_main/test/utils/utils/process_example, and its PID is 6481.  
[vflow] [2025-11-21 05:22:24.043] [debug] [process_monitor.cpp:44] The remaining data in the stderr pipe of the process with PID 6479 is 0 bytes.  
[vflow] [2025-11-21 05:22:24.043] [info] [multi_process_manager.cpp:218] Process 0 has exited with exit code 1.  
[vflow] [2025-11-21 05:22:24.043] [debug] [process_monitor.cpp:225] The process with PID 6480 is starting to terminate.  
[vflow] [2025-11-21 05:22:24.043] [debug] [process_monitor.cpp:246] The process with PID 6480 has been terminated. Exit Code: 1  
[vflow] [2025-11-21 05:22:24.044] [debug] [process_monitor.cpp:44] The remaining data in the stderr pipe of the process with PID 6481 is 0 bytes.  
[vflow] [2025-11-21 05:22:24.044] [info] [multi_process_manager.cpp:218] Process 2 has exited with exit code 0.  
[vflow] [2025-11-21 05:22:24.044] [debug] [process_monitor.cpp:44] The remaining data in the stderr pipe of the process with PID 6480 is 0 bytes.  
[vflow] [2025-11-21 05:22:24.044] [info] [multi_process_manager.cpp:218] Process 1 has exited with exit code 0.  
```  
  
Version: 1.84.0  
Linux: 5.4.0-150-generic

---

## Comment 1

> Username: klemens-morgenstern  
> Created at: 2025-12-17 00:48:38 UTC  
> Url: https://github.com/boostorg/process/issues/528#issuecomment-3663080012  

Are you compiling with pidfd_open support or using signals?

---

## Comment 2

> Username: Zeratul-Aiur  
> Created at: 2025-12-22 02:20:25 UTC  
> Updated at: 2025-12-22 02:23:15 UTC  
> Url: https://github.com/boostorg/process/issues/528#issuecomment-3680079923  

I am using Bazel as my build tool and I haven’t manually enabled any compile options. How do I enable pidfd_open support? How can I determine whether pidfd_open is enabled?  
  
Bazel target: https://github.com/nelhage/rules_boost/blob/5d04542e52164931841d06d5a6b3fd2f43c436d0/boost.BUILD#L1569

---

## Comment 3

> Username: klemens-morgenstern  
> Created at: 2025-12-22 04:31:52 UTC  
> Url: https://github.com/boostorg/process/issues/528#issuecomment-3680392055  

Here's the check: https://github.com/boostorg/process/blob/develop/include/boost/process/v2/detail/config.hpp#L164  
  
Specifically if `SYS_pidfd_open` is defined.  
  
What you describe could be caused by it using signals and missing one.
