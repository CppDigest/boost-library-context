# #110 - Cancelling request fails for serialized communication [Open]

> Username: sdebionne  
> Created at: 2019-11-19 13:11:56 UTC  
> Updated at: 2020-06-19 14:13:38 UTC  
> Url: https://github.com/boostorg/mpi/issues/110  

I believe this is a regression from 1.70.  
  
In the following code, an asynchronous recv is initiated then later an other thread marks the operation for cancellation (and that should guarantee `wait()` to return according to the spec).  
  
This works for fundamental type (such as `int`) but not for serialized types.  
  
```c++  
#include <mpi.h>  
  
#include <iostream>  
#include <future>  
  
#include <boost/mpi.hpp>  
#include <boost/serialization/serialization.hpp>  
  
using namespace std::literals::chrono_literals;  
namespace mpi = boost::mpi;  
  
void async_cancel(boost::mpi::request request)  
{  
  std::this_thread::sleep_for(1s);  
  
  std::cout << "Before MPI_Cancel" << std::endl;  
  
  request.cancel();  
  
  std::cout << "After MPI_Cancel" << std::endl;  
}  
  
  
struct data  
{  
  int i;  
};  
  
template <typename Archive>  
void serialize(Archive& ar, data& t, const unsigned int version)  
{  
  ar & t.i;  
}  
  
int main(int argc, char* argv[])  
{  
  mpi::environment env(mpi::threading::level::multiple);  
  mpi::communicator world;  
    
  if (world.rank() == 0)  
  {  
    //int buffer; // WORKS  
    data buffer;  // FAILS  
    auto request = world.irecv(0, 0, buffer);  
      
    auto res = std::async(std::launch::async, &async_cancel, request);  
  
    std::cout << "Before MPI_Wait" << std::endl;  
  
    auto status = request.wait();  
  
    std::cout << "After MPI_Wait " << std::endl;  
  }  
  else  
    std::this_thread::sleep_for(2s);  
  
  return 0;  
}  
```  
  
The expected result is:  
  
```  
Before MPI_Wait  
Before MPI_Cancel  
After MPI_Cancel  
After MPI_Wait  
```

---

## Comment 1

> Username: sdebionne  
> Created at: 2019-11-19 13:45:52 UTC  
> Url: https://github.com/boostorg/mpi/issues/110#issuecomment-555514169  

checking the source in `detail/request_handlers.hpp`:  
  
```c++  
class request::probe_handler  
  ...  
  void cancel() { m_source = MPI_PROC_NULL; }  
```  
  
does not call `MPI_Cancel`. So it sounds like it is not supported yet.  
  
Before the refactoring (1.70), I had the following patch:  
  
```diff  
diff --git a/boost/mpi/communicator.hpp b/boost/mpi/communicator.hpp  
index 6e55b16..5bcd985 100644  
--- a/boost/mpi/communicator.hpp  
+++ b/boost/mpi/communicator.hpp  
@@ -1760,6 +1760,8 @@ request::handle_serialized_irecv(request* self, request_action action)  
       // Wait for the count message to complete  
       BOOST_MPI_CHECK_RESULT(MPI_Wait,  
                              (self->m_requests, &stat.m_status));  
+      if (stat.cancelled())  
+        return stat;  
       // Resize our buffer and get ready to receive its data  
       data->ia.resize(data->count);  
       BOOST_MPI_CHECK_RESULT(MPI_Irecv,  
@@ -1772,6 +1774,8 @@ request::handle_serialized_irecv(request* self, request_action action)  
     BOOST_MPI_CHECK_RESULT(MPI_Wait,  
                            (self->m_requests + 1, &stat.m_status));  
   
+    if (stat.cancelled())  
+      return stat;  
     data->deserialize(stat);  
     return stat;  
   } else if (action == ra_test) {  
@@ -1783,6 +1787,9 @@ request::handle_serialized_irecv(request* self, request_action action)  
       BOOST_MPI_CHECK_RESULT(MPI_Test,  
                              (self->m_requests, &flag, &stat.m_status));  
       if (flag) {  
+        if (stat.cancelled())  
+          return stat;  
+  
         // Resize our buffer and get ready to receive its data  
         data->ia.resize(data->count);  
         BOOST_MPI_CHECK_RESULT(MPI_Irecv,  
@@ -1797,11 +1804,22 @@ request::handle_serialized_irecv(request* self, request_action action)  
     BOOST_MPI_CHECK_RESULT(MPI_Test,  
                            (self->m_requests + 1, &flag, &stat.m_status));  
     if (flag) {  
+      if (stat.cancelled())  
+        return stat;  
       data->deserialize(stat);  
       return stat;  
     } else   
       return optional<status>();  
-  } else {  
+  }  
+  else if (action == ra_cancel) {  
+    status stat;  
+    if (self->m_requests[0] != MPI_REQUEST_NULL)  
+      BOOST_MPI_CHECK_RESULT(MPI_Cancel, (&self->m_requests[0]));  
+    if (self->m_requests[1] != MPI_REQUEST_NULL)  
+      BOOST_MPI_CHECK_RESULT(MPI_Cancel, (&self->m_requests[0]));  
+    return optional<status>();  
+  }  
+  else {  
     return optional<status>();  
   }  
 }  
```  
  
