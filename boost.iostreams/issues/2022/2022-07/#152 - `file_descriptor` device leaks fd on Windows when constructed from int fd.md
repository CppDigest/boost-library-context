# #152 - `file_descriptor` device leaks fd on Windows when constructed from int fd [Open]

> Username: julianbrost  
> Created at: 2022-07-27 14:21:45 UTC  
> Updated at: 2022-07-27 14:21:45 UTC  
> Url: https://github.com/boostorg/iostreams/issues/152  

`boost::iostreams::file_descriptor` has the following two constructors on Windows (among others):  
  
```cpp  
file_descriptor( int fd, file_descriptor_flags );  
file_descriptor( HANDLE hFile, file_descriptor_flags );  
```  
  
When using the first one in combination with `close_handle`, the file descriptor is leaked. Take the following code:  
  
```cpp  
#ifdef _WIN32  
#include <io.h>  
#endif  
#include <fcntl.h>  
#include <sys/stat.h>  
#include <iostream>  
#include <boost/iostreams/device/file_descriptor.hpp>  
  
int main() {  
	std::cout << "open + stream:" << std::endl;  
	for (int i = 0; i < 3; i++) {  
		int fd = open("tmp", O_CREAT|O_RDWR, S_IREAD|S_IWRITE);  
		std::cout << "open(): " << fd << std::endl;  
		boost::iostreams::file_descriptor iostreamsFd(fd, boost::iostreams::close_handle);  
	}  
  
	std::cout << "open + close:" << std::endl;  
	for (int i = 0; i < 3; i++) {  
		int fd = open("tmp", O_CREAT|O_RDWR, S_IREAD|S_IWRITE);  
		std::cout << "open(): " << fd << std::endl;  
		close(fd);  
	}  
}  
```  
  
It repeatedly uses `open` to create `int` file descriptors, and then constructs `boost::iostreams::file_descriptor` with `boost::iostreams::close_handle` from it, which then go out of scope immediately, which should close the fd. For comparison, a second loop just calls `close` to verify that fd numbers are actually reused immediately.  
  
Running this on Windows (Windows 10, MSVC 19.29.30146.0, Boost 1.79.0) gives the following output, i.e. it leaks the fds from the first loop but not from the second:  
```  
open + stream:  
open(): 3  
open(): 4  
open(): 5  
open + close:  
open(): 6  
open(): 6  
open(): 6  
```  
  
In comparison, running the same code on Linux (Arch Linux, g++ 12.1.0, Boost 1.79.0), the output looks like this, no fds are leaked:  
```  
open + stream:  
open(): 3  
open(): 3  
open(): 3  
open + close:  
open(): 3  
open(): 3  
open(): 3  
```  
  
It looks like `_get_osfhandle` is used to obtain a `HANDLE` from the `int` fd:  
https://github.com/boostorg/iostreams/blob/800323298658c876976d360a2a96d47c0f5f8c81/src/file_descriptor.cpp#L111-L112  
  
Which is then later used with `CloseHandle`:  
https://github.com/boostorg/iostreams/blob/800323298658c876976d360a2a96d47c0f5f8c81/src/file_descriptor.cpp#L266  
  
According to the [documentation for `_get_osfhandle`](https://docs.microsoft.com/en-us/cpp/c-runtime-library/reference/get-osfhandle?view=msvc-170), this is something you explicitly shouldn't do:  
  
> To close a file whose operating system (OS) file handle is obtained by _get_osfhandle, call [_close](https://docs.microsoft.com/en-us/cpp/c-runtime-library/reference/close?view=msvc-170) on the file descriptor fd. Never call CloseHandle on the return value of this function. The underlying OS file handle is owned by the fd file descriptor, and is closed when [_close](https://docs.microsoft.com/en-us/cpp/c-runtime-library/reference/close?view=msvc-170) is called on fd.  
  
This probably explains the leak: due to not using `close`/`_close`, the `int` fd is kept open (but now refers to an invalid handle).
