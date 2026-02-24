# #505 - Make launching a process without throwing an exception more obvious in Boost.Process V2 [Open]

> Username: csk-ableton  
> Created at: 2025-07-02 14:04:47 UTC  
> Updated at: 2025-07-14 07:52:51 UTC  
> Url: https://github.com/boostorg/process/issues/505  

Boost.Process generally offers throwing and non-throwing overloads.  
  
The constructor of `boost::process::v2::process` is missing an overload that takes an `error_code` instance. The following code does not compile:  
  
```cpp  
boost::system::error_code ec;  
boost::process::v2::process process{ctx, ec, "/usr/bin/cp", {"source.txt", "target.txt"}};  
```  
  
Looks like these constructors are simply missing and could easily forward `ec` to `default_process_launcher`? v1 offered such constructor overloads.

---

## Comment 1

> Username: klemens-morgenstern  
> Created at: 2025-07-02 21:00:05 UTC  
> Url: https://github.com/boostorg/process/issues/505#issuecomment-3029311581  

That's a deliberate choice. I can't think of any other library having constructors that take an error_code to indicate an error.  
Rather, the common pattern is that a constructor should throw on error.  
  
In other libraries, the `ec` always gets passsed into a function, that returns an empty thingy if `ec` is set. You can do the above easily enough by just using the default_launcher directly.  
  
```cpp  
boost::system::error_code ec;  
auto process = boost::process::v2::default_launcher()(ctx, ec, "/usr/bin/cp", {"source.txt", "target.txt"});  
```

---

## Comment 2

> Username: csk-ableton  
> Created at: 2025-07-03 07:43:03 UTC  
> Url: https://github.com/boostorg/process/issues/505#issuecomment-3031228550  

Hi @klemens-morgenstern,   
  
thanks for the quick response!  
  
> That's a deliberate choice. I can't think of any other library having constructors that take an error_code to indicate an error. Rather, the common pattern is that a constructor should throw on error.  
  
You are totally right that it's not very common. There is `filesystem::directory_iterator` but it has an obvious state it can go into in case of an error.  
  
A better example might be `boost::asio::basic_socket_acceptor` and `basic_stream_file` which don't take an `error_code` in their constructors but they do provide non-throwing constructors that don't open the socket/stream and allow to do that using a method afterwards.   
  
`process` has a non-throwing default constructor but no way to later launch the process as far as I can see? Maybe introducing `launch` methods that take an `error_code` could be an option? That would make the API for clients that want to avoid exceptions more obvious than the example you provided and is consistent with the design in Asio.  
  
What do you think about documenting the exception behavior in the constructors? Given the API generally offers non-throwing versions, one could assume the lack of such an API means that the constructor doesn't throw and lead to surprises / crashes, especially given the code that throws is hidden behind an indirection.

---

## Comment 3

> Username: klemens-morgenstern  
> Created at: 2025-07-03 08:30:01 UTC  
> Url: https://github.com/boostorg/process/issues/505#issuecomment-3031365624  

What's the issue with this?  
  
```cpp  
boost::process::v2::process proc;  
  
boost::system::error_code ec;  
proc = boost::process::v2::default_launcher()(ctx, ec, "/usr/bin/cp", {"source.txt", "target.txt"});  
```  
  
Do you need this to be a member? You could also do this:  
  
```cpp  
auto launch = boost::process::v2::default_launcher();  
  
boost::process::v2::process proc;  
boost::system::error_code ec;  
  
proc = launch(ctx, ec, "/usr/bin/cp", {"source.txt", "target.txt"});  
```

---

## Comment 4

> Username: csk-ableton  
> Created at: 2025-07-03 08:43:15 UTC  
> Url: https://github.com/boostorg/process/issues/505#issuecomment-3031408292  

> What's the issue with this?  
  
No issue once I found this API, it just took me a moment to discover it. I just tried to make suggestions for making the non-throwing API more discoverable. I don't technically need a member.
