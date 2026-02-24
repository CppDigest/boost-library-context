# #2859 - The beast Server Exits frequently [Closed]

> Username: vtharmalingam  
> Created at: 2024-04-25 11:32:30 UTC  
> Updated at: 2024-07-24 06:18:26 UTC  
> Closed at: 2024-07-24 06:18:26 UTC  
> Url: https://github.com/boostorg/beast/issues/2859  

1.84  
  
The application I developed is frequently exiting, causing me to lose "sessions" stored in "shared_state." These sessions are crucial for the app, and such exits pose a significant problem of user's discontinuity. Although I enabled boost::stacktrace, these exits do not qualify as crashes, so it doesn't provide much help. It seems to me (with my limited knowledge) that it's a session issue between the client and server, and a typical closure of a session could potentially bring down the entire app. Perhaps, the io_context exits due to completed work.  
  
### Crash Scenario 1 and the Console message  
```  
write: The I/O operation has been aborted because of either a thread exit or an application request  
  
```  
  
### Crash Scenario 2 and the Console message  
```  
read: An existing connection was forcibly closed by the remote host  
[on_write]: exception caught: bad_weak_ptr  
read: An existing connection was forcibly closed by the remote host  
  
```  
  
I read this [stackoverflow](https://stackoverflow.com/questions/76929848/make-parallel-group-the-i-o-operation-has-been-aborted-because-of-either-a-thre) thread and it says "cancellation of the read invalidates future writes on the websocket.".   
  
Given my environment being Windows, maybe leveraging Structured Exception Handling (SEH)  __try and __Error could be of any help at all, nor sure of what is the right way to leverage them.  
  
Please provide guidance to ensure stability and robustness.  
  
Thanks, in advance!  
  
Regards,  
Tharma

---

## Comment 1

> Username: ashtum  
> Created at: 2024-04-25 11:52:43 UTC  
> Url: https://github.com/boostorg/beast/issues/2859#issuecomment-2077003005  

```BASH  
write: The I/O operation has been aborted because of either a thread exit or an application request  
```  
This error suggests you might have a lifetime issue in your application, where an I/O object (such as a socket or resolver) has been destroyed while there was an outstanding async operation.  
  
```BASH  
read: An existing connection was forcibly closed by the remote host  
[on_write]: exception caught: bad_weak_ptr  
read: An existing connection was forcibly closed by the remote host  
```  
Same is the case here, it seems you tried to access a weak_ptr unconditionally while the actual object has been destroyed, my guess is you are using weak_ptr where a shared_ptr is appropriate or having a flawed object hierarchy.  
  
It's difficult to pinpoint the issue without additional details about your code.

---

## Comment 2

> Username: vtharmalingam  
> Created at: 2024-04-25 16:22:26 UTC  
> Url: https://github.com/boostorg/beast/issues/2859#issuecomment-2077684975  

Thanks, @ashtum. Got it. I'll work on streamlining the code flow and share it over. It might take a little while though.  
  
Additionally, I've noticed occasionally encountering the below type of error that leads to an exit.   
Does that ring any bells? I suspect it's distinct from the errors we discussed earlier. Any insights on that?  
  
```  
read: The network connection was aborted by the local system  
```  
  
Thanks,  
Tharma

---

## Comment 3

> Username: ashtum  
> Created at: 2024-04-25 17:07:56 UTC  
> Url: https://github.com/boostorg/beast/issues/2859#issuecomment-2077765939  

> ```  
> read: The network connection was aborted by the local system  
> ```  
  
Your server should be prepared for this kind of error (client connections might fail for all sorts of reasons). The server should close the socket and release all the resources associated with it. It seems you have a flaw in your code in that regard (either accessing a destroyed object or not checking the returned error code).

---

## Comment 4

> Username: ashtum  
> Created at: 2024-07-04 16:31:15 UTC  
> Url: https://github.com/boostorg/beast/issues/2859#issuecomment-2209322619  

@vtharmalingam, is this issue still open or has the problem been resolved?
