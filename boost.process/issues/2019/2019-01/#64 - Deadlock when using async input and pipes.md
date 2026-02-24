# #64 - Deadlock when using async input and pipes. [Open]

> Username: davisking  
> Created at: 2019-01-02 18:27:49 UTC  
> Updated at: 2024-09-25 23:43:36 UTC  
> Url: https://github.com/boostorg/process/issues/64  

Should this work, or is there a bug in what I'm doing?  With the latest code on github, the following program hangs:  
  
```  
    std::string data = "some data to send to cat";  
    std::future<std::string> result;  
    boost::asio::io_service ios;  
    bp::pipe p;  
    bp::spawn("cat", bp::std_in < bp::buffer(data), bp::std_out > p, ios);  
    bp::spawn("cat", bp::std_in < p, bp::std_out > result, ios);  
    
    // run blocks forever.  
    ios.run();  
    std::cout << result.get() << std::endl;  
```  
  
This seems like something that ought to work.  Also, these two other programs that are only a little different work as expected, making it seem like there might be a bug in the interaction between multiple processes participating in a pipeline and the use of async input:  
```  
    // use echo to make data instead of bp::buffer()   
    std::future<std::string> result;  
    boost::asio::io_service ios;  
    bp::pipe p1,p2;  
    bp::spawn("echo -n some data to send to cat", bp::std_out > p1);  
    bp::spawn("cat", bp::std_in < p1, bp::std_out > p2);  
    bp::spawn("cat", bp::std_in < p2, bp::std_out > result, ios);  
  
    ios.run();  
    std::cout << result.get() << std::endl;  
  
```  
  
```  
    // When pipes aren't used everything also works as expected.  
    std::string data = "some data to send to cat";  
    std::future<std::string> result;  
    boost::asio::io_service ios;  
    bp::spawn("cat", bp::std_in < bp::buffer(data), bp::std_out > result, ios);  
  
    ios.run();  
    std::cout << result.get() << std::endl;  
```

---

## Comment 1

> Username: klemens-morgenstern  
> Created at: 2019-01-03 01:41:21 UTC  
> Url: https://github.com/boostorg/process/issues/64#issuecomment-451037219  

I think it's a bug in the async stdin version, because (I fear) that doesn't close the pipe when done. Thus cat stays active since the pipe is open.  
  
Can you test using a different input method? i won't be able to look into it before next week.

---

## Comment 2

> Username: davisking  
> Created at: 2019-01-03 12:48:25 UTC  
> Url: https://github.com/boostorg/process/issues/64#issuecomment-451133857  

I've found a number of other seemingly related deadlocks. Like maybe https://github.com/boostorg/process/issues/65.  As well as this:  
  
```  
    std::string data = "some data to send to cat";  
    std::future<std::string> result;  
    boost::asio::io_service ios;  
    boost::process::pipe p;  
    opstream out;  
    spawn("cat", std_in<out, std_out>p);  
    spawn("cat", std_in<p, std_out>result, ios);  
  
    std::thread t([&]() { out << data << std::flush; out.pipe().close(); });  
    // run blocks forever.  
    ios.run();  
    t.join();  
```  
However, this slightly different version works as expected:  
```  
    std::string data = "some data to send to cat";  
    std::future<std::string> result;  
    boost::asio::io_service ios;  
    boost::process::pipe p;  
    opstream out;  
    spawn("cat", std_in<out, std_out>result, ios);  
  
    std::thread t([&]() { out << data << std::flush; out.pipe().close(); });  
    ios.run();  
    t.join();  
```  
  
Maybe they are all caused by the same underlying issue.  I am not sure.

---

## Comment 3

> Username: klemens-morgenstern  
> Created at: 2019-01-07 10:50:17 UTC  
> Url: https://github.com/boostorg/process/issues/64#issuecomment-451896307  

I assume you're on linux, right?  
Can you try out what happens if you change `spawn` to `async_system` and put some dummy handler in?  
  
I.e. `async_system(ios, [](const boost::system::error_code & ec, int ret){}, ("cat", std_in<out, std_out>p);`

---

## Comment 4

> Username: davisking  
> Created at: 2019-01-08 03:23:43 UTC  
> Url: https://github.com/boostorg/process/issues/64#issuecomment-452162182  

Yeah, using Ubuntu 16.04.  
  
I tried replacing `spawn` with `async_system` and it still hangs just like before.

---

## Comment 5

> Username: klemens-morgenstern  
> Created at: 2019-01-08 03:48:24 UTC  
> Url: https://github.com/boostorg/process/issues/64#issuecomment-452165819  

Weird. Alright, I'll look into it. It might actually be the same bug for all your issues.

---

