# #24 Improve windows timer precision when dealing with duration < 1ms with a CPU trade-off [Closed]

> Username: securesocketfunneling  
> Created at: 2015-09-03 15:25:47 UTC  
> Updated at: 2015-09-21 09:38:53 UTC  
> Closed at: 2015-09-21 09:38:53 UTC  
> Url: https://github.com/boostorg/asio/pull/24  

Hi Chris,  
  
While developing our [boost.asio stream socket based on UDT protocol](https://github.com/securesocketfunneling/udt), we encountered performance issues using asio timers on Windows.  
[UDT protocol](http://udt.sourceforge.net/) adjusts numerous critical variables for its internal dataflow  and bandwidth management using micro timing events.  
  
The proposed patch improves asio timer precision when dealing with short wait duration (< 1 ms) with a CPU trade-off, activable using macro definition "BOOST_ASIO_ENABLE_CPU_TIMER_TRADE_OFF".  
  
On UDT implementation, this patch leads to a dramatically increased bandwidth (from 10-15Mbit/s up to 300Mbit/s approx).  
Without it, boost asio's timer could not be used when "high" precision is required.  
  
What do you think about it ?  
  
Please, [find enclosed](https://github.com/securesocketfunneling/udt/blob/develop/src/timer_benchmark/main.cpp) a test program that can be used to get an idea of patch's impact on sub-millisecond timers.  
  
Here are some results we get :  
#### Without patch :  
  
```  
Number of samples    : 10000  
Expected wait (usec) : 800  
Mean (usec)          : 981.227  
Median (usec)        : 975.97  
Min (usec)           : 813  
Max (usec)           : 1994  
  
Number of samples    : 10000  
Expected wait (usec) : 500  
Mean (usec)          : 975.899  
Median (usec)        : 975.731  
Min (usec)           : 745  
Max (usec)           : 1488  
  
Number of samples    : 10000  
Expected wait (usec) : 100  
Mean (usec)          : 975.812  
Median (usec)        : 976.612  
Min (usec)           : 421  
Max (usec)           : 1230  
  
Number of samples    : 10000  
Expected wait (usec) : 50  
Mean (usec)          : 975.373  
Median (usec)        : 975.895  
Min (usec)           : 179  
Max (usec)           : 1233  
  
Number of samples    : 10000  
Expected wait (usec) : 10  
Mean (usec)          : 972.895  
Median (usec)        : 975.931  
Min (usec)           : 59  
Max (usec)           : 1270  
```  
#### With patch :  
  
```  
Number of samples    : 10000  
Expected wait (usec) : 800  
Mean (usec)          : 812.062  
Median (usec)        : 808.066  
Min (usec)           : 803  
Max (usec)           : 1078  
  
Number of samples    : 10000  
Expected wait (usec) : 500  
Mean (usec)          : 511.158  
Median (usec)        : 507.992  
Min (usec)           : 503  
Max (usec)           : 831  
  
Number of samples    : 10000  
Expected wait (usec) : 100  
Mean (usec)          : 106.495  
Median (usec)        : 105  
Min (usec)           : 103  
Max (usec)           : 324  
  
Number of samples    : 10000  
Expected wait (usec) : 50  
Mean (usec)          : 59.3095  
Median (usec)        : 58.0607  
Min (usec)           : 53  
Max (usec)           : 304  
  
Number of samples    : 10000  
Expected wait (usec) : 10  
Mean (usec)          : 18.5015  
Median (usec)        : 18  
Min (usec)           : 14  
Max (usec)           : 140  
```

---

## Comment 1

> Username: haegele-tv  
> Created_at: 2015-09-09 08:20:29 UTC  
> Url: https://github.com/boostorg/asio/pull/24#issuecomment-138821776  

I did test you sample program on my machine and did not get anywhere as close results as you have. Did you change something in windows to get more accurate timers?  
  
My system is Windows 8.1 (x64). I compiled your test-program with Visual Studio 2013 for x64. I did not patch boost::asio.  
  
My results look more like this:  
Number of samples    : 1000  
Expected wait (usec) : 800  
Mean (usec)          : 6497.39  
Median (usec)        : 3144.56  
Min (usec)           : 804  
Max (usec)           : 16514  
  
In my experience you can't rely that the timer is more accurate than 15ms.

---

## Comment 2

> Username: securesocketfunneling  
> Created_at: 2015-09-10 08:37:45 UTC  
> Url: https://github.com/boostorg/asio/pull/24#issuecomment-139168437  

We did not change anything in Windows settings.  
But thanks to your comment, we [found out](https://randomascii.wordpress.com/2013/07/08/windows-timer-resolution-megawatts-wasted) that an other app (VirtualBox) lowered the global timer resolution to around 1ms.  
  
Anyway, it would be great to have a way to improve asio timer resolution when deadling with short wait duration.

---

## Comment 3

> Username: haegele-tv  
> Created_at: 2015-09-10 09:24:11 UTC  
> Url: https://github.com/boostorg/asio/pull/24#issuecomment-139181076  

Because of the said limitation I would vote against the your change in boost::asio.  
  
For your problem (10-15Mbit/s vs 300Mbit/s) I would suggest you do your timer like now and check after you got called how much time really passed and "repair" the number of packets you wanted to send. This is not optimal, but will work "okay" on all timer resolutions. You can profit from good timers (like 1ms) and also work "okay" on bad timers (like 15ms).  
You cannot rely that Timers are as accurate as 1ms or even lower. Even if you do busy-loops you will get latency spikes over several Milliseconds (or even more) because of the Thread-scheduler. Busy-loops are always a bad idea in user-mode (you won't have a priority boost for the thread priority).

---

## Comment 4

> Username: securesocketfunneling  
> Created_at: 2015-09-17 09:16:54 UTC  
> Url: https://github.com/boostorg/asio/pull/24#issuecomment-141019563  

Well, we understand your point.  
However, your suggestion cannot be used in this protocol as the regular packets burst every Xms will invalidate some internal statistics used to regulate traffic.  
  
For testing purpose, we will implement a new timer service based on busy loop and see if it is conclusive.

---

## Comment 5

> Username: securesocketfunneling  
> Created_at: 2015-09-21 09:38:53 UTC  
> Url: https://github.com/boostorg/asio/pull/24#issuecomment-141925267  

Looking for another solution (#25)

---
