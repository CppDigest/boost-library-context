# #499 - How do I forward the current environment to a child process with added/removed env variables? [Open]

> Username: martin5233  
> Created at: 2025-06-26 08:11:48 UTC  
> Updated at: 2025-06-30 08:26:31 UTC  
> Url: https://github.com/boostorg/process/issues/499  

Migrating from Boost 1.85 to 1.88 I am trying to migrate existing code from v1 to v2. Unfortunately the documentation is still scarce and I have trouble converting our code. In one case I want to use the current process environment and forward it to a child process with some variables added and some removed. I haven't found anything in the documentation on how to do this and also looking into the header files of environment.hpp left me wondering if this is possible at all. Please let me know how to do this.

---

## Comment 1

> Username: klemens-morgenstern  
> Created at: 2025-06-26 08:52:41 UTC  
> Url: https://github.com/boostorg/process/issues/499#issuecomment-3007716862  

You'll need to copy the current environment from `environment::current()` and pass it into the subsprocess.  
  
What data structure you use is up to you.   
  
Below are some examples:  
  
```cpp  
    boost::asio::io_context ctx;  
  
    namespace bp = boost::process;  
  
    auto c = bp::environment::current();  
    std::vector<bp::environment::key_value_pair> env{c.begin(), c.end()};  
    env.push_back(bp::environment::key_value_pair("new-value", "test"));  
    bp::process p(ctx, "/bin/not-a-virus", {}, bp::process_environment{env});  
    
    // or  
    std::unordered_map<bp::environment::key, bp::environment::value> env2;  
    for (const auto & entry : c)  
    {  
      if (entry.key() != "PATH") // don't copy PATH values  
        env2.emplace(entry.key(), entry.value());  
    }  
    env2.emplace("PATH", "/foo:/bar");  
  
    bp::process q(ctx, "/bin/not-a-virus", {}, bp::process_environment{env});  
  
```

---

## Comment 2

> Username: martin5233  
> Created at: 2025-06-26 10:09:14 UTC  
> Url: https://github.com/boostorg/process/issues/499#issuecomment-3007946246  

Thanks a lot for the quick reply. That's not exactly easier to use than V1 though. It would be good, if it was possible to initialize a process_environment from environment::current() and then add or remove individual variables. Also the whole type system around the environment is not documented at all, so finding out, that you need key_value_pair is not obvious.

---

## Comment 3

> Username: klemens-morgenstern  
> Created at: 2025-06-26 14:16:21 UTC  
> Url: https://github.com/boostorg/process/issues/499#issuecomment-3008658819  

It is not mean to be simpler, but more obvious. V1 was doing a bunch of stuff under the hood that should've been explicit to begin with.   
You're not wrong on the documentation.

---

## Comment 4

> Username: klemens-morgenstern  
> Created at: 2025-06-26 14:49:28 UTC  
> Url: https://github.com/boostorg/process/issues/499#issuecomment-3008768095  

I added some docs https://github.com/boostorg/process/commit/c72650df3017a5000970f3b8bc89465140e8a8ce  
  
If you got some more questions please ask them. I'll add to the docs accordingly.

---

## Comment 5

> Username: martin5233  
> Created at: 2025-06-27 13:46:08 UTC  
> Updated at: 2025-06-27 18:27:12 UTC  
> Url: https://github.com/boostorg/process/issues/499#issuecomment-3013146143  

You are very helpful. What would help a lot, would be some real examples. Currently I'm struggling with reading stdout and stderr via two pipes from a subprocess. This used to work flawlessly with V1, but I cannot get it to work with V2. It's probably more related to asio, but with V2 I am forced to use more of it.  
Currently I have the following snippet:  
  
