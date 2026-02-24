# #1445 - Retry close on EINTR is wrong on linux and some other posix platfoms [Closed]

> Username: maximilianriemensberger  
> Created at: 2019-02-09 19:15:31 UTC  
> Updated at: 2019-02-10 21:47:06 UTC  
> Closed at: 2019-02-10 21:47:06 UTC  
> Url: https://github.com/boostorg/beast/issues/1445  

close() is called in a loop on EINTR in file_posix even on linux, see https://github.com/boostorg/beast/blob/06efddd8b851610b5b3a5832ac87f1c52b838d9b/include/boost/beast/core/impl/file_posix.hpp  
  
```cpp  
int  
file_posix::  
native_close(native_handle_type& fd)  
{  
    if(fd != -1)  
    {  
        for(;;)  
        {  
            if(::close(fd) == 0)  
                break;  
            int const ev = errno;  
            if(errno != EINTR)  
                return ev;  
        }  
        fd = -1;  
    }  
    return 0;  
}  
```  
  
Now the linux manpage of close says:  
  
###   Dealing with error returns from close()  
  
       A  careful programmer will check the return value of close(), since it is quite possible  
       that errors on a previous write(2) operation are reported only on the final close() that  
       releases  the  open  file description.  Failing to check the return value when closing a  
       file may lead to silent loss of data.  This can especially be observed with NFS and with  
       disk quota.  
  
       Note,  however, that a failure return should be used only for diagnostic purposes (i.e.,  
       a warning to the application that there may still be I/O pending or there may have  been  
       failed  I/O)  or  remedial  purposes  (e.g.,  writing  the  file once more or creating a  
       backup).  
  
       Retrying the close() after a failure return is the wrong thing to  do,  since  this  may  
       cause a reused file descriptor from another thread to be closed.  This can occur because  
       the Linux kernel always releases the file descriptor early in the close operation, free‐  
       ing  it  for  reuse;  the  steps  that may return an error, such as flushing data to the  
       filesystem or device, occur only later in the close operation.  
  
       Many other implementations similarly always close the file  descriptor  (except  in  the  
       case  of  EBADF, meaning that the file descriptor was invalid) even if they subsequently  
       report an error on return from close().  POSIX.1 is currently silent on this point,  but  
       there are plans to mandate this behavior in the next major release of the standard  
  
       A  careful programmer who wants to know about I/O errors may precede close() with a call  
       to fsync(2).  
  
       The EINTR error is a somewhat special case.  Regarding  the  EINTR  error,  POSIX.1-2013  
       says:  
  
              If  close()  is  interrupted by a signal that is to be caught, it shall return -1  
              with errno set to EINTR and the state of fildes is unspecified.  
  
       This permits the behavior that occurs on Linux and many other implementations, where, as  
       with  other errors that may be reported by close(), the file descriptor is guaranteed to  
       be closed.  However, it  also  permits  another  possibility:  that  the  implementation  
       returns an EINTR error and keeps the file descriptor open.  (According to its documenta‐  
       tion, HP-UX's close() does this.)  The caller must then once more use close()  to  close  
       the  file descriptor, to avoid file descriptor leaks.  This divergence in implementation  
       behaviors provides a difficult hurdle for portable applications, since on many implemen‐  
       tations,  close()  must  not  be called again after an EINTR error, and on at least one,  
       close() must be called again.  There are plans to address this conundrum  for  the  next  
       major release of the POSIX.1 standard.  
  
Special attention should be payed to the last paragraph.  
  
Now the bad news is the implementation above is correct and incorrect at the same time for Linux and many others. For Linux which is probably the largest posix platform it is unfortunately incorrect. So I suggest at least for linux that close is not called in a loop even on EINTR.  
  
This is an extreme corner case that you probably won't ever hit in the lifetime of most applications, but still ...

---

## Comment 1

> Username: vinniefalco  
> Created at: 2019-02-09 19:35:31 UTC  
> Url: https://github.com/boostorg/beast/issues/1445#issuecomment-462072834  

Thanks for opening this issue! I see that there is a little mess around implementations with respect to POSIX close....
