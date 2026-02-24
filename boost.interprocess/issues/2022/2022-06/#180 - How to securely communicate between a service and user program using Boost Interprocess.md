# #180 - How to securely communicate between a service and user program using Boost Interprocess? [Closed]

> Username: AaronSomeone  
> Created at: 2022-06-27 20:08:03 UTC  
> Updated at: 2022-07-16 20:39:12 UTC  
> Closed at: 2022-07-16 20:39:12 UTC  
> Url: https://github.com/boostorg/interprocess/issues/180  

I have a service with LOCAL SYSTEM privileges, and I want it to be able to communicate with my client application, which is run under unprivileged users (I'm on Windows). I am trying to use boost::interprocess for this, and specifically I'm creating managed_shared_memory—using memory-mapped files rather than native shared memory. Right now I simply create a memory-mapped file with GENERIC_READ and GENERIC_WRITE permissions given to everyone, so that the client can communicate with the service. However, I'm wondering what the current security risks of that are, and how to make this secure?  
  
For one, someone could overwrite the data in the memory mapped file, interrupting communication between the client and the service. But I'm wondering if something more drastic is possible, like someone redirecting the service to other memory addresses to execute malicious code by editing the memory-mapped file. I don't store pointers within the memory-mapped file itself, I only use the boost::interprocess offset_ptr and plain data.  
  
But there's no way to set a password on the memory mapped file, right? And no way to only give access to one specific process? Access rights seem to be on a more generic basis.

---

## Comment 1

> Username: igaztanaga  
> Created at: 2022-07-16 20:39:12 UTC  
> Url: https://github.com/boostorg/interprocess/issues/180#issuecomment-1186286911  

Well, I think this is not an issue but a question... You can pass a "permissions" instance when creating a managed_shared_memory instance. This "permissions" instance can be constructed with a pointer to a SECURITY_ATTRIBUTES object in Windows. Those permissions will be applied in the created file. Boost.Interprocess offers the security level that the underlying OS can offer...