```cpp      
      asio::io_context ios;  
      std::string stdoutString;  
      asio::readable_pipe pipeStdout(ios);  
      std::string stderrString;  
      asio::readable_pipe pipeStderr(ios);  
  
      log << "Starting process with cmd = " << cmd << "\n";  
      log.flush();  
      bp::process_stdio filehandles  
      {  
         .in = nullptr,       // close  
         .out = pipeStdout,  
         .err = pipeStderr  
      };  
      bp::process c(ios, cmd, args, filehandles, bp::process_environment(targetEnv));  
      asio::async_read(pipeStdout, asio::dynamic_buffer(stdoutString), [&log](boost::system::error_code, std::size_t) { log << "async_read stdout finished\n"; log.flush(); });  
      asio::async_read(pipeStderr, asio::dynamic_buffer(stderrString), [&log](boost::system::error_code, std::size_t) { log << "async_read stderr finished\n"; log.flush(); });  
      log << "Calling ios.run()\n";  
      log.flush();  
      ios.run();  
      log << "Calling c.wait()\n";  
      log.flush();  
      c.wait();  
      log << "Returned from c.wait()\n";  
      log.flush();  
```  
but the call to ios.run() never returns. With strace I can see, that the started process writes something its stdout, which is read by the parent process. Then the child process exits with exit code 0. Do you have any idea, what I'm doing wrong?

---

## Comment 6

> Username: klemens-morgenstern  
> Created at: 2025-06-27 18:36:11 UTC  
> Updated at: 2025-06-27 18:36:29 UTC  
> Url: https://github.com/boostorg/process/issues/499#issuecomment-3014043671  

Assigning the pipeStdout to out will create a writable_pipe within the `process_stdio` object. You need to destruct that, so that the writeEnd of the pipe in the current process gets closed. The easiest way to do this is by making it a temporary:  
  
```cpp  
      bp::process c(ios, cmd, args, bp::process_stdio{.in=nullptr, .out=pipeStdout,.err=pipeStderr}, bp::process_environment(targetEnv));  
```  
  
  
`async_read` will read until the end of the stream, i.e. until the subprocess finishes, that's why the other side of the pipe needs to be closed.  
  
I would think you'd  also want to make async_wait an asynchronous operation:  
  
```cpp  
bp::process c(ios, cmd, args, filehandles, bp::process_environment(targetEnv));  
asio::async_read(pipeStdout, asio::dynamic_buffer(stdoutString), [&log](boost::system::error_code, std::size_t) { log << "async_read stdout finished\n"; log.flush(); });  
asio::async_read(pipeStderr, asio::dynamic_buffer(stderrString), [&log](boost::system::error_code, std::size_t) { log << "async_read stderr finished\n"; log.flush(); });  
c.async_wait([&log](const boost::system::error_code& ec, int exit_code) {log << "Process finished with code: " << exit_code;});  
````  
  
I take it you did not use asio heavily before? If so I'd generally recommend using an asio::awaitable if you can (i.e. if you have C++20).  
  
Can you think of a generally available process that shows the same behaviour for an example??

---

## Comment 7

> Username: martin5233  
> Created at: 2025-06-30 06:13:25 UTC  
> Url: https://github.com/boostorg/process/issues/499#issuecomment-3017926866  

Thank you for your feedback. With the trick to make process_stdio a temporary reading the pipes works now. However the wait always fails, if the child process has already finished, when the wait is reached. This looks more like a bug or a race condition to me (the sequence of ios.run(); c.wait() worked flawlessly in V1). async_wait isn't an option here, as I want the calling function to wait until the underlying (short-lived) process has finished. I circumvented this by using wait with an error code and ignore the error code for 'no child processes', which isn't ideal.  
  
Overall I have to say, that for my understanding using Boost.Process has become much more complicated with V2. V1 used to shield the user from most asio intricacies, which V2 no longer does. I understand, that it makes V2 more flexible, but sometimes you only need simplicity.  
  
Regarding your question for an example, I think you could use any subprocess you want, e.g. "/bin/ls", it doesn't really matter. From my point of view starting a process, while separately catching stdout and stderr is a standard use case, which should be as simple as possible.

---

## Comment 8

> Username: klemens-morgenstern  
> Created at: 2025-06-30 08:26:31 UTC  
> Url: https://github.com/boostorg/process/issues/499#issuecomment-3018253891  

The wait should complete without an error in this case. I'll change that.
