# #438 - Segfault when importing Python Module [Open]

> Username: paulbourelly999  
> Created at: 2024-05-21 19:13:46 UTC  
> Updated at: 2024-05-21 20:26:22 UTC  
> Url: https://github.com/boostorg/python/issues/438  

Hello, I am attempting to wrap a C++ class using boost python into a python includable module. I am using CMake instead of bjam to build the project but I am running into a segfault when I attempt to include import the generated shared library into a python script.   
Below is the class as well as the boost python wrapper code and the python script I am using to test this. The entire branch can be found [here ](https://github.com/usdot-fhwa-stol/carma-time-lib/tree/tt-69-boost-python-module)  
```cpp  
/*  
 * Copyright (C) 2023 LEIDOS.  
 *  
 * Licensed under the Apache License, Version 2.0 (the "License"); you may not  
 * use this file except in compliance with the License. You may obtain a copy of  
 * the License at  
 *  
 * http://www.apache.org/licenses/LICENSE-2.0  
 *  
 * Unless required by applicable law or agreed to in writing, software  
 * distributed under the License is distributed on an "AS IS" BASIS, WITHOUT  
 * WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied. See the  
 * License for the specific language governing permissions and limitations under  
 * the License.  
 */  
  
#pragma once  
  
#include <memory>  
#include <condition_variable>  
#include <mutex>  
#include <vector>  
  
  
namespace fwha_stol::lib::time {  
  
/** Alias for a timestamp in seconds. */  
using timeStampSeconds = uint32_t;  
/** Alias for a timestamp in milliseconds. */  
using timeStampMilliseconds = uint64_t;  
using sleepCVPair = std::pair<std::shared_ptr<std::condition_variable>, std::shared_ptr<std::mutex>>;  
using sleepValuePair = std::pair<timeStampMilliseconds, sleepCVPair>;  
  
/**  
 * @brief Provides access to current time and various utilities. If a simulation is enabled, all time-related  
 * functions reflect simulation time. Otherwise system time (via std::chrono::system_clock) is utilized.  
 *  
 * No interpolation is performed; the most recently received simulation time point is considered to  
 * be current.  
 */  
class CarmaClock {  
public:  
    /**  
     * @brief Create a clock instance using read time mode (default) or simulation mode.  
     * @param simulation_mode bool flag to indicate whether clock is in simulation mode.  
     */  
    explicit CarmaClock(bool simulation_mode = false);  
    /**  
     * @brief Destructor  
    */  
    ~CarmaClock() = default;  
    // Public move constructor and move assignment operator  
    CarmaClock(const CarmaClock&) = delete; // Deleted copy constructor  
    CarmaClock& operator=(const CarmaClock&) = delete; // Deleted copy assignment operator  
    CarmaClock(CarmaClock&&) = delete; // Deleted move constructor  
    CarmaClock& operator=(CarmaClock&&) = delete; // Deleted move assignment operator  
  
public:  
    /**  
     * @brief Get the current time in seconds.  
     * @return epoch time in seconds as a uint32_t.  
    */  
    timeStampSeconds nowInSeconds() const;  
    /**  
     * @brief Get the current time in milliseconds.  
     * @return epoch time in milliseconds as a uint64_t.  
    */  
    timeStampMilliseconds nowInMilliseconds() const;  
  
public:  
    /**  
     * @brief Update the clock time. First call to update will initialize the carma-clock  
     * @attention only valid when in simulation mode.  
     * @param current_time in milliseconds to update the clock to.  
     * @throws std::invalid_argument exception if called while carma-clock is not in simulation  
     * mode.  
    */  
    void update(timeStampMilliseconds current_time);  
  
public:  
      
    /**  
     * @brief Return whether this instance is in simulation mode.  
    */  
    inline bool is_simulation_mode() const {return _is_simulation_mode; };  
  
    /**  
     * @brief Will block calling thread until carma-clock has been initiliazed   
     * with the first call to update.   
     * @attention Only relavent when clock is in simulation mode. If not, will  
     * immediately unblock.  
    */  
    void wait_for_initialization();  
    /**  
     * @brief Sleep until future time.  
     * @param future_time epoch timestamp in milliseconds to sleep until.  
     * @throw std::invalid_argument if carma-clock is not initialized.  
     */  
    void sleep_until(timeStampMilliseconds future_time);  
  
    /**  
     * @brief Sleep for time_to_sleep in milliseconds.  
     * @param time_to_sleep milliseconds from current time to sleep for.  
    */  
    void sleep_for(timeStampMilliseconds time_to_sleep);  
  
private:  
    // Current simulation time  
    timeStampMilliseconds _current_time = 0;  
  
    // Simulation mode  
    bool _is_simulation_mode;  
  
    // Initialization variables  
    bool _is_initialized;  
    // Mutex for conditional variable  
    std::mutex _initialization_mutex;  
    // Conditional_variable used to block one or multiple threads until   
    // carma-clock object is initialized. On initialization all threads are   
    // notified.  
    std::condition_variable _initialization_cv;  
  
    // sleep related items  
    // Mutex for conditional variable  
    std::mutex _sleep_mutex;  
    // A vector of time, mutex and conditional variables used to  
    // block calling threads until a certain time update. Each call   
    // to sleep_until, creates a new entry that includes a time, a conditional   
    // variable and a mutex for the conditional variable. On each incoming time update  
    // , all vector entries with times smaller or equal to the incoming update are notified.   
    // NOTE: Each entry is only expected to have a single thread waiting on it so the relevant  
    // time update will only notify a single thread for each entry. Multiple threads waiting on   
    // the same time should each have their one entry in this vector.  
    std::vector <sleepValuePair> _sleep_holder;  
};  
  
}  
  
```  
  
```cpp  
#include <boost/python.hpp>  
#include <boost/python/def.hpp>  
#include "carma_clock.h"  
  
BOOST_PYTHON_MODULE(libcarma_clockd) {  
    using namespace boost::python;  
    class_<fwha_stol::lib::time::CarmaClock, boost::noncopyable>("CarmaClock", init<bool>(arg("simulation_mode")=false))  
        .def("nowInSeconds", &fwha_stol::lib::time::CarmaClock::nowInSeconds)  
        .def("nowInMilliseconds", &fwha_stol::lib::time::CarmaClock::nowInMilliseconds)  
        .def("update", &fwha_stol::lib::time::CarmaClock::update,args("current_time"))  
        .def("is_simulation_mode", &fwha_stol::lib::time::CarmaClock::is_simulation_mode)  
        .def("wait_for_initialization",&fwha_stol::lib::time::CarmaClock::wait_for_initialization)  
        .def("sleep_until",&fwha_stol::lib::time::CarmaClock::sleep_until,args("future_time"))  
        .def("sleep_for",&fwha_stol::lib::time::CarmaClock::sleep_for,args("time_to_sleep"));  
        init_module_libcarma_clockd();  
}  
```  
  
  
```python  
#!/usr/bin/python3  
  
import libcarma_clockd  
print("Creating Clock Object")  
  
clock = libcarma_clockd.CarmaClock(False)  
```  
  
I suspect this is either related to our constructor with default parameters or our use of std::thread or std::mutex. I am unsure how to continue debugging this issue other than looking at the core dumb with gdb. Any help would be greatly appreciated.  
  
This is currently being built in Jammy dev containers using boost 1.74 and cmake version 3.27.7

---

## Comment 1

> Username: paulbourelly999  
> Created at: 2024-05-21 20:26:21 UTC  
> Url: https://github.com/boostorg/python/issues/438#issuecomment-2123385745  

Update, here is the trace I found when analyzing the core dumb with gdb  
```  
Reading symbols from libcarma_clockd.so...  
  
warning: core file may not match specified executable file.  
[New LWP 67622]  
[Thread debugging using libthread_db enabled]  
Using host libthread_db library "/lib/x86_64-linux-gnu/libthread_db.so.1".  
Core was generated by `python3 python_wrapper_test.py'.  
Program terminated with signal SIGSEGV, Segmentation fault.  
#0  0x00007c4c37b73661 in boost::tuples::cons<char const*, boost::tuples::cons<void (boost::python::detail::not_specified::*)(), boost::tuples::null_type> >::cons<boost::tuples::null_type const, boost::tuples::null_type const, boost::tuples::null_type const, boost::tuples::null_type const, boost::tuples::null_type const, boost::tuples::null_type const, boost::tuples::null_type const, boost::tuples::null_type const, boost::tuples::null_type const> (this=0x7ffde466d600, t2=..., t3=..., t4=..., t5=..., t6=..., t7=...,   
--Type <RET> for more, q to quit, c to continue without paging--  
    t8=<error reading variable: Cannot access memory at address 0x7ffde466cff8>, t9=<error reading variable: Cannot access memory at address 0x7ffde466cff0>,   
    t10=<error reading variable: Cannot access memory at address 0x7ffde466cfe8>) at /usr/include/boost/tuple/detail/tuple_basic.hpp:307  
