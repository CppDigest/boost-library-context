# #284 - Build failed on Ubuntu18.04 with armf [Closed]

> Username: potato1992  
> Created at: 2019-05-30 05:08:28 UTC  
> Updated at: 2020-04-19 03:44:23 UTC  
> Closed at: 2020-04-19 03:44:23 UTC  
> Url: https://github.com/boostorg/thread/issues/284  

Boost version 1.65, 1.70 the same result.   
Hardware: odroid XU4, armf platform  
gcc version 7.4.0 (Ubuntu/Linaro 7.4.0-1ubuntu1~18.04)  
It seems to be some functions conflict?  
Build fail, I am using rosbag.h in ros melodic , here is a part of the output:  
  
[  2%] Building CXX object CMakeFiles/ros_data_collector.dir/src/app/ros_data_collector_svr.cpp.o  
In file included from /usr/include/boost/thread/locks.hpp:10:0,  
                 from /usr/include/boost/thread.hpp:21,  
                 from /opt/ros/melodic/include/class_loader/multi_library_class_loader.hpp:35,  
                 from /opt/ros/melodic/include/pluginlib/class_loader.hpp:38,  
                 from /opt/ros/melodic/include/rosbag/bag.h:65,  
                 from /home/odroid/WindowsProjects/C++/ros_data_collector/ros_data_collector/src/app/../include/ros_data_collector_svr.h:7,  
                 from /home/odroid/WindowsProjects/C++/ros_data_collector/ros_data_collector/src/app/ros_data_collector_svr.cpp:24:  
/usr/include/boost/thread/lock_algorithms.hpp:170:43: error: macro "lock" passed 2 arguments, but takes just 1  
   void lock(MutexType1& m1, MutexType2& m2)  
                                           ^  
/usr/include/boost/thread/lock_algorithms.hpp:176:49: error: macro "lock" passed 2 arguments, but takes just 1  
   void lock(const MutexType1& m1, MutexType2& m2)  
                                                 ^  
/usr/include/boost/thread/lock_algorithms.hpp:182:49: error: macro "lock" passed 2 arguments, but takes just 1  
   void lock(MutexType1& m1, const MutexType2& m2)  
                                                 ^  
/usr/include/boost/thread/lock_algorithms.hpp:188:55: error: macro "lock" passed 2 arguments, but takes just 1  
   void lock(const MutexType1& m1, const MutexType2& m2)  
                                                       ^  
/usr/include/boost/thread/lock_algorithms.hpp:194:59: error: macro "lock" passed 3 arguments, but takes just 1  
   void lock(MutexType1& m1, MutexType2& m2, MutexType3& m3)  
                                                           ^  
/usr/include/boost/thread/lock_algorithms.hpp:221:75: error: macro "lock" passed 4 arguments, but takes just 1  
   void lock(MutexType1& m1, MutexType2& m2, MutexType3& m3, MutexType4& m4)  
                                                                           ^  
/usr/include/boost/thread/lock_algorithms.hpp:253:91: error: macro "lock" passed 5 arguments, but takes just 1  
   void lock(MutexType1& m1, MutexType2& m2, MutexType3& m3, MutexType4& m4, MutexType5& m5)  
                                                                                           ^  
/usr/include/boost/thread/lock_algorithms.hpp:366:31: error: macro "lock" passed 2 arguments, but takes just 1  
         boost::lock(begin, end);  
                               ^  
In file included from /usr/include/boost/thread/future.hpp:32:0,  
                 from /usr/include/boost/thread.hpp:24,  
                 from /opt/ros/melodic/include/class_loader/multi_library_class_loader.hpp:35,  
                 from /opt/ros/melodic/include/pluginlib/class_loader.hpp:38,  
                 from /opt/ros/melodic/include/rosbag/bag.h:65,  
                 from /home/odroid/WindowsProjects/C++/ros_data_collector/ros_data_collector/src/app/../include/ros_data_collector_svr.h:7,  
                 from /home/odroid/WindowsProjects/C++/ros_data_collector/ros_data_collector/src/app/ros_data_collector_svr.cpp:24:  
/usr/include/boost/thread/futures/wait_for_any.hpp:71:55: error: macro "lock" passed 2 arguments, but takes just 1  
           boost::lock(locks.get(), locks.get() + count);

---

## Comment 1

> Username: viboes  
> Created at: 2019-05-31 12:53:28 UTC  
> Url: https://github.com/boostorg/thread/issues/284#issuecomment-497697846  

Please add an issue to the library that is adding a macro called lock.

---

## Comment 2

> Username: viboes  
> Created at: 2019-05-31 13:00:07 UTC  
> Url: https://github.com/boostorg/thread/issues/284#issuecomment-497699744  

Any PR with a *reasonable* solution is welcome. I will not do anything from my side as I don't have access to this platform.

---

## Comment 3

> Username: potato1992  
> Created at: 2019-09-06 13:22:58 UTC  
> Updated at: 2019-09-06 13:26:11 UTC  
> Url: https://github.com/boostorg/thread/issues/284#issuecomment-528852629  

> Any PR with a _reasonable_ solution is welcome. I will not do anything from my side as I don't have access to this platform.  
  
Hello, I have moved to boost version 1.7.0 and  changed every 'lock' inside /usr/local/include/boost/thread/future.hpp   
/usr/local/include/boost/thread/lock_algorithms.hpp  
/usr/include/c++/7/mutex  
/usr/local/include/boost/thread/futures/wait_for_any.hpp  
/usr/local/include/boost/thread/pthread/thread_data.hpp  
/usr/local/include/boost/thread/barrier.hpp  
 into 'lock_', now it works.   
It seems that the lock template function declaration and variable definition conflicts with 'lock' from standard C++ library.

---

## Comment 4

> Username: potato1992  
> Created at: 2019-09-08 11:36:16 UTC  
> Url: https://github.com/boostorg/thread/issues/284#issuecomment-529194311  

> > Any PR with a _reasonable_ solution is welcome. I will not do anything from my side as I don't have access to this platform.  
>   
> Hello, I have moved to boost version 1.7.0 and changed every 'lock' inside /usr/local/include/boost/thread/future.hpp  
> /usr/local/include/boost/thread/lock_algorithms.hpp  
> /usr/include/c++/7/mutex  
> /usr/local/include/boost/thread/futures/wait_for_any.hpp  
> /usr/local/include/boost/thread/pthread/thread_data.hpp  
> /usr/local/include/boost/thread/barrier.hpp  
> into 'lock_', now it works.  
> It seems that the lock template function declaration and variable definition conflicts with 'lock' from standard C++ library.  
  
Same issue on x86  
CPU: intel i5 8250u  
System: ubuntu 18.04  
gcc version 7.4.0  
boost version:  1.7.0

---

## Comment 5

> Username: viboes  
> Created at: 2019-09-09 19:26:08 UTC  
> Url: https://github.com/boostorg/thread/issues/284#issuecomment-529629088  

Renaming lock id not reasonable solution. Truc to do as with min,max and prevent macro substitution.
