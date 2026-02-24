# #2735 - How to read `client hello` request in tls handshake [Closed]

> Username: MortezaBashsiz  
> Created at: 2023-09-06 08:10:13 UTC  
> Updated at: 2023-11-15 11:51:46 UTC  
> Closed at: 2023-11-15 11:51:46 UTC  
> Url: https://github.com/boostorg/beast/issues/2735  

This is a question that I tried to find the answer, but I didn't get it and I think I missed something between  
I am trying to simulate tls handshake when a client wants to connect to a https website over proxy  
You can see the history here #2732  
Now I came with a question  
  
How can I read `client hello` request while tls handshake ?  
  
The scenario is like this  
1. Sample code `main.cpp` which listens to port 8080 and reads the socket. It will write on socket `HTTP/1.1 200 Connection established\r\n` for any request  
2. Client set https_proxy to the port which is listening by `main.cpp`  
3. Client curl some https website  
4. Client sends `CONNECT` to `main.cpp`  
5. `main.cpp` prints the request and I see the connect request (**soooo goood**)  
6. `main.cpp` returns `HTTP/1.1 200 Connection established\r\n`  
7. Client understood that the session is established   
8. Client sends `client hello` to `main.cpp`  
9. `main.cpp` should print what it received over socket at this step i supposed to print the `client hello` (**BUT DOES NOT**)  
  
My problem is at step 9  
I want to see and check the `client hello` but I don't know how it could be possible to do it

---

## Comment 1

> Username: klemens-morgenstern  
> Created at: 2023-09-06 10:22:24 UTC  
> Url: https://github.com/boostorg/beast/issues/2735#issuecomment-1708072303  

I don't follow - why send data during a TLS handshake and how is that connected to `CONNECT`?

---

## Comment 2

> Username: MortezaBashsiz  
> Created at: 2023-09-06 10:26:51 UTC  
> Updated at: 2023-09-06 10:27:35 UTC  
> Url: https://github.com/boostorg/beast/issues/2735#issuecomment-1708078122  

I don't want to send data during TLS handshake  
I am trying to do it manually and simulate it like https_proxy  
  
To answer this question how they are related to `CONNECT`  
  
In https_proxy when the client wants to use https it tries to send at first the `CONNECT` request to the proxy server and then when client received `HTTP/1.1 200 Connection established` it knows that the connection is established, and then it will start to do the TLS handshake with `client hello`  
![image](https://github.com/boostorg/beast/assets/19620677/43137b21-331a-4c75-ae05-0b904c5f5242)

---

## Comment 3

> Username: klemens-morgenstern  
> Created at: 2023-09-07 07:48:16 UTC  
> Url: https://github.com/boostorg/beast/issues/2735#issuecomment-1709645697  

How does main receive the https. request? Where does the https_proxy request go?

---

## Comment 4

> Username: MortezaBashsiz  
> Created at: 2023-09-07 09:27:30 UTC  
> Url: https://github.com/boostorg/beast/issues/2735#issuecomment-1709806789  

What I want to do is like following picture I already wrote the agent and server and it works with http requests but for https I have problem  
  
1. Client sets https_proxy on the browser side to a program called agent  
2. Agent gets the original request from client  
3. Agents encrypt the request and put it as body and post it to server  
4. Server receives the post from agent and decrypt the body to make the original request  
5. Server sends the original request to origin server and gets the response  
6. Server encrypts the response as body and send it as response for post request which received from agent  
7. Agent decrypt the body and find the original response and send it as response to client  
8. And client continues and this process repeats  
  
My code on http requests works fine in Agent and Server side so if your destination is an http url then it is OK but for https requests since it needs tls handshake I need to get the `client hello` request from client on agent and send it to server  
But I can't  
  
![image](https://github.com/boostorg/beast/assets/19620677/be624124-e0fd-404e-a33d-069099c0bd83)

---

## Comment 5

> Username: klemens-morgenstern  
> Created at: 2023-09-07 09:37:07 UTC  
> Url: https://github.com/boostorg/beast/issues/2735#issuecomment-1709827584  

@ashtum Do you follow this?

---

## Comment 6

> Username: MortezaBashsiz  
> Created at: 2023-11-15 11:51:46 UTC  
> Url: https://github.com/boostorg/beast/issues/2735#issuecomment-1812395706  

At the end  
I parsed the client hello request on agent manually  
Read buffer as hex and parse it byte by byte and detect if it is client hello request or not  
You can see the full project [HERE](https://github.com/MortezaBashsiz/nipovpn)   
  
And the file to parse TLS requests [HERE](https://github.com/MortezaBashsiz/nipovpn/blob/main/agent/tls.cpp)
