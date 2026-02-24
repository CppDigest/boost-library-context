# #2833 - Beast Server Unable to Serve JavaScript-Enabled Web Pages/Apps [Closed]

> Username: vtharmalingam  
> Created at: 2024-03-04 12:52:38 UTC  
> Updated at: 2024-03-05 08:32:57 UTC  
> Closed at: 2024-03-05 08:32:57 UTC  
> Url: https://github.com/boostorg/beast/issues/2833  

### Version of Beast  
1.84  
  
### Steps necessary to reproduce the problem  
[Advanced, flex (plain + SSL](https://www.boost.org/doc/libs/master/libs/beast/example/http/server/fast/http_server_fast.cpp)  
  
I was able to run the server. It serves a simple HTML that i placed under _doc_root.  
But when I put a JS app, this is not serving the page.   
  
Here is what I did:  
  
1. Pulled this source : [React Sample App](https://github.com/andrewagain/calculator)  
2. Did `npm install` and `npm build`. That created build files inside `build` dir  
3. Dropped that inside _doc_root  
  
I just get to see this when I opened the browser:  
  
![image](https://github.com/boostorg/beast/assets/7923720/4809deb4-ab3b-4e57-936b-67ba480bf7fe)  
  
Please provide guidance on extending the Beast server to support a JavaScript-rich application (like that one above). I aim to integrate my backend (Beast) with the frontend (React JS) hosted by the same beast server (without having to rely on another WS like Apache or similar). The reason is that as there will be hardly five users who will use the frontend on this server (the front-end necessarily have to be rich enough to support JS), and the rest will use only the the backend (beast WebSocket).   
  
Thanks for your advice.  
  
Thanks,  
Tharma

---

## Comment 1

> Username: ashtum  
> Created at: 2024-03-04 14:05:27 UTC  
> Url: https://github.com/boostorg/beast/issues/2833#issuecomment-1976657633  

The HTTP server example functions as a basic static web server and doesn't provide any additional features for the React application. It appears that the index.html has been successfully loaded.  
I suspect the problem might lie with the React application. Are you sure that it has been built properly?

---

## Comment 2

> Username: ashtum  
> Created at: 2024-03-04 15:18:33 UTC  
> Url: https://github.com/boostorg/beast/issues/2833#issuecomment-1976823124  

You can check your browser's developer tools to see if any resources fail to load.

---

## Comment 3

> Username: vtharmalingam  
> Created at: 2024-03-04 15:19:21 UTC  
> Url: https://github.com/boostorg/beast/issues/2833#issuecomment-1976824756  

Thank you, @ashtum. I am debugging and keep you posted.

---

## Comment 4

> Username: vtharmalingam  
> Created at: 2024-03-05 08:32:57 UTC  
> Url: https://github.com/boostorg/beast/issues/2833#issuecomment-1978213736  

You're amazing! Indeed, there was a build issue with that React JS. I was able to figure it out using the "developer tools" as you suggested. The JavaScript and static files weren't generated in the expected directory structure. After resolving that, the Beast server performed flawlessly!  
  
![image](https://github.com/boostorg/beast/assets/7923720/0f71364c-85ae-4763-9459-36ab24d95491)