## Comment 6

> Username: davisking  
> Created at: 2019-01-08 04:08:49 UTC  
> Url: https://github.com/boostorg/process/issues/64#issuecomment-452169022  

Sweet, thanks :)  
  
Yeah, that's my suspicion as well.

---

## Comment 7

> Username: klemens-morgenstern  
> Created at: 2019-01-08 06:59:53 UTC  
> Url: https://github.com/boostorg/process/issues/64#issuecomment-452195404  

Do you use boost 1.68 or the current HEAD of the git repo?

---

## Comment 8

> Username: davisking  
> Created at: 2019-01-08 12:55:56 UTC  
> Url: https://github.com/boostorg/process/issues/64#issuecomment-452288477  

I'm using Boost 1.66 along with the code from  
https://github.com/boostorg/process as it was on Jan 1st.

---

## Comment 9

> Username: klemens-morgenstern  
> Created at: 2019-01-09 05:40:50 UTC  
> Url: https://github.com/boostorg/process/issues/64#issuecomment-452578704  

Ok, that should, but could you use the currend develop-HEAD just to be sure?

---

## Comment 10

> Username: davisking  
> Created at: 2019-01-10 19:31:13 UTC  
> Url: https://github.com/boostorg/process/issues/64#issuecomment-453223729  

Just grabbed the develop-HEAD of https://github.com/boostorg/process and got the same results, same things work and same things still hang.

---

## Comment 11

> Username: LuisAyuso  
> Created at: 2019-05-22 07:08:39 UTC  
> Url: https://github.com/boostorg/process/issues/64#issuecomment-494680813  

I am experimenting sort of the same issue. Although my code only binds stderr and stdout output streams, whenever I execute the context run function it will hang in there even when the child process is gone.  
  
something like:  
```  
    child = boost::process::child(path, args, group, bp::std_out > stdout_stream, bp::std_err > stderr_stream, ios);  
[..]  
    ios.run();  
    child.wait();  
```  
When I submit a kill signal to the child process, the parent process is still waiting for IO. ```child.wait()``` is never executed.  
  
Is this the same issue?   
If so, could we rename this thread to remove the input keyword?

---

## Comment 12

> Username: klemens-morgenstern  
> Created at: 2019-05-22 07:30:56 UTC  
> Url: https://github.com/boostorg/process/issues/64#issuecomment-494687455  

@LuisAyuso I would need more about your use-case - do the streams refer to an `async_pipe`?

---

## Comment 13

> Username: LuisAyuso  
> Created at: 2019-05-22 07:40:29 UTC  
> Url: https://github.com/boostorg/process/issues/64#issuecomment-494690402  

my stream objects are a custom class,  they wrap an async_pipe, hold a buffer to accumulate output,  and re-register the callback after every read.   
I  noticed that the read callback is never invoked after child process dead, not even to notify an error.

---

## Comment 14

> Username: klemens-morgenstern  
> Created at: 2019-05-22 07:58:03 UTC  
> Url: https://github.com/boostorg/process/issues/64#issuecomment-494695956  

Weird, which system are you on? Normally you should get an EOF.  
  
What do you call to start the read operation?

---

## Comment 15

> Username: LuisAyuso  
> Created at: 2019-05-22 08:52:18 UTC  
> Updated at: 2019-05-22 08:52:33 UTC  
> Url: https://github.com/boostorg/process/issues/64#issuecomment-494714388  

Sorry for fragmented information, let's see if we manage to have a proper report ;)  
I am using Ubuntu 18.04, with gcc 7.4.0  
The Boost version is 1.69  
  
I have been cleaning the code so I can show something here:  
```  
    ba::io_context ios;  
  
    std::vector<char> stdout_buffer(1024);  
    auto              out_buffer{ba::buffer(stdout_buffer)};  
    bp::async_pipe    std_out{ios};  
    std::function<void(const boost::system::error_code &ec, size_t s)> stdout_callback;  
    stdout_callback = [&](const boost::system::error_code &ec, size_t s) {  
        if (ec)  
            return;  
        auto v = std::string_view(&stdout_buffer.front(), s);  
        fmt::print("stdout: {}", v);  
        std_out.async_read_some(out_buffer, stdout_callback);  
    };  
  
    std::vector<char> stderr_buffer(1024);  
    auto              err_buffer{ba::buffer(stderr_buffer)};  
    bp::async_pipe    std_err{ios};  
    std::function<void(const boost::system::error_code &ec, size_t s)> stderr_callback;  
    stderr_callback = [&](const boost::system::error_code &ec, size_t s) {  
        if (ec)  
            return;  
        auto v = std::string_view(&stdout_buffer.front(), s);  
        fmt::print("stderr: {}", v);  
        std_err.async_read_some(err_buffer, stderr_callback);  
    };  
  
    std_out.async_read_some(out_buffer, stdout_callback);  
    std_err.async_read_some(err_buffer, stderr_callback);  
  
    child =  
          bp::child(path, args, group, bp::std_out > std_out, bp::std_err > std_err, ios);  
  
    ios.run();  
    child.wait();  
```  
The behavior of this code is the same as the one reported originally.  
- ```ios.run()``` blocks event when the child process is dead.  
- ```child.wait()``` is never executed if the child process is externally killed

