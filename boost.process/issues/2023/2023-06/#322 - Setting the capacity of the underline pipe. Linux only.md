# #322 - Setting the capacity of the underline pipe. Linux only [Open]

> Username: SilverPlate3  
> Created at: 2023-06-28 20:07:21 UTC  
> Updated at: 2023-07-01 06:34:40 UTC  
> Url: https://github.com/boostorg/process/issues/322  

I work for a big security vendor, and recently we tried to shift from libc interface (fork, exec, waitpid...) to Boost::Process.  
However, we are missing some crucial API's related to the underline pipe.  
One of the functionality we need is: Setting the pipe capacity.   
If the pipe fills up, 2 things may happen (Depends if the pipe is nonblocking or blocking):  
1) **The producer will be blocked**. From [pipe man page](https://man7.org/linux/man-pages/man7/pipe.7.html): "If a process attempts to write to a full pipe, then blocks until sufficient data has been read from the pipe to allow the write to complete"  
2) **The new data will be thrown**. From [pipe man page](https://man7.org/linux/man-pages/man7/pipe.7.html): "If the pipe is full, then write(2) fails, with errno set to EAGAIN."  
  
Both cases cause us to loose live data from the producer.   
increasing the pipe capacity doesn't solve this issue 100%. But the possibility of this happening, diminishing by a lot.  
This is crucial for us.   
  
**Running the following code for 5 seconds:**  
```  
int main()  
{  
    bp::ipstream stdOut;  
    bp::child childProcess = bp::child("sysdig", bp::std_out > stdOut);  
    auto fdStdOut = stdOut.pipe().native_source();  
      
    while (true)  
    {  
        int bytesInPipe;  
        ioctl(fdStdOut, FIONREAD, &bytesInPipe); // Find number of available bytes  
        std::string output(bytesInPipe + 1, 0);  
        std::cout << "\nPipe size: " << fcntl(fdStdOut, F_GETPIPE_SZ) << std::endl;  
        std::cout << "Bytes available: " << bytesInPipe << std::endl;  
        std::cout << "Bytes read: " << read(fdStdOut, output.data(), bytesInPipe) << std::endl;  
        std::this_thread::sleep_for(std::chrono::milliseconds(50)); // Some work  
    }  
}  
```  
  
**Here is some of the output**:  
```  
Pipe size: 65536  
Bytes available: 17685  
Bytes read: 17685  
  
Pipe size: 65536  
Bytes available: 5577  
Bytes read: 5577  
  
Pipe size: 65536  
Bytes available: 65536  
Bytes read: 65536  
  
Pipe size: 65536  
Bytes available: 65421  
Bytes read: 65421  
  
Pipe size: 65536  
Bytes available: 4564  
Bytes read: 4564  
```  
The capacity is easily reached.   
  
  
After changing the pipe capacity to the value specified at: /proc/sys/fs/pipe-max-size, I was able to 20X the pipe capacity.   
**New code**  
```  
int main()  
{  
    bp::ipstream stdOut;  
    bp::child childProcess = bp::child("sysdig", bp::std_out > stdOut);  
    auto fdStdOut = stdOut.pipe().native_source();  
    std::cout << "new pipe capacity: "  << fcntl(fdStdOut, F_SETPIPE_SZ, 1048576) << std::endl; // Changing the capacity  
  
    while (true)  
    {  
        int bytesInPipe;  
        ioctl(fdStdOut, FIONREAD, &bytesInPipe); // Find number of available bytes  
        std::string output(bytesInPipe + 1, 0);  
        std::cout << "\nPipe size: " << fcntl(fdStdOut, F_GETPIPE_SZ) << std::endl;  
        std::cout << "Bytes available: " << bytesInPipe << std::endl;  
        std::cout << "Bytes read: " << read(fdStdOut, output.data(), bytesInPipe) << std::endl;  
        std::this_thread::sleep_for(std::chrono::milliseconds(50)); // Some work  
    }  
}  
```  
New output:  
```  
new pipe capacity: 1048576  
  
Pipe size: 1048576  
Bytes available: 259710  
Bytes read: 259710  
```  
  
  
I have created a Pull request that will add this functionality, without the the user accessing the file descriptor or calling fcntl()  
PR:  https://github.com/boostorg/process/pull/324

---

## Comment 1

> Username: SilverPlate3  
> Created at: 2023-06-28 20:15:15 UTC  
> Url: https://github.com/boostorg/process/issues/322#issuecomment-1612042281  

Please let me know how can I improve my PR, to better suite the code base.

---

## Comment 2

> Username: klemens-morgenstern  
> Created at: 2023-06-28 23:29:14 UTC  
> Url: https://github.com/boostorg/process/issues/322#issuecomment-1612236633  

I don't think this should be in the code base, as it's only a linux thing. On windows you have to set this on startup.  
  
Why can't you just call `fcntl(stdOut.pipe().native_source(), F_SETPIPE_SZ, capacity)` directly? That's the reason the `native_source` function exists.  
  
Did you look at the boost.process v2? It's still marked as experimental, but only because I didn't yet use it in a big project myself.   
It's using asio's pipes instead, so that might be preferrable if you want to go non-blocking.

---

## Comment 3

> Username: SilverPlate3  
> Created at: 2023-06-29 05:10:59 UTC  
> Updated at: 2023-06-29 05:27:46 UTC  
> Url: https://github.com/boostorg/process/issues/322#issuecomment-1612439942  

Hey.   
Thanks for the quick reply and the great work with Boost::process.  
  
1) Sorry but I didn't have chance to look at V2 yet.   
  
2) I definitely can just use `fcntl(stdOut.pipe().native_source(), F_SETPIPE_SZ, capacity)`  directly, but:  
 A. Its undocumented, and I don't want the other developers to start drilling through the objects.  
     It took me a lot of trial and error to finally get to that understanding.   
 B. In issue https://github.com/boostorg/process/issues/38 you have stated "Sink is the one written to, source is the one read   
     from", so I know which one to use. But if a user sees both native_source & native_sink its unclear to him which one to use   
     when.   
 C . Continuing on B.  `fcntl(stdOut.pipe().native_source(), F_SETPIPE_SZ, capacity)` isn't correct for stdin.    
   
 3) Indeed its only for linux that why in the PR I placed the functions and the tests in #if.  
     I would happily add it to docs if you want and specify its only for linux.   
       
       
I am really trying to make the library more of a plug and play. The docs don't cover the full capability of this amazing library, so users are lost.   
Making it even simpler for users, I offered in the PR to move the functions to bp::ipstream/bp::opstream  
I have some other PR's ready for other pipe related functionalities, but I wanted to see your reaction to this one first.

---

## Comment 4

> Username: liorzemah  
> Created at: 2023-06-29 10:33:28 UTC  
> Updated at: 2023-06-29 10:33:45 UTC  
> Url: https://github.com/boostorg/process/issues/322#issuecomment-1612828524  

It sounds like a good addition, I am also using boost::process and always need to set the pipe any time I create a new process, will be good to add it to the library.
