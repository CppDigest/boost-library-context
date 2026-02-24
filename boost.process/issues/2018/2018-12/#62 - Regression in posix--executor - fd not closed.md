# #62 - Regression in posix::executor - fd not closed [Closed]

> Username: KiNgMaR  
> Created at: 2018-12-27 13:09:00 UTC  
> Updated at: 2022-11-11 17:51:10 UTC  
> Closed at: 2022-11-11 17:51:10 UTC  
> Url: https://github.com/boostorg/process/issues/62  

Hi,  
  
8d2bd8770784944547ed71697042daed46b5526b (i.e. upgrading from boost 1.68 to boost 1.69) has introduced a regression where an `fd` for one of the child process pipes is not closed when there's nothing to read from it.  
  
This leads to fd exhaustion after a while, e.g. with the following piece of code:  
  
```  
    bp::ipstream stdout;  
    bp::child diffProcess(bp::search_path("diff"),  
        "--unified=1",  
        static_cast<std::string>(tmpFile1),  
        static_cast<std::string>(tmpFile2),  
        bp::std_out > stdout);  
  
    diffProcess.wait();  
```  
  
This patch is a workaround for the single case that we ran into, but there are probably more cases where the fd is not closed either. Please check :)  
  
```  
diff --git a/include/boost/process/detail/posix/executor.hpp b/include/boost/process/detail/posix/executor.hpp  
index 1390a58..8b86ed1 100644  
--- a/include/boost/process/detail/posix/executor.hpp  
+++ b/include/boost/process/detail/posix/executor.hpp  
@@ -282,7 +282,10 @@ class executor  
                 set_error(std::error_code(err, std::system_category()), "Error read pipe");  
         }  
         if (count == 0)  
+        {  
+            ::close(source);  
             return  ;  
+        }  
  
         std::error_code ec(data[0], std::system_category());  
         std::string msg(data[1], ' ');  
```  
  
Thank You & BR,  
Ingmar

---

## Comment 1

> Username: reddwarf69  
> Created at: 2019-01-11 19:57:45 UTC  
> Url: https://github.com/boostorg/process/issues/62#issuecomment-453638416  

I have not looked at the patch. But I can confirm I have found a file descriptor leaking issue using 1.69 and this patch fixes it.

---

## Comment 2

> Username: pfaffe  
> Created at: 2019-01-28 12:38:08 UTC  
> Url: https://github.com/boostorg/process/issues/62#issuecomment-458115779  

+1  
Fixes master as well!

---

## Comment 3

> Username: SimonEbner  
> Created at: 2019-03-22 14:20:42 UTC  
> Url: https://github.com/boostorg/process/issues/62#issuecomment-475639119  

I experience the same bug and it looks like it's still not patched in boost `1.70.beta1` :cry: Does anybody know if this will be addressed in time for 1.70 ?

---

## Comment 4

> Username: klemens-morgenstern  
> Created at: 2019-04-10 04:05:39 UTC  
> Url: https://github.com/boostorg/process/issues/62#issuecomment-481524959  

It is solved in my current develop branch, I sadly did not make the 1.70 release. It can be fixed by inserting `::close(p[0])` after `_read_error(p[0]);` there: https://github.com/klemens-morgenstern/boost-process/blob/master/include/boost/process/detail/posix/executor.hpp#L432
