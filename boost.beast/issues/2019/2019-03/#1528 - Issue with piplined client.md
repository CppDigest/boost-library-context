# #1528 - Issue with piplined client. [Closed]

> Username: ra1u  
> Created at: 2019-03-19 19:52:44 UTC  
> Updated at: 2019-03-19 21:40:15 UTC  
> Closed at: 2019-03-19 21:40:15 UTC  
> Url: https://github.com/boostorg/beast/issues/1528  

server: (echo back received body on PUT) seems to work fine  
https://gist.github.com/ra1u/ea36d9c1e51fc7538974fe8a29ed991f  
  
client: has issue   
https://gist.github.com/ra1u/1e3cf085f680c0901bb92668254e996b  
  
Client creates body with every time decreasing  integer _counter__ and sends that on server. When callback in client  _on_write_ is called, new message is sent.   
  
Sending and receiving of messages from client is not synchronized, but performed over same thread and connection. It can happen that multiple request are sent or received in same tcp packet.  
  
Wireshark shows that all messages are sent from client. All messages are responded   from server. Not all responses are parsed and displayed on client.  
  
for example  
  
    ./client 100     
    100  
    98  
    99  
    97  
    96  
    95  
    94  
    93  
    91  
    92  
    90  
    88  
    89  
    87  
    read: bad version  
    read: bad version  
    read: bad version  
    read: bad version  
    ......   
  
Expected output would be print of received responses that were sent as numbers from 100 to 0, but some numbers are missing.  
  
Edit:  
Using boost 1.69

---

## Comment 1

> Username: vinniefalco  
> Created at: 2019-03-19 20:26:35 UTC  
> Url: https://github.com/boostorg/beast/issues/1528#issuecomment-474568234  

Your problem is here:  
https://gist.github.com/ra1u/1e3cf085f680c0901bb92668254e996b#file-client-cpp-L112  
  
You are causing two `async_read` to be outstanding at the same time. You must wait for the first read to finish before starting the second.

---

## Comment 2

> Username: ra1u  
> Created at: 2019-03-19 21:40:15 UTC  
> Url: https://github.com/boostorg/beast/issues/1528#issuecomment-474595363  

Thank You for your response. Much appreciated.
