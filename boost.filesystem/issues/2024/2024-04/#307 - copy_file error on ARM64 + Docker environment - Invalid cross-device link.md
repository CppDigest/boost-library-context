# #307 - copy_file error on ARM64 + Docker environment - Invalid cross-device link [Closed]

> Username: bigF93  
> Created at: 2024-04-10 08:58:02 UTC  
> Updated at: 2024-04-13 21:31:39 UTC  
> Closed at: 2024-04-13 21:31:39 UTC  
> Url: https://github.com/boostorg/filesystem/issues/307  

Hi,  
I'd like to report an issue that I am encountering with boost::filesystem::copy_file.  
The problem is apparent only in ARM architectures (on amd64 things work fine), and only when calling copy_file in a docker container, if the source directory is mounted (with type = bind ), and if the target directory belongs to the container (like /tmp ).  
  
boost version =  1.74  
docker version = 26.0.0  
OS base image in docker = ubuntu22.04 / aarch64 GNU/Linux  
  
  
steps to reproduce:  
1) start some ARM64 system  
2) prepare a container containing boost_filesystem  
3) docker run -it --mount type=bind,source=/path/containing/some/file/to/copy,target=/builder/ <some_container_id> /bin/bash  
4) try to run boost::filesystem::copy_file("/builder/some/file/to/copy/file.bin", "/tmp/file.bin")  
  
you should get the error:  
terminate called after throwing an instance of 'boost::filesystem::filesystem_error'  
  what():  boost::filesystem::copy_file: Invalid cross-device link ...  
  
code to reproduce:  
  
#include <boost/filesystem.hpp>  
int main()  
{  
    boost::filesystem::copy_file( boost::filesystem::path( "/builder/path/to/test.bin" ), boost::filesystem::path( "/tmp/test.bin") );  
    return 0;  
}

---

## Comment 1

> Username: Lastique  
> Created at: 2024-04-10 09:05:42 UTC  
> Url: https://github.com/boostorg/filesystem/issues/307#issuecomment-2046968030  

* What versions of Boost and host Linux kernel are you using?  
* Does it reproduce with the current Boost.Filesystem develop?  
* Do you build Boost.Filesystem in the container? If yes, do the Linux headers match the host kernel version? If no, do you build on a system equivalent to the host?

---

## Comment 2

> Username: Lastique  
> Created at: 2024-04-13 21:31:39 UTC  
> Url: https://github.com/boostorg/filesystem/issues/307#issuecomment-2053766323  

This does not reproduce for me with the current develop (equivalent to the about to be released 1.85.0) on Debian 12 aarch64 host and ubuntu:22.04 container. I'm assuming this was fixed at some point.