---

## Comment 16

> Username: klemens-morgenstern  
> Created at: 2019-05-22 09:56:52 UTC  
> Url: https://github.com/boostorg/process/issues/64#issuecomment-494736362  

This is weird indeed. Can you add `bp::on_exit` to check if this gets called?   
  
Also, where does the `group` come from?

---

## Comment 17

> Username: LuisAyuso  
> Created at: 2019-05-22 13:26:44 UTC  
> Url: https://github.com/boostorg/process/issues/64#issuecomment-494801597  

Group is currently default initialized. (bp::group)  
  
on_exit works, I can use it now to shut down the io context (```ios.stop()```) and let the program proceed to ```child.wait()```  
I can use now this as a workaround, it is way better than my original one (which was to put io.run in a separate thread)

---

## Comment 18

> Username: klemens-morgenstern  
> Created at: 2019-05-22 13:48:42 UTC  
> Url: https://github.com/boostorg/process/issues/64#issuecomment-494810424  

The better workaround would be to close the pipes from the on-exit handler.  
  
Can you try the current master branch and see if it fixes the issue?

---

## Comment 19

> Username: xd009642  
> Created at: 2020-02-27 23:01:37 UTC  
> Url: https://github.com/boostorg/process/issues/64#issuecomment-592221763  

So I encountered this issue today, but I had also encountered the same effect in some rust async code in tokio. So because of the possibility they could be the same issue here is my associated issue and more importantly the PR that fixed it https://github.com/tokio-rs/tokio/issues/2255 https://github.com/tokio-rs/tokio/pull/2218

---

## Comment 20

> Username: Spixmaster  
> Created at: 2024-09-19 07:54:05 UTC  
> Updated at: 2024-09-20 08:22:58 UTC  
> Url: https://github.com/boostorg/process/issues/64#issuecomment-2360251685  

@klemens-morgenstern, I still have the same issue even with v2.  
  
```cpp  
boost::asio::io_context io_context;  
boost::asio::writable_pipe std_in(io_context);  
boost::system::error_code error_code_stdin;  
boost::asio::write(std_in, boost::asio::buffer("asdf"), error_code_stdin);  
  
const std::int32_t exit_code = boost::process::v2::execute(boost::process::v2::process(  
  io_context, boost::process::v2::environment::find_executable("cat"), {},  
  boost::process::v2::process_stdio {std_in, {}, {}}));  
//First point that is not reached.  
```

---

## Comment 21

> Username: klemens-morgenstern  
> Created at: 2024-09-19 15:08:37 UTC  
> Url: https://github.com/boostorg/process/issues/64#issuecomment-2361289946  

You're using execute which is blocking, which waits for subprocess to exit.   
And `cat` has an open pipe as stdio, which means it won't exit.

---

## Comment 22

> Username: Spixmaster  
> Created at: 2024-09-19 15:13:10 UTC  
> Updated at: 2024-09-20 08:47:02 UTC  
> Url: https://github.com/boostorg/process/issues/64#issuecomment-2361305829  

Can you give a working example, please?  
  
What do you mean with "open pipe"? Running `echo "a" | cat` prints `a`. `cat` prints out what it received via stdin. Is not it possible to use `cat` with `boost::process::v2::process` and stdin?

---

## Comment 23

> Username: klemens-morgenstern  
> Created at: 2024-09-25 23:43:34 UTC  
> Url: https://github.com/boostorg/process/issues/64#issuecomment-2375456344  

You provided an example:  
  
1. `execute` waits for `cat` to exit (unlike `v2::process`)  
2. `cat` waits for the input pipe to be closed or a signal  
  
```cpp  
boost::asio::io_context io_context;boost::asio::writable_pipe std_in(io_context);  
boost::system::error_code error_code_stdin;  
  
boost::process::v2::process cat(  io_context, boost::process::v2::environment::find_executable("cat"), {},  boost::process::v2::process_stdio {std_in, {}, {}});  
  
boost::asio::write(std_in, boost::asio::buffer("asdf"), error_code_stdin);  
std_in.close();  
  
const auto int exit_code = cat.wait();  
```