307       cons( const null_type& /*t1*/, T2& t2, T3& t3, T4& t4, T5& t5,  
(gdb) bt 10  
#0  0x00007c4c37b73661 in boost::tuples::cons<char const*, boost::tuples::cons<void (boost::python::detail::not_specified::*)(), boost::tuples::null_type> >::cons<boost::tuples::null_type const, boost::tuples::null_type const, boost::tuples::null_type const, boost::tuples::null_type const, boost::tuples::null_type const, boost::tuples::null_type const, boost::tuples::null_type const, boost::tuples::null_type const, boost::tuples::null_type const> (this=0x7ffde466d600, t2=..., t3=..., t4=..., t5=..., t6=..., t7=...,   
    t8=<error reading variable: Cannot access memory at address 0x7ffde466cff8>, t9=<error reading variable: Cannot access memory at address 0x7ffde466cff0>,   
    t10=<error reading variable: Cannot access memory at address 0x7ffde466cfe8>) at /usr/include/boost/tuple/detail/tuple_basic.hpp:307  
#1  0x00007c4c37b7300f in boost::tuples::cons<boost::python::detail::keywords<0ul>, boost::tuples::cons<char const*, boost::tuples::cons<void (boost::python::detail::not_specified::*)(), boost::tuples::null_type> > >::cons<boost::tuples::null_type const, boost::tuples::null_type const, boost::tuples::null_type const, boost::tuples::null_type const, boost::tuples::null_type const, boost::tuples::null_type const, boost::tuples::null_type const, boost::tuples::null_type const, boost::tuples::null_type const> (this=0x7ffde466d5f8, t2=...,   
    t3=..., t4=..., t5=..., t6=..., t7=..., t8=..., t9=..., t10=...) at /usr/include/boost/tuple/detail/tuple_basic.hpp:310  
