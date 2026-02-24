# #46 - The posix implementation of wait_until, when the wait time is too long and the child process does not exit, the CPU usage is too high to reach 100%. [Closed]

> Username: boostprox  
> Created at: 2018-07-13 07:19:53 UTC  
> Updated at: 2019-04-10 04:12:06 UTC  
> Closed at: 2019-04-10 04:12:06 UTC  
> Url: https://github.com/boostorg/process/issues/46  

**【Problem Description】**  
In the Linux operating system, after the child process is created, the wait_for(const std::chrono::duration<Rep, Period>& rel_time) function is called, and the rel_time is set too large and the child process does not exit within the set time. wait_for() The function will cause the CPU usage to be too high to reach 100%.  
Wait_until() implements the use of do{}while(); is there any optimization space?  
**【Code location】**  
process/include/boost/process/detail/posix/wait_for_exit.hpp  
  
template< class Clock, class Duration >  
inline bool wait_until(  
        const child_handle &p,  
        int & exit_code,  
        const std::chrono::time_point<Clock, Duration>& time_out,  
        std::error_code & ec) noexcept  
{  
    pid_t ret;  
    int status;  
  
    bool timed_out;  
  
    do  
    {  
        ret = ::waitpid(p.pid, &status, WNOHANG);  
        if (ret == 0)  
        {  
            timed_out = Clock::now() >= time_out;  
            if (timed_out)  
                return false;  
        }  
    }  
    while ((ret == 0) ||  
          (((ret == -1) && errno == EINTR) ||  
           ((ret != -1) && !WIFEXITED(status) && !WIFSIGNALED(status))));  
  
    if (ret == -1)  
        ec = boost::process::detail::get_last_error();  
    else  
    {  
        ec.clear();  
        exit_code = status;  
    }  
  
    return true;  
}  
  
**【scenes to be used】**  
...  
pusher_ = std::make_shared<boost::process::child>(exec_cmd_/*,pubsher_handler(*this)*/);  
 const auto& wait_ret = pusher_->wait_for(std::chrono::seconds(1000));  
 if (wait_ret)  
{  
                    int exit_code = pusher_->exit_code();  
 }  
...

---

## Comment 1

> Username: klemens-morgenstern  
> Created at: 2018-07-13 08:12:42 UTC  
> Url: https://github.com/boostorg/process/issues/46#issuecomment-404760754  

It seems to run into an infinite loop, though I'M not sure what happens. Could you check what the return code is? Just use `::waitpid` on `child::native_handleI()". I fear the condition of the while loop is incorrect.

---

## Comment 2

> Username: boostprox  
> Created at: 2018-07-13 10:25:15 UTC  
> Url: https://github.com/boostorg/process/issues/46#issuecomment-404793179  

**[test demo]**  
int main(int argc, char** argv)  
{  
    std::string cmd  
    {  
        "/home/songlei/work/mss/bin/ffmpeg -i rtsp://admin:intedio123@192.168.2.153/h264/ch33/main/av_stream  -c copy -f flv rtmp://192.168.2.41:9091/mss/8000"  
    };  
  
    boost::process::child ch(cmd);  
    ch.wait_for(std::chrono::seconds(10000));  
    return 0;  
}  
![2018-07-13_182127](https://user-images.githubusercontent.com/26051046/42686941-05905822-86ca-11e8-8ad3-6307eab0a227.jpg)

---

## Comment 3

> Username: klemens-morgenstern  
> Created at: 2018-07-13 11:31:07 UTC  
> Url: https://github.com/boostorg/process/issues/46#issuecomment-404807013  

That does not help, because I have no idea what state the ffmpeg process is in.

---

## Comment 4

> Username: boostprox  
> Created at: 2018-07-13 11:49:25 UTC  
> Url: https://github.com/boostorg/process/issues/46#issuecomment-404810545  

FFmpeg is pushing RTMP, the process state is R or S, I want to stop all processes after Wait 10000S, but the CPU usage in the Wait_for time reaches 100%.

---

## Comment 5

> Username: klemens-morgenstern  
> Created at: 2018-07-13 12:29:20 UTC  
> Url: https://github.com/boostorg/process/issues/46#issuecomment-404818947  

OH right, I remember now, there is no `waitpid` with a timeout. If you have an idea how to do that, I'd be very grateful, I couldn't come up with one yet.

---

## Comment 6

> Username: klemens-morgenstern  
> Created at: 2019-04-10 04:12:06 UTC  
> Url: https://github.com/boostorg/process/issues/46#issuecomment-481526003  

It's implemented with `sigtimedwait` now.
