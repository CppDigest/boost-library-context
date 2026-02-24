# #38 - IO redirection documentation issues [Open]

> Username: matthijskooijman  
> Created at: 2018-04-15 21:02:33 UTC  
> Updated at: 2020-07-27 17:14:52 UTC  
> Url: https://github.com/boostorg/process/issues/38  

I just got started with Boost.process, and noticed some things in the documentation that I hope can be addressed.  
  
About [sync io](https://www.boost.org/doc/libs/1_67_0/doc/html/boost_process/tutorial.html#boost_process.tutorial.io) it says:  
  
>  The pipe will cause a deadlock if you try to read after nm exited  
  
Doesn't this suggest the example above it has a race condition? Also, doesn't the pipe just give EOF when reading from it after the process exited?  
  
About [async IO](https://www.boost.org/doc/libs/1_67_0/doc/html/boost_process/tutorial.html#boost_process.tutorial.async_io)  
  
>  Passing an instance of boost::asio::io_service to the launching function automatically cause it to wait asynchronously for the exit, so no call of wait is needed.  
  
What is waiting for the exit automaticaly? This suggests the `child` constructor does, but I suspect it is the `ios.run()` function that actually does the waiting?  
  
> To make it even easier, you can use std::future for asynchronous operations (you will still need to pass a reference to a boost::asio::io_service) to the launching function, unless you use bp::system or bp::async_system  
  
It seems the parenthesis are messed up here?  
  
Also, perhaps it would be good to mention the fact that passing the `ios` is needed for `bp:child`, but not for `bp:system` (I suppose because it  an create its own `ios` and run it, since `bp:system` is blocking).  
  
```  
boost::asio::io_service ios;  
std::vector<char> buf;  
  
bp::child c(bp::search_path("g++"), "main.cpp", bp::std_out > boost::asio::buffer(buf), ios);  
  
ios.run();  
int result = c.exit_code();  
```  
This example suggests that the output is read into `buf`. Since `buf` does not get a particular size, this suggests that it is automatically resized (it is a vector after all). However, digging into `asio`, shows that these buffers are really simple, and just contain a reference to an existing buffer and size, without any support for resizing. In fact, running the above example leaves `buf` empty, so all output is actually discarded.  
  
Defining `buf` with an initial size allows capturing output up to that size, but I couldn't find a way to actually get the amount of data put into the buffer (since the vector size is unaffected by writing to the buffer).  
  
I'm not quite sure how this is intended to work, but with the current documentation, I got quite the wrong expectation from how this buffer stuff works. The next example on that page shows how to get an auto-resizing buffer by passing a `std::future<std::string>` (which also works for `vector<char>` it seems), but I wonder why a future is actually needed for that: Can it not just write to an existing string instead?

---

## Comment 1

> Username: klemens-morgenstern  
> Created at: 2018-04-15 22:03:35 UTC  
> Url: https://github.com/boostorg/process/issues/38#issuecomment-381441667  

1. No, the pipe does not close automatically, i.e. the sink part is still open on the side of the father process. And no, closing it automatically would not be the right design choice.  
  
2. Automatically means what you suspect it means, i.e. the ios has to be run. But `run` does that asynchrronously, i.e. through signal or object-handle, thus not blocking. So you don't need to call `wait` on the child object. No idea how to phrase that better.  
  
3. Parenthesis seems fine: pass the io_service (and run it at some point) and the future will work.  
  
4. That is mentioned [here](https://www.boost.org/doc/libs/1_67_0/doc/html/boost/process/system.html)  
  
5. The missing buffer size is an error in the example, you are right. I normally use asio::streambuf in my application, where that is not needed.  
  
6. Well you can use a dynamic buffer such as `asio::streambuf` or you can check the end of your buffer by size. You're write there's currently no way to tell how many bytes are in a fixed-size buffer, From my pov that's only useful for a small application, where you basically know how the output looks. If you have a syntax idea how to capture the size let me know.  
  
7. THe future uses a `asio::streambuf` underneath and tells you when it's done.   
  
8. If asio supports that now you can. Back in 1.64 the you could only read from a string.  
  
If you want to do something more complex, you should use `async_pipe` with the other asio stuff. The direct writing to buffers can be useful for simple stuff, but does not scale well.

---

## Comment 2

> Username: matthijskooijman  
> Created at: 2018-04-16 08:24:13 UTC  
> Updated at: 2018-04-16 08:24:24 UTC  
> Url: https://github.com/boostorg/process/issues/38#issuecomment-381519581  

Thanks for your replies. I'll answer below.  
  
1. > No, the pipe does not close automatically, i.e. the sink part is still open on the side of the father process. And no, closing it automatically would not be the right design choice.  
  
    I'm not sure if I got the terminology right, but the sink part is the part you read from (and the source part is written to)? In the case of the stdout pipe, this should have the sink part in the child process and the source part in the father process, right? Are you saying the sink part is *also* still open in the child process?  
  
    What I would expect, is that when the child terminates, the sink part in the child is closed, which subsequently returns EOF on the source part in the father. `pipe(7)` seems to support this saying "If  all  file  descriptors  referring  to  the  write  end of a pipe have been closed, then an attempt to read(2) from the pipe will see end-of-file (read(2) will return 0)". Are you saying this does not happen? Or are there indeed open sink parts kept in the father to prevent this?  
  
    If the EOF-reading indeed does not happen, how can you even read all data from a child pipe? It seems to me that there is a race condition: When the child terminates, there might still be data buffered in the pipe, but if a read might deadlock, you cannot safely read that last bit of data?  
  
2. > Automatically means what you suspect it means, i.e. the ios has to be run. But run does that asynchrronously, i.e. through signal or object-handle, thus not blocking. So you don't need to call wait on the child object. No idea how to phrase that better.  
  
    But `run()` itself does block, so the actual waiting happens in `run()`, not in `child()` AFAICS. The current wording implies (to me) that `child()` would wait. How about:  
  
    > Passing an instance of boost::asio::io_service to the launching function automatically cause the `ios.run()` call to wait asynchronously for the exit, so no call of wait is needed.  
  
3. > Parenthesis seems fine: pass the io_service (and run it at some point) and the future will work.  
  
    I believe the closing parenthesis should move to the end. E.g. if you skip the parenthesised part, the sentence no longer works:  
  
    > To make it even easier, you can use std::future for asynchronous operations ~~(you will still need to pass a reference to a boost::asio::io_service)~~ to the launching function, unless you use bp::system or bp::async_system  
  
    Or "use `std::future" should be "pass `std::future`", but then it would say that using  futures with `bp:system` and `bp::async_system` is not supported, which I don't think is true? OTOH, if the closing parenthesis is moved to the end, it would say that you do not need to pass an `ios` to `bp::async_system`, but reading its docs I suspect that passing `ios` is not optional at all...  
  
 4. > That is mentioned here  
  
    My suggestion was to also add it [here](https://www.boost.org/doc/libs/1_67_0/doc/html/boost_process/tutorial.html#boost_process.tutorial.async_io) (addition in italics):  
  
    >  To make it easier, boost.process provides simpler interface for that, so that the buffer can be passed directly, provided we also pass a reference to an boost::asio::io_service *(when using `bp::system` passing the `boost::asio::io_service` is optional, `bp::system` will create and run one if none is passed).*  
  
5. > The missing buffer size is an error in the example, you are right. I normally use asio::streambuf in my application, where that is not needed.  
  
    Ok.  
  
6. > Well you can use a dynamic buffer such as asio::streambuf or you can check the end of your buffer by size. You're write there's currently no way to tell how many bytes are in a fixed-size buffer, From my pov that's only useful for a small application, where you basically know how the output looks. If you have a syntax idea how to capture the size let me know.  
  
    First off: If these are indeed the limitations of using `bp::buffer`, then I believe the tutorial should at least state them, and probably also offer an example using `streambuf` as an alternative (there is already an alternative show using `std::future<std::string>`, so that's good).  
  
    However, using a fixed-size buffer seems like a valid usecase as well, so it would indeed be good if the size could somehow be communicated.  The first thing that springs to mind, is to introduce a new buffer type that tracks both capacity and actual size and let the caller convert to such a type. Then, in `async_out.hpp`, you can convert this into a normal asio buffer (using the pointer and the capacity) and in the `async_read` completion handler, set the actual size. E.g. for a caller this would look something like this:  
  
    ```  
    auto buf = sized_buffer(std::vector<char>(100));  
    bp::system(..., std_out > buf);  
  
    cout << "Used " << buf.size() << " out of " << buf.capacity() " bytes";  
    ```  
  
    I wonder if something like this should be better placed inside asio, though.  
  
  
  
 7. > The future uses a asio::streambuf underneath and tells you when it's done.  
  
    Ah, looking at the code, it seems that it first reads into a streambuf, and at the end copies into a `std::string`. That explains why it needs a `future`. Pity that it doesn't support reading into the string directly, though, which would skip a copy.  
  
8. > If asio supports that now you can. Back in 1.64 the you could only read from a string.  
  
    What do you mean here? Reading directly into a `std::string` or `std::vector<char>`? How would that look? If you simply do `stdout > some_string`, you'll trigger #37 and things get messed up. Looking more closely at `asio`, I found [`dynamic_buffer`](https://www.boost.org/doc/libs/1_67_0/doc/html/boost_asio/reference/dynamic_buffer.html) which seems to fit perfectly, but it seems trying `std_out > boost::asio::dynamic_buffer(some_string)` doesn't work either:  
  
        foo.cpp: In function ‘int main()’:  
        foo.cpp:11:63: error: no match for ‘operator>’ (operand types are ‘const boost::process::detail::std_out_<1>’ and ‘boost::asio::dynamic_string_buffer<char, std  
        ::char_traits<char>, std::allocator<char> >’)  
          bp::child c(bp::search_path("cat"), "/etc/motd", bp::std_out > boost::asio::dynamic_buffer(data), ios);  
                                                           ~~~~~~~~~~~~^~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
  
    Calling `async_read` manually works, so I guess that's what you meant? So adding an overload to support the `std_out > dynamic_buffer` is really a feature request, should I file a separate issue for that?  
  
9. To solve the `dynamic_buffer` thing more generally, wouldn't it be possible to allow redirecting to anything that supports being passed into `async_read()` instead of having a hardcoded list as now? Or would that cause issues?

---

## Comment 3

> Username: klemens-morgenstern  
> Created at: 2018-04-16 08:58:50 UTC  
> Url: https://github.com/boostorg/process/issues/38#issuecomment-381529037  

1. Sink is the one written to, source is the one read from. Btw. just checked the code, the last statement was incorrect. The handle of the pipe you pass to std_* not used will actually be closed, but you can hold a copy. I.e. in the case of   
  
```cpp  
ipstream str;  
child c("foo", std_out > str);  
```   
  
will automatically close the pipe when foo exits (i.e. the sink on the child size, causing EOF to be read from the source).  
  
```cpp  
pipe p;  
ipstream str(p);  
child c("foo", std_out > str);  
```  
  
This would keep the pipe sink open, because you hold cloned handle. Sorry about that mistake.  
  
6. That is a design problem, especially when used with `bp::system`. Having a temporary type for that might make sense, though I'm not sure how well that works tbh.  
  
```cpp  
size_t read_bytes;  
error_code ec;  
vector<char> buf;  
  
bp::system(..., std_out(bytes, ec) > buf);  
```   
8. `boost::asio::buffer` gives you a constbuf when used with a string in 1.64. Not sure if that changed.  
The `dynamic_buffer` should be supported, though, just didn't get around to it yet.  
  
9. Yep, that would be the proper solution.

---

## Comment 4

> Username: matthijskooijman  
> Created at: 2018-04-16 12:27:50 UTC  
> Url: https://github.com/boostorg/process/issues/38#issuecomment-381581846  

1. Ok, so the warning in the docs isn't correct with the example as such and might need to be removed, or modified to warn against extra copies of the pipe fd's. I tried the example, removing the `running()` check in the loop and adding a `getline` after the wait, and did not see any deadlock. Modifying the example to create a `pipe` and copy that into a `ipstream` as you suggest *does* deadlock.  
  
...  
  
 6. Not sure I like passing stuff to `std_out` either, it doesn't really read well...  
  
...  
  
 8. I don't think that changed. I believe that there is no way to access a `std::string`'s bytes directly other than through `.c_str()`, which returns a const*. The only way to write a string's bytes, AFAICS, is through it's methods or operators, which requires keeping a reference to the string itself (which is what .e.g `dynamic_buffer` does).

---

## Comment 5

> Username: klemens-morgenstern  
> Created at: 2018-04-16 12:39:35 UTC  
> Url: https://github.com/boostorg/process/issues/38#issuecomment-381585062  

1. Right, I changed the code without updating the doc.  
  
6. Yep, not sure how to solve that properly. I guess going for a dynamic_buffer would be the proper solution, the fixed buffers don't work well without callbacks anyway. I might as well remove them and only allow the dynamic ones.  
  
8. `std::string::data` in C++17 or `&*std::string::begin()` work and you can create a `asio::buffer` from pointers to the memory, i.e.   
  
```cpp  
std::string buffer;  
buffer.resize(4096);  
auto mutable_buffer = boost::asio::buffer(&*buffer.begin(), buffer.size());   
```  
should work.

---

## Comment 6

> Username: jonesmz  
> Created at: 2019-04-11 09:15:04 UTC  
> Updated at: 2019-04-11 16:38:50 UTC  
> Url: https://github.com/boostorg/process/issues/38#issuecomment-482035765  

I know that this has been opened for a while, and several improvements have been made to the boost process documentation since then,  
  
but I wanted to chime in on my experience with figuring out how to use boost::process with boost::asio.  
  
First, I tried to use the Child interface, with an io_context, and an on_exit handler. But I couldn't figure out how to keep the Child object alive until on_exit was called. I considered using std::make_shared<Child>, but that's not possible because I would have needed to put the resulting shared pointer into the on_exit handler in some way. Alternatives could include creating a map with a unique index key, and putting that key in the on_exit handler.  
  
Then I tried boost::process::async_system, which was much closer to what I wanted. I was able to get the on_exit handler to run properly, but it was really confusing for me what the options to the constructor were supposed to be based on the documentation. Ultimately I just tried using the same constructor args that I was using with Client, and that seemed to work.  
  
But getting data out of the child process's std_out was very confusing.  
  
For what it's worth, my linux system has Boost 65, so I didn't immediately get access to the new and improved documentation. So at first, I tried using std::string with boost::asio::buffer, but that resulted in compile failure because in Boost 65, `boost::asio::buffer(std::string)` always returns a const_buffer.  
  
Then I tried using `boost::asio::buffer(std::vector<char>)`, with the lifetime of std::vector<char> managed by a shared pointer. That "worked" in the sense that my child program would run, but the std::vector<char> was always empty.  
  
I tried to figure out how to use the boost::asio::streambuf, but the asio documentation is... very light on examples...  
  
And finally I found this discussion here, which said that I should be able to use std::future<std::string> with boost::process::async_system. Using `std::future<std::string>` works.  
  
  
From Boost 69  
  
> To make it even easier, you can use std::future for asynchronous operations (you will still need to pass a reference to a boost::asio::io_service) to the launching function, unless you use bp::system or bp::async_system.  
  
When I read this, it was very obvious that it meant std::future could *not* be used with bp::async_system. This is clearly not the case.  
  
I think that there's no real need to say : unless you use bp::system or bp::async_system.  
  
A more clear wording, in my opinion:  
  
> To make it even easier, you can use std::future for asynchronous operations. To use std::future, make sure you pass a reference to a boost::asio::io_service to your call to bp::system, bp::async_system, or bp::Client  
  
This is how I ended up using boost::process::async_system().  
  
```  
std::shared_ptr<std::future<std::string>> pFuture = std::make_shared<std::future<std::string>>();  
auto lambda = [pFuture, &](boost::system::error_code ec, int exit_code){ ... do something when the program exits ... };  
boost::process::async_system(ios, std::move(lambda), "/path/to/program", boost::process::std_out > *pFuture);  
```  
  
I found the need to use a shared_ptr to a future to be awkward, and I think it would be easier on users new to the library if it was explicitly pointed out, or if std::shared_future was supported (I get a compiler error on Boost 65, at least).

---

## Comment 7

> Username: klemens-morgenstern  
> Created at: 2019-04-11 09:37:59 UTC  
> Url: https://github.com/boostorg/process/issues/38#issuecomment-482043594  

Shared future is not a bad idea. I might look over clearing up the docs a bit; I think a big part is the complexity brought about by `boost.asio`; if you know how that works, process falls into place. I think my shortcuts using`std_out> future<string>` actually backfired a bit, forcing you to use `async_pipe` might have been the better choice.

---

## Comment 8

> Username: lgwozdecki  
> Created at: 2020-07-27 16:24:02 UTC  
> Url: https://github.com/boostorg/process/issues/38#issuecomment-664498615  

Hello, I am writing about inconsistencies in the documentation of synchronous I/O. According to point 1 in the thread:  
1. https://klemens.dev/process/boost_process/faq.html#boost_process.faq.closep - this example is wrong because while loop will finish after EOF after all stream was read. There will be deadlock only in case of pipe copy.  
2. https://klemens.dev/process/boost_process/tutorial.html#boost_process.tutorial.io - in all the examples using "running" function creates a possibility that not all data is read from stream. It would be better to check for stream operator bool like in introduction example https://klemens.dev/process/index.html#boost_process.introduction or even omit it (getline returns the stream).  
  
can you confirm that my understanding is correct? Is there a plan to update the documentations?

---

## Comment 9

> Username: klemens-morgenstern  
> Created at: 2020-07-27 17:14:52 UTC  
> Url: https://github.com/boostorg/process/issues/38#issuecomment-664525529  

Yes your understanding is correct in both cases. I am sorry for that, updating the docs fell off my radar a bit.   
Updated will at the earliers make it into the 1.75 release though.