#2  0x00007c4c37b729c7 in boost::tuples::cons<boost::python::default_call_policies, boost::tuples::cons<boost::python::detail::keywords<0ul>, boost::tuples::cons<char const*, boost::tuples::cons<void (boost::python::detail::not_specified::*)(), boost::tuples::null_type> > > >::cons<boost::tuples::null_type const, boost::tuples::null_type const, boost::tuples::null_type const, boost::tuples::null_type const, boost::tuples::null_type const, boost::tuples::null_type const, boost::tuples::null_type const, boost::tuples::null_type const, boost::tuples::null_type const> (this=0x7ffde466d5f0, t2=..., t3=..., t4=..., t5=..., t6=..., t7=..., t8=..., t9=..., t10=...) at /usr/include/boost/tuple/detail/tuple_basic.hpp:310  
#3  0x00007c4c37b72192 in boost::tuples::cons<boost::python::detail::not_specified const&, boost::tuples::cons<boost::python::default_call_policies, boost::tuples::cons<boost::python::detail::keywords<0ul>, boost::tuples::cons<char const*, boost::tuples::cons<void (boost::python::detail::not_specified::*)(), boost::tuples::null_type> > > > >::cons<boost::python::detail::not_specified const, boost::tuples::null_type const, boost::tuples::null_type const, boost::tuples::null_type const, boost::tuples::null_type const, boost::tuples::null_type const, boost::tuples::null_type const, boost::tuples::null_type const, boost::tuples::null_type const, boost::tuples::null_type const> (this=0x7ffde466d5e8, t1=..., t2=..., t3=...,   
    t4=..., t5=..., t6=..., t7=..., t8=..., t9=..., t10=...) at /usr/include/boost/tuple/detail/tuple_basic.hpp:302  
