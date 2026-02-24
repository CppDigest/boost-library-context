# #143 - Double close possible in file_descriptor_impl::close_impl() [Open]

> Username: jbatmac  
> Created at: 2022-02-10 20:51:57 UTC  
> Updated at: 2022-07-02 00:47:48 UTC  
> Url: https://github.com/boostorg/iostreams/issues/143  

The current implementation of file_descriptor_impl::close_impl() will cause a double close on a file descriptor if you explicitly call close() and it results in an error (e.g. BOOST_IOSTREAMS_FD_CLOSE() returns a failure due to EIO or ENOSPC).  In this case, the first call to close_impl() (from file_descriptor_impl::close()) will call the operating system's close() call on handle_ then throw an exception before it clears handle_ (by setting it to invalid_handle()).  When the destructor is called, handle_ is still set to the old file handle, so the destructor will call the operating system's close() again.  In the mean time, another thread may have opened that same file / socket handle.  If you're lucky, that other thread will get some kind of IO error.  If you're not lucky (as was my case), yet another thread then opens the same file handle (because it was closed again), at which point you have really weird file corruption.  
  
Proposed patch:  
diff --git a/src/file_descriptor.cpp b/src/file_descriptor.cpp  
index 288e2c6..77dd65d 100644  
```  
--- a/src/file_descriptor.cpp  
+++ b/src/file_descriptor.cpp  
@@ -266,8 +266,11 @@ void file_descriptor_impl::close_impl(bool close_flag, bool throw_) {  
                 #else  
                     BOOST_IOSTREAMS_FD_CLOSE(handle_) != -1;  
                 #endif  
-            if (!success && throw_)  
+            if (!success && throw_) {  
+                handle_ = invalid_handle();  
+                flags_ = 0;  
                 throw_system_failure("failed closing file");  
+               }  
         }  
         handle_ = invalid_handle();  
         flags_ = 0;  
```  
  
I'm not sure about flags_, but handle should definitely be set to invalid_handle() before throwing the exception.

---

## Comment 1

> Username: mclow  
> Created at: 2022-06-29 04:28:55 UTC  
> Url: https://github.com/boostorg/iostreams/issues/143#issuecomment-1169521121  

I'm happy with this proposed change, but do you have any idea how to test it?

---

## Comment 2

> Username: mclow  
> Created at: 2022-06-29 04:33:49 UTC  
> Url: https://github.com/boostorg/iostreams/issues/143#issuecomment-1169523357  

Although I might write it as (uncompiled code):  
```  
void file_descriptor_impl::close_impl(bool close_flag, bool throw_) {  
    if (handle_ != invalid_handle()) {  
    	file_handle h = handle_;  
        handle_ = invalid_handle();  
        flags_ = 0;  
  
        if (close_flag) {  
            bool success =   
                #ifdef BOOST_IOSTREAMS_WINDOWS  
                    ::CloseHandle(h) == 1;  
                #else  
                    BOOST_IOSTREAMS_FD_CLOSE(h) != -1;  
                #endif  
            if (!success && throw_)  
                throw_system_failure("failed closing file");  
        }  
    }  
}  
```  
  
to avoid the duplicate setting of `handle_` and `flags_`

---

## Comment 3

> Username: jbatmac  
> Created at: 2022-06-29 19:07:43 UTC  
> Url: https://github.com/boostorg/iostreams/issues/143#issuecomment-1170384362  

I was hoping to repro with a unix socket (closing the listening side of the socket while the client was still sending, then do the close on the client side), but I couldn't get close() to return an error in that case (although I had to catch SIGPIPE for that to work).  Without a tightly controlled remote or fake filesystem, I'm not sure you can get a reliable EIO from close, and EINTR is probably very hard to get in a reliable fashion.  That leaves EBADF.  
  
My best guess on a reasonable test would be to create a file_descriptor (open any random file) then use the handle() method to manually ::close() the file (e.g. the unix close, not the file_descriptor's close method).  That should return 0.  Next, call ::close() on the file descriptor (this will return EBADF as expected, as long as this is test is single threaded and another thread hasn't picked up this file descriptor).  Expect an exception here.  Next, call the close() method again.  Without the patch (your version or mine), expect yet another exception.  With the patch, there will be no exception (which means that the destructor will also work, since it's just calling close_impl() again, this time with throw_ false, so there's no easy way to detect it).  
  
So something like this:  
auto fd = file_descriptor("/tmp/test");  
close(fd.handle());  // #include <unistd.h> needed here  
try {  
  fd.close();  
  cerr << "Unexpected success on second close" << end;  
} catch (...) (  
  // We expect this to fail since we manually closed earlier without the fd instance knowing it.  
  // Still really bad to do in real code, but it gives us the error code from close() that we want.  
}  
  
try {  
  fd.close();  
} catch (...) (  
  cerr << "Unexpected failure on third close" << end;  
}  
  
  
I haven't tried compiling it, but hopefully something like that should show whether or not the patch works.

---

## Comment 4

> Username: mclow  
> Created at: 2022-07-02 00:47:47 UTC  
> Url: https://github.com/boostorg/iostreams/issues/143#issuecomment-1172800615  

Committed 7f49cec to fix this problem
