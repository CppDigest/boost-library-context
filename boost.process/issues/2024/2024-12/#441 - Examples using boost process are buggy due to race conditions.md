# #441 - Examples using boost process are buggy due to race conditions [Closed]

> Username: pps83  
> Created at: 2024-12-18 20:22:32 UTC  
> Updated at: 2024-12-20 00:39:48 UTC  
> Closed at: 2024-12-20 00:39:48 UTC  
> Url: https://github.com/boostorg/process/issues/441  

For example, basic code to read stdout from a child process: https://www.boost.org/doc/libs/1_87_0/doc/html/process.html#boost_process.v1.tutorial.io  
  
This code is clearly buggy, as `c.running()` could become false right after `child` is constructed (due to os scheduling). That means, that even if the child was properly constructed and executed, it's possible to miss the output produced by the child process in case if the child process completes faster than the control gets back to the program.  
  
To test the theory, I added a simple sleep(100ms) before the reader loop, and this confirmed the theory, suddenly I stopped getting any output:  
  
```  
std::vector<std::string> read_outline(std::string & file)  
{  
    bp::ipstream is; //reading pipe-stream  
    bp::child c(bp::search_path("nm"), file, bp::std_out > is);  
  
    std::vector<std::string> data;  
    std::string line;  
  
    std::this_thread::sleep_for(100ms); // added sleep  
  
    for (c.running() && std::getline(is, line) && !line.empty())  
        data.push_back(line);  
  
    c.wait();  
    return data;  
}  
```  
  
So, what's the proper way to read `stdout` then?

---

## Comment 1

> Username: pps83  
> Created at: 2024-12-18 22:40:27 UTC  
> Updated at: 2024-12-18 22:53:50 UTC  
> Url: https://github.com/boostorg/process/issues/441#issuecomment-2552405936  

Note, that this buggy code not only gets used all over the place and pushed as the way to do it, but also all the AI tools now spread this if you ask them to write `boost::process::child` code.

---

## Comment 2

> Username: klemens-morgenstern  
> Created at: 2024-12-19 00:09:48 UTC  
> Url: https://github.com/boostorg/process/issues/441#issuecomment-2552504995  

Using asynchronous IO is the way to go. Updates docs & deprecation of v1 are in the works.

---

## Comment 3

> Username: pps83  
> Created at: 2024-12-19 10:40:10 UTC  
> Url: https://github.com/boostorg/process/issues/441#issuecomment-2553383304  

There is nothing wrong with synchronous io if one wants to simply read stdout. Requiring to use asio for that is overkill. I'd rather prefer seeing possibility to create process::child using proper methods (instead of overloads and syntactic sugar for child ctor) which already [makes basic code painful](https://stackoverflow.com/questions/73413456/conditionally-adding-arguments-to-a-constructor-c).  
  
In my case all I wanted to is to be able to create child process where based on function param I would get stdout, or stderr. I had to do this:  
```  
    std::error_code ec;  
    bp::child c;  
    if (readStderr)  
        c = bp::child(cmd, bp::std_err > out, ec);  
    else  
        c = bp::child(cmd, bp::std_out > out, ec);  
```  
  
imo, this stinks that I'm not doing something right. After digging, appears that's the way it is  
  
> Using asynchronous IO is the way to go. Updates docs & deprecation of v1 are in the works.  
  
all the AI tools will be stuck suggesting buggy code because of that. They always do, and only if one knows and asks AI to check if the code is ok, they actually fix it, but by default this bug gets pushed on to every user. And they aren't wrong, the code was bad from day one on the official docs, and it still stays this way.

---

## Comment 4

> Username: klemens-morgenstern  
> Created at: 2024-12-19 11:02:28 UTC  
> Url: https://github.com/boostorg/process/issues/441#issuecomment-2553426862  

Again, I'll deprecate the v1 API in the next release, and you'll need do this:  
  
```cpp  
boost::system::error_code ec;  
boost::asio::io_context ctx;  
bp::process c;  
if (readStderr)  
    c = bp::process(ctx, exe, args, bp::process_stdio{.err= out}, ec);  
else  
    c = bp::process(ctx, exe, args, bp::process_stdio{.out=out}, ec);  
```  
  
I also couldn't care less what AI tools generate.

---

## Comment 5

> Username: pps83  
> Created at: 2024-12-19 11:19:46 UTC  
> Updated at: 2024-12-19 11:37:52 UTC  
> Url: https://github.com/boostorg/process/issues/441#issuecomment-2553458816  

> I also couldn't care less what AI tools generate.  
  
Well, that's the modern way to search how to use a lib. You could google and waste tons of time (you'd still get the same buggy code), or you could ask grok/meta whatever to write basic function that shows how to use it. That's all. That bad code gets indexed all over the place now.  
  
Is it possible to do this instead: `bp::process c(ctx, exe, args, readStderr ? bp::process_stdio{.err= out} : bp::process_stdio{.out=out}, ec);`. If both of these result in a `bp::process_stdio` then this should work.  
  
  
```  
 if (readStderr)  
     c = bp::process(ctx, exe, args, bp::process_stdio{.err= out}, ec);  
 else  
     c = bp::process(ctx, exe, args, bp::process_stdio{.out=out}, ec);  
```  
This still leaves one to create N! versions of ctor if they want to optionally have some params or not. Many people would appreciate if there was a way to set these configurations using proper readable methods, instead of syntactic sugar. This is nonsense `bp::process_stdio{.err= out}`, should also have simple method (something like `.set_stream(stdout, out)` followed by `.run()` or `.start()`).  
  
There is also intellisense (or whatever it's called) with `boost::process` you get pretty much nothing useful on how to use code from the available methods, as most of the configuration is buried in ctor  
![Image](https://github.com/user-attachments/assets/0c87b0dd-fda6-451d-a1bb-45e7183819d4)

---

## Comment 6

> Username: klemens-morgenstern  
> Created at: 2024-12-19 12:37:36 UTC  
> Url: https://github.com/boostorg/process/issues/441#issuecomment-2553725997  

Yes, `process_stdio` is a type. I got rid of all the expression templates from `v1`. So the ternary operator should work.  
  
Also this:  
  
```cpp  
process_stdio my_io;  
my_io.out = out;  
process proc(..., std::move(out));  
```  
  
The initialization parameters of a process are an open set, i.e. there are more parameters you might want to apply at process launch that I don't know about. Therefore the constructor takes a variadic set of initializers.   
  
I've discussed this design too many times at this point and am not interested in another discussion. Thank you for your input.

---

## Comment 7

> Username: pps83  
> Created at: 2024-12-19 16:06:21 UTC  
> Updated at: 2024-12-19 16:13:00 UTC  
> Url: https://github.com/boostorg/process/issues/441#issuecomment-2554825184  

I'm having hard time to make it work (your examples, or any v2 samples). Perhaps, that's the approach in boost, but it would be really really nice if quickstart or tutorial samples actually listed code that compiles (with required includes).   
Crazy part, is that v1 already works for me. Replacing code with v2 version breaks it.  
Even code generated with AI is broken using v2 (I use boost 1.86 though)