Something similar might be required.

---

## Comment 2

> Username: sdebionne  
> Created at: 2019-11-19 16:24:25 UTC  
> Url: https://github.com/boostorg/mpi/issues/110#issuecomment-555587989  

Is there a way to cancel a blocking `MPI_Mprobe` call?

---

## Comment 3

> Username: aminiussi  
> Created at: 2019-11-19 16:42:10 UTC  
> Url: https://github.com/boostorg/mpi/issues/110#issuecomment-555596211  

I guess not, probably need to move to  MPI_IMprobe.  
  
But I need o read 3.8 more carefully (I hate that section...)

---

## Comment 4

> Username: sdebionne  
> Created at: 2019-11-20 07:50:24 UTC  
> Updated at: 2019-11-20 07:53:32 UTC  
> Url: https://github.com/boostorg/mpi/issues/110#issuecomment-555882165  

FYI, I asked the [MPI community on SO](https://stackoverflow.com/questions/58938736/is-there-a-way-to-cancel-a-blocking-mpi-probe-call).

---

## Comment 5

> Username: aminiussi  
> Created at: 2019-11-24 18:48:07 UTC  
> Url: https://github.com/boostorg/mpi/issues/110#issuecomment-557916369  

I'm going to check why it hang.  
  
I'd like to point out that none of Boost.MPI function is supposed to map the MPI standard regarding serialized communication as there is none in C MPI. We just try to comply to our documentation which is expected not to be surprising for an MPI user.   
  
Right now it's hanging on:  
```  
[alainm@castor test]$ mpirun -np 2 ../../../bin.v2/libs/mpi/test/cancel110-2.test/intel-linux/debug/threading-multi/visibility-hidden/cancel110-2  
Before MPI_Wait  
Before MPI_Cancel  
After MPI_Cancel  
 ....  
```  
  
I need to check if a recv with no matching send is defined.    
  
We probably need to change that probe for it's async version and check if they were introduced at the same time.  
  
Thank for you report and test case.

---

## Comment 6

> Username: aminiussi  
> Created at: 2019-11-24 18:52:20 UTC  
> Url: https://github.com/boostorg/mpi/issues/110#issuecomment-557916686  

There was a proposal in #72 to make the switch between the 2 implementation available at runtime, we still have that option if it comes down to that.

---

## Comment 7

> Username: sdebionne  
> Created at: 2019-11-27 10:46:57 UTC  
> Url: https://github.com/boostorg/mpi/issues/110#issuecomment-559034613  

For my use case, switching at compile time would be good enough. That means that using Probe should be driven not only by the MPI supported standard but also by a user defined macro `BOOST_MPI_USE_IMPROBE`.  
  
```c++  
// If BOOST_MPI_USE_IMPROBE was not user defined  
#if !defined(BOOST_MPI_USE_IMPROBE)  
#if BOOST_MPI_VERSION >= 3   
// MPI_Probe an friends should work  
#  if defined(I_MPI_NUMVERSION)  
// Excepted for some Intel versions.  
// Note that I_MPI_NUMVERSION is not always defined with Intel.  
#    if I_MPI_NUMVERSION > 20190004000  
#      define BOOST_MPI_HAVE_IMPROBE 1  
#    endif  
#  else  
#    define BOOST_MPI_HAVE_IMPROBE 1  
#  endif  
#endif  
#endif //!defined(BOOST_MPI_USE_IMPROBE)  
```  
  
and use `#if BOOST_MPI_USE_IMPROBE` instead of `#if defined(BOOST_MPI_USE_IMPROBE)` in the communicators.

---

## Comment 8

> Username: aminiussi  
> Created at: 2020-06-19 12:45:58 UTC  
> Url: https://github.com/boostorg/mpi/issues/110#issuecomment-646616186  

Would a   
``  
// Uncomment if you don't want to use MPI_Probe and friends`  
//#define BOOST_MPI_NO_IMPROBE   
```  
Do the trick ?

---

## Comment 9

> Username: aminiussi  
> Created at: 2020-06-19 13:35:52 UTC  
> Url: https://github.com/boostorg/mpi/issues/110#issuecomment-646639371  

So basically, what you want is a wait those behavior is:  
```  
 while (request.active()) request.test();  
```  
?

---

## Comment 10

> Username: aminiussi  
> Created at: 2020-06-19 14:13:37 UTC  
> Url: https://github.com/boostorg/mpi/issues/110#issuecomment-646659073  

So it should be possible to implement something sensible. The trick is to handle the status;
