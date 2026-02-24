# #210 - boost::filesystem::create_directory: File exists [Closed]

> Username: computervisionlearner  
> Created at: 2021-10-18 13:14:41 UTC  
> Updated at: 2021-10-18 14:30:37 UTC  
> Closed at: 2021-10-18 14:30:37 UTC  
> Url: https://github.com/boostorg/filesystem/issues/210  

Hi, all:  
    I am using boost 1.75 version on centos system。In the docker image, I used the sample code located at libs/log/example/rotating_file/main.cpp and it reports the following error: FAILURE: boost::filesystem::create_directory: File exists: "/opt/boost/demo".  However, I can execute the program correctly on the physical machine。My question is exactly the same as this statement (https://stackoverflow.com/questions/67256984/boostfilesystemexists-throws-operation-not-permitted-exception-on-centos-8-d), so I think this error is not a special case for me. Would you mind tell my how to solve this problem? Looking forward to your reply, Thank you ~~

---

## Comment 1

> Username: Lastique  
> Created at: 2021-10-18 14:30:37 UTC  
> Url: https://github.com/boostorg/filesystem/issues/210#issuecomment-945837394  

Looks like a duplicate of https://github.com/boostorg/filesystem/issues/182 and https://github.com/boostorg/filesystem/issues/173. I don't see a connection to the SO question - there, the issue is with permissions, which is different.  
  
Try updating to the latest Boost release or configure the Docker so that it allows the syscalls supported by the host kernel.
