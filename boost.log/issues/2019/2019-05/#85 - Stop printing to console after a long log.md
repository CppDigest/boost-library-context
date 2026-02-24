# #85 - Stop printing to console after a long log [Closed]

> Username: ououmania  
> Created at: 2019-05-21 08:05:56 UTC  
> Updated at: 2019-05-27 02:55:54 UTC  
> Closed at: 2019-05-23 12:27:53 UTC  
> Url: https://github.com/boostorg/log/issues/85  

Hi, I follow the example code and write my log to both console and a file.  
If the log record is short, say at most several hundred bytes, everything works well.  
However, after a long log is written, the console side stop working(the following logs were not printed) but the file works with no problem. I checked the file and found the long log was truncated.   
In my case, the long log is 19985 bytes long.  
The link of the example is:  
https://www.boost.org/doc/libs/1_58_0/libs/log/doc/html/log/detailed/sink_backends.html  
Any help is appreciated.

---

## Comment 1

> Username: Lastique  
> Created at: 2019-05-21 08:47:37 UTC  
> Url: https://github.com/boostorg/log/issues/85#issuecomment-494299916  

1. What system are you runnung? OS, compiler, etc.  
2. What Boost version are you using? Can you reproduce it with the latest version?  
3. Can you provide a small compilable code sample that reproduces the problem?

---

## Comment 2

> Username: ououmania  
> Created at: 2019-05-23 09:48:19 UTC  
> Url: https://github.com/boostorg/log/issues/85#issuecomment-495148654  

I'm sorry for the incomplete information.   
I am using boost-1.70 on archlinux with gcc version 8.3  
I used gnu-readline to read some input and boost-log to print the result.   
For short  log, things worked with no problem. But for a long one, the text on the terminal was truncated and there was no log printed to the terminal afterwards.  
If boost-log was changed to printf(std::cout), the long log was also truncated but the log afterwards was still printed.  
However, I tried boost-log separately with the long log, there was no problem.  
I guess gnu-readline may change the terminal buffer. But I have no idea why this make boost-log stop working.   
I will put the minimal piece of code soon.

---

## Comment 3

> Username: ououmania  
> Created at: 2019-05-23 12:27:53 UTC  
> Updated at: 2019-05-23 12:28:11 UTC  
> Url: https://github.com/boostorg/log/issues/85#issuecomment-495197501  

I replaced the following code with boost::log::add_console_log() and it worked even if the long log was still truncated. I don't know why...  
`        
      auto backend = boost::make_shared< sinks::text_ostream_backend >();                                                                
  
      backend->add_stream(  
          boost::shared_ptr< std::ostream >(&std::clog, boost::null_deleter()));  
    
      using  text_sink = sinks::synchronous_sink< sinks::text_ostream_backend >;  
      boost::shared_ptr<text_sink> console(new text_sink(backend));  
      console->set_filter(severity >= LogLevel::STATUS);  
      console->locked_backend()->auto_flush(true);  
      core->add_sink(console);`

---

## Comment 4

> Username: Lastique  
> Created at: 2019-05-23 16:03:45 UTC  
> Url: https://github.com/boostorg/log/issues/85#issuecomment-495281676  

I'd still like to see a compilable example that shows the problem. If there is a problem, I'd like to fix it.

---

## Comment 5

> Username: ououmania  
> Created at: 2019-05-24 04:00:53 UTC  
> Url: https://github.com/boostorg/log/issues/85#issuecomment-495464302  

Many thanks. I have committed the minimal code.   
https://github.com/ououmania/boost-log-readline

---

## Comment 6

> Username: Lastique  
> Created at: 2019-05-25 16:41:46 UTC  
> Url: https://github.com/boostorg/log/issues/85#issuecomment-495931879  

I tried your example and it looks like this issue is somehow caused by readline. I modified `Shell::Run` so that it starts with:  
  
```  
ProcessCommad("short");  
sleep(2);  
ProcessCommad("long");  
sleep(2);  
ProcessCommad("short");  
return;  
```  
  
Then the console output contains all three commands, including the long line of `#`s.  
  
I'm not sure what readline does to cause this and what I can do to fix it. I'm not familiar with the library.

---

## Comment 7

> Username: Lastique  
> Created at: 2019-05-25 17:04:57 UTC  
> Updated at: 2019-05-25 17:08:29 UTC  
> Url: https://github.com/boostorg/log/issues/85#issuecomment-495933524  

When the long line is output, `std::clog` becomes bad (it gets `badbit` iostate). You can see that if you set `std::clog.exceptions(std::ios_base::badbit);` before running `Shell::Run`, you'll get an exception when the long line is printed. When the stream is marked with `badbit`, any output through it is ignored.  
  
I suspect that this may be caused by a file descriptor buffer overflow. E.g. if readline replaces the standard output stream with a pipe, there is a limited amount of data you can write in the pipe without reading from it, until it reports error from `write`, which results in marking `clog` with `badbit`.  
  
As a sort of workaround, you can limit the size of output by using [this](https://www.boost.org/doc/libs/1_70_0/libs/log/doc/html/log/detailed/expressions.html#log.detailed.expressions.formatters.decorators.max_size) decorator in your formatter. However, it only works on per-log record basis, and if you (or readline) is not reading from the pipe fast enough, by writing multiple long log records in a row you can still overflow the fd.  
  
Ideally, if there is a way to tell readline not to replace stdout and stderr, that would probably be a better way to go.

---

## Comment 8

> Username: ououmania  
> Created at: 2019-05-27 02:47:47 UTC  
> Updated at: 2019-05-27 02:55:54 UTC  
> Url: https://github.com/boostorg/log/issues/85#issuecomment-496060215  

Wow, thank you!   
  
I really appreciate your effort. I think the readline library makes some change to stdout too but I spent a day searching on the web and reading its manual with no result. readline uses stdout as its default output stream, but its manual doesn't explain what it does to the output stream. I will read the library's code if I am free.  
  
As I mentioned above, changing the way in which text_sink is added to boost::log::add_console_log() works though I checked the function and didn't find any significant difference. Anyway, it works well now(I mean it continue printing log to the terminal after the truncated long log).  
  
Since I am just developing a tool for self-use only, it's okay even if the log record is truncated. I just need to know something really happens. The log file contains the whole log record after all.  
  
Thanks again.
