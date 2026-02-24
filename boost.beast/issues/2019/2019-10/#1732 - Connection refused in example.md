# #1732 - Connection refused in example [Closed]

> Username: rakhecha42  
> Created at: 2019-10-16 05:28:19 UTC  
> Updated at: 2019-10-16 07:15:55 UTC  
> Closed at: 2019-10-16 07:15:54 UTC  
> Url: https://github.com/boostorg/beast/issues/1732  

Hello. I'm using version 1.68. I tried to run the example HTTP_SERVER_SYNC.CPP using   
`g++ -std=c++11 beast.cpp -o beast -lpthread -lboost_system`  
  
to start the server, i used  
`./beast 0.0.0.0 8080 .`  
  
Now, doing `curl -v GET localhost/8080/ax.txt` shows the following:  
  
`* Rebuilt URL to: GET/  
* Could not resolve host: GET  
* Closing connection 0  
curl: (6) Could not resolve host: GET  
*   Trying 127.0.0.1...  
* TCP_NODELAY set  
* connect to 127.0.0.1 port 80 failed: Connection refused  
* Failed to connect to localhost port 80: Connection refused  
* Closing connection 1  
curl: (7) Failed to connect to localhost port 80: Connection refused`  
  
Also, opening 0.0.0.0:8080 in the browser shows "The resource '/' was not found."

---

## Comment 1

> Username: rakhecha42  
> Created at: 2019-10-16 07:04:20 UTC  
> Url: https://github.com/boostorg/beast/issues/1732#issuecomment-542554633  

Also, when I'm trying to start the server with the file in the command line, as in:  
`./beast 0.0.0.0 8080 ax.txt `   
I'm getting a server error saying:  
An error occurred: 'Not a directory'

---

## Comment 2

> Username: rakhecha42  
> Created at: 2019-10-16 07:11:01 UTC  
> Url: https://github.com/boostorg/beast/issues/1732#issuecomment-542556960  

Okay my curl command was wrong. Apologies. But the problems in showing in the browser still persist.

---

## Comment 3

> Username: rakhecha42  
> Created at: 2019-10-16 07:15:54 UTC  
> Url: https://github.com/boostorg/beast/issues/1732#issuecomment-542558675  

Nevermind it was my fault. I wasn't accesing the file. Sorry for the noise.