#4  0x00007c4c37b71b4a in boost::tuples::cons<boost::python::detail::not_specified const&, boost::tuples::cons<boost::python::detail::not_specified const&, boost::tuples::cons<boost::python::default_call_policies, boost::tuples::cons<boost::python::detail::keywords<0ul>, boost::tuples::cons<char const*, boost::tuples::cons<void (boost::python::detail::not_specified::*)(), boost::tuples::null_type> > > > > >::cons<boost::python::detail::not_specified const, boost::python::detail::not_specified const, boost::tuples::null_type const, boost::tuples::null_type const, boost::tuples::null_type const, boost::tuples::null_type const, boost::tuples::null_type const, boost::tuples::null_type const, boost::tuples::null_type const, boost::tuples::null_type const> (this=0x7ffde466d5e0, t1=..., t2=..., t3=..., t4=..., t5=..., t6=..., t7=..., t8=..., t9=..., t10=...)  
    at /usr/include/boost/tuple/detail/tuple_basic.hpp:302  
--Type <RET> for more, q to quit, c to continue without paging--  
#5  0x00007c4c37b71422 in boost::tuples::cons<boost::python::detail::not_specified const&, boost::tuples::cons<boost::python::detail::not_specified const&, boost::tuples::cons<boost::python::detail::not_specified const&, boost::tuples::cons<boost::python::default_call_policies, boost::tuples::cons<boost::python::detail::keywords<0ul>, boost::tuples::cons<char const*, boost::tuples::cons<void (boost::python::detail::not_specified::*)(), boost::tuples::null_type> > > > > > >::cons<boost::python::detail::not_specified const, boost::python::detail::not_specified const, boost::python::detail::not_specified const, boost::tuples::null_type const, boost::tuples::null_type const, boost::tuples::null_type const, boost::tuples::null_type const, boost::tuples::null_type const, boost::tuples::null_type const, boost::tuples::null_type const> (this=0x7ffde466d5d8, t1=..., t2=..., t3=..., t4=..., t5=..., t6=...,   
    t7=..., t8=..., t9=..., t10=...) at /usr/include/boost/tuple/detail/tuple_basic.hpp:302  
#6  0x00007c4c37b70cac in boost::tuples::cons<char const* const&, boost::tuples::cons<boost::python::detail::not_specified const&, boost::tuples::cons<boost::python::detail::not_specified const&, boost::tuples::cons<boost::python::detail::not_specified const&, boost::tuples::cons<boost::python::default_call_policies, boost::tuples::cons<boost::python::detail::keywords<0ul>, boost::tuples::cons<char const*, boost::tuples::cons<void (boost::python::detail::not_specified::*)(), boost::tuples::null_type> > > > > > > >::cons<char const* const, boost::python::detail::not_specified const, boost::python::detail::not_specified const, boost::python::detail::not_specified const, boost::tuples::null_type const, boost::tuples::null_type const, boost::tuples::null_type const, boost::tuples::null_type const, boost::tuples::null_type const, boost::tuples::null_type const> (this=0x7ffde466d5d0,   
    t1=@0x7ffde466d760: 0x0, t2=..., t3=..., t4=..., t5=..., t6=..., t7=..., t8=..., t9=..., t10=...) at /usr/include/boost/tuple/detail/tuple_basic.hpp:302  
#7  0x00007c4c37b7047c in boost::tuples::tuple<char const* const&, boost::python::detail::not_specified const&, boost::python::detail::not_specified const&, boost::python::detail::not_specified const&, boost::python::default_call_policies, boost::python::detail::keywords<0ul>, char const*, void (boost::python::detail::not_specified::*)(), boost::tuples::null_type, boost::tuples::null_type>::tuple (this=0x7ffde466d5d0, t0=@0x7ffde466d760: 0x0, t1=..., t2=..., t3=...) at /usr/include/boost/tuple/detail/tuple_basic.hpp:515  
#8  0x00007c4c37b6fa6f in boost::python::detail::def_helper<char const*, boost::python::detail::not_specified, boost::python::detail::not_specified, boost::python::detail::not_specified>::def_helper (this=0x7ffde466d5d0, a1=@0x7ffde466d760: 0x0) at /usr/include/boost/python/detail/def_helper.hpp:165  
#9  0x00007c4c37b73580 in boost::python::class_<fwha_stol::lib::time::CarmaClock, boost::noncopyable_::noncopyable, boost::python::detail::not_specified, boost::python::detail::not_specified>::def_maybe_overloads<boost::python::api::object, char const*> (this=0x7ffde466d958, name=0x7c4c37b783e1 "__init__", fn=..., a1=@0x7ffde466d760: 0x0)  
    at /usr/include/boost/python/class.hpp:566  
```
