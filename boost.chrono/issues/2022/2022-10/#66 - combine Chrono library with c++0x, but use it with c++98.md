# #66 - [1.80] combine Chrono library with c++0x, but use it with c++98 [Open]

> Username: youngwolf-project  
> Created at: 2022-10-04 11:07:29 UTC  
> Updated at: 2022-10-04 11:07:29 UTC  
> Url: https://github.com/boostorg/chrono/issues/66  

I built Boost without any specific customization, which is:  
./bootstrap  
./b2  
  
Before 1.80, following codes can work properly (-std=c++98), but in 1.80, it must be compiled with -std=c++0x (or heigher) or with macro BOOST_CHRONO_HEADER_ONLY, otherwise, the timer can never expire. My query is -- is this a bug or right behavior by design?  
  
```c++  
void timer_handler(const boost::system::error_code& ec, const boost::shared_ptr<boost::asio::system_timer>&)  
{  
    puts("receive a timeout event.");  
}  
void main()  
{  
    boost::asio::io_context ctx[4];  
    for (int i = 0; i < 16; ++i)  
    {  
        BOOST_AUTO(timer, boost::make_shared<boost::asio::system_timer>(ctx[i % 4].get_executor()));  
        timer->expires_after(boost::chrono::milliseconds(50));  
        timer->async_wait(boost::bind(&timer_handler, boost::asio::placeholders::error, timer));  
    }  
    boost::thread_group tg;  
    for (int i = 0; i < 4; ++i)  
        tg.create_thread(boost::bind(&boost::asio::io_context::run, &ctx[i]));  
    tg.join_all();  
}  
```
