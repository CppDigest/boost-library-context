# #469 - Handshake hardening [Closed]

> Username: anarthal  
> Created at: 2025-04-23 17:03:54 UTC  
> Updated at: 2025-05-07 11:11:53 UTC  
> Closed at: 2025-05-07 11:11:53 UTC  
> Url: https://github.com/boostorg/mysql/issues/469  

Handshake is a state-machine-like algorithm, where the server can send one of many packet types, and we react accordingly. The current algorithm works but allows state transitions which aren't supposed to happen in the protocol (e.g. if the server sends two auth switch requests, we don't fail). Refactor the algorithm to perform stronger validation.  
  
This task should also provide a more modular implementation to handshake, where plugin logic is separated from the main algorithm, so that #313 can be implemented.
