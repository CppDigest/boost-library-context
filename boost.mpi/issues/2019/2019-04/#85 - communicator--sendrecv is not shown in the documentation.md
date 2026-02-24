# #85 - communicator::sendrecv is not shown in the documentation [Closed]

> Username: bellaz89  
> Created at: 2019-04-03 08:08:40 UTC  
> Updated at: 2019-10-22 15:07:36 UTC  
> Closed at: 2019-10-22 15:07:35 UTC  
> Url: https://github.com/boostorg/mpi/issues/85  

Even if an implementation of sendrecv is present in boost::mpi::communicator  
  
https://github.com/boostorg/mpi/blob/develop/include/boost/mpi/communicator.hpp#L511-L541  
https://github.com/boostorg/mpi/blob/develop/include/boost/mpi/communicator.hpp#L1532-L1539  
  
The support in the documentation says 'unsupported'  
  
https://github.com/boostorg/mpi/blob/develop/doc/c_mapping.qbk#L86-L89  
  
Is this intended or not?

---

## Comment 1

> Username: aminiussi  
> Created at: 2019-04-03 08:15:20 UTC  
> Url: https://github.com/boostorg/mpi/issues/85#issuecomment-479387038  

Thanks for reporting that.   
  
The "unsupported" is now a mistake. There is a test for that one.   
  
I'll fix it.   
  
Cheers   
  
  
From: "bellaz89" <notifications@github.com>   
To: "boostorg/mpi" <mpi@noreply.github.com>   
Cc: "Subscribed" <subscribed@noreply.github.com>   
Sent: Mercredi 3 Avril 2019 10:08:40   
Subject: [boostorg/mpi] communicator::sendrecv is not shown in the documentation (#85)   
  
  
  
Even if an implementation of sendrecv is present in boost::mpi::communicator   
  
[ https://github.com/boostorg/mpi/blob/develop/include/boost/mpi/communicator.hpp#L511-L541 | https://github.com/boostorg/mpi/blob/develop/include/boost/mpi/communicator.hpp#L511-L541 ]   
[ https://github.com/boostorg/mpi/blob/develop/include/boost/mpi/communicator.hpp#L1532-L1539 | https://github.com/boostorg/mpi/blob/develop/include/boost/mpi/communicator.hpp#L1532-L1539 ]   
  
The support in the documentation says 'unsupported'   
  
[ https://github.com/boostorg/mpi/blob/develop/doc/c_mapping.qbk#L86-L89 | https://github.com/boostorg/mpi/blob/develop/doc/c_mapping.qbk#L86-L89 ]   
  
Is this intended or not?   
  
—   
You are receiving this because you are subscribed to this thread.   
Reply to this email directly, [ https://github.com/boostorg/mpi/issues/85 | view it on GitHub ] , or [ https://github.com/notifications/unsubscribe-auth/AAhsjBbrXSkEqlLwsKrDV0dWqLr8HNNvks5vdGGIgaJpZM4cZ3oO | mute the thread ] .

---

## Comment 2

> Username: aminiussi  
> Created at: 2019-10-22 15:07:35 UTC  
> Url: https://github.com/boostorg/mpi/issues/85#issuecomment-545008682  

Done
