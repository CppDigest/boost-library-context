# #239 - Problem using interprocess_condition [Closed]

> Username: decryp7  
> Created at: 2024-11-08 08:00:42 UTC  
> Updated at: 2026-01-07 09:51:13 UTC  
> Closed at: 2026-01-07 09:51:12 UTC  
> Url: https://github.com/boostorg/interprocess/issues/239  

Hi, I am checking the feasibility of implementing a simple IPC using boost::interprocess.  
  
I have a problem with the interprocess_condition.  
  
Brief explanation of my prototype,  
1. I have 2 processes, client and server process.  
2. Client create a request with interprocess_condition and share it with the server process using shared memory.  
3. Server process the request and use interprocess_condition to signal the client.  
4. Client process the reply and delete the interprocess_condition.  
  
I observed that creating interprocess_condition on shared memory will create handle \BaseNamedObjects\boost.ipcXXXXXXXXXXXXXXX on windows.  
  
Expected behavior: handle is deleted on the server process.  
Current observation: handle is not deleted on the server process even though I have deleted the interprocess_condition on the client.  
  
Please refer to the attached video and source code.  
https://github.com/decryp7/BISharedMemoryIPCTest  
https://github.com/user-attachments/assets/c3c94307-49cb-49ba-bfe8-7aa404ae6470

---

## Comment 1

> Username: igaztanaga  
> Created at: 2026-01-07 09:51:13 UTC  
> Url: https://github.com/boostorg/interprocess/issues/239#issuecomment-3718082890  

Sorry, BOOST_INTERPROCESS_FORCE_NATIVE_EMULATION is a undocumented feature, but the behaviour is expected, as the client can't automatically close the handle of another process (or many processes).
