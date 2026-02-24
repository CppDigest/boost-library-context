# #191 - Not working in Google Cloud Run (uses gVisor) [Closed]

> Username: jgsogo  
> Created at: 2021-05-29 13:36:56 UTC  
> Updated at: 2021-08-12 08:22:26 UTC  
> Closed at: 2021-08-12 08:22:25 UTC  
> Url: https://github.com/boostorg/filesystem/issues/191  

I've created a docker container (`ubuntu:focal`) with a C++ application that is using `boost::filesystem` (v1.76.0) to create some directories while processing data. It works if I run the container locally, but it fails when deployed to Cloud Run.  
  
A simple statement like  
```  
boost::filesystem::exists(boost::filesystem::current_path())  
```  
fails with _"Invalid argument '/current/path/here'"_. **It doesn't work in this C++ application, but from a Python app running equivalent statements, it does work.**  
  
Reading [the docs](https://cloud.google.com/run/docs/reference/container-contract#sandbox) I can see Cloud Run is using gVisor and not all the system calls are fully supported (link: https://gvisor.dev/docs/user_guide/compatibility/linux/amd64/), nevertheless I would expect simple calls to work: check if a directory exists, create a directory, remove,...   
  
---  
  
Tried with std::filesystem (C++17), it works.

---

## Comment 1

> Username: Lastique  
> Created at: 2021-05-29 14:02:11 UTC  
> Url: https://github.com/boostorg/filesystem/issues/191#issuecomment-850838593  

Sorry, there's no chance I'm going to be able to debug this. Please, investigate this further. In particular, reduce the test case to a single call and preferably down to a single system call. Patches are welcome, of course.

---

## Comment 2

> Username: Lastique  
> Created at: 2021-06-14 21:24:32 UTC  
> Url: https://github.com/boostorg/filesystem/issues/191#issuecomment-861006189  

Any updates on this? Does it still fail with the current develop?

---

## Comment 3

> Username: Lastique  
> Created at: 2021-08-12 08:22:25 UTC  
> Url: https://github.com/boostorg/filesystem/issues/191#issuecomment-897444255  

Since there's no reply and I'm not going to be able to debug this, I'm closing this ticket.
