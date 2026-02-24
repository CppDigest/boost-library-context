# #3011 - Examples in docs are broken (404) [Open]

> Username: Zen0x7  
> Created at: 2025-05-26 15:23:16 UTC  
> Updated at: 2025-05-27 18:45:15 UTC  
> Url: https://github.com/boostorg/beast/issues/3011  

Hi  
  
I was looking the docs and the boost documentation version upgrade is great but something is currently broken...  
  
https://www.boost.org/doc/libs/1_87_0/libs/beast/example/websocket/server/chat-multi/ throws 404.

---

## Comment 1

> Username: ashtum  
> Created at: 2025-05-26 15:27:55 UTC  
> Updated at: 2025-05-26 15:30:55 UTC  
> Url: https://github.com/boostorg/beast/issues/3011#issuecomment-2910103736  

This seems like an issue with the new website. Could you please open an issue here:  
https://github.com/boostorg/website-v2/issues  
In the meantime, you can use:  
https://original.boost.org/doc/libs/1_87_0/libs/beast/example/websocket/server/chat-multi/

---

## Comment 2

> Username: sdarwin  
> Created at: 2025-05-26 16:19:10 UTC  
> Url: https://github.com/boostorg/beast/issues/3011#issuecomment-2910209553  

@ashtum docs on the new website are stored in AWS S3.  Although "anything is possible" with enough code I believe that out-of-the-box that service does not permit (or easily enable) directory browsing.     
  
It seems /chat-multi/ doesn't take the visitor to a webpage, but rather tries to display a directory listing with many pages:  
  
http_session.cpp  
shared_state.hpp  
listener.hpp  
websocket_session.cpp  
shared_state.cpp  
beast.hpp  
main.cpp  
http_session.hpp  
listener.cpp  
websocket_session.hpp  
CMakeLists.txt  
chat_client.html  
net.hpp  
Jamfile  
  
Could you create this table of contents in the beast documentation?  And link to individual files.   Not to a whole directory.  
  
Or include this reference:   https://github.com/boostorg/beast/tree/develop/example/websocket/server/chat-multi

---

## Comment 3

> Username: ashtum  
> Created at: 2025-05-26 16:29:47 UTC  
> Url: https://github.com/boostorg/beast/issues/3011#issuecomment-2910226841  

> [@ashtum](https://github.com/ashtum) docs on the new website are stored in AWS S3. Although "anything is possible" with enough code I believe that out-of-the-box that service does not permit (or easily enable) directory browsing.  
>   
> It seems /chat-multi/ doesn't take the visitor to a webpage, but rather tries to display a directory listing with many pages:  
>   
> http_session.cpp shared_state.hpp listener.hpp websocket_session.cpp shared_state.cpp beast.hpp main.cpp http_session.hpp listener.cpp websocket_session.hpp CMakeLists.txt chat_client.html net.hpp Jamfile  
>   
> Could you create this table of contents in the beast documentation? And link to individual files. Not to a whole directory.  
>   
> Or include this reference: https://github.com/boostorg/beast/tree/develop/example/websocket/server/chat-multi  
  
Yes, instead of linking to a directory path, we can list the related files in docs:  
https://www.boost.org/doc/libs/latest/libs/beast/doc/html/beast/examples.html#beast.examples.chat_server

---

## Comment 4

> Username: vinniefalco  
> Created at: 2025-05-26 22:33:29 UTC  
> Url: https://github.com/boostorg/beast/issues/3011#issuecomment-2910713851  

is it possible that this was a function of the old website? there could be a php script which turns the directory contents into a webpage?

---

## Comment 5

> Username: sdarwin  
> Created at: 2025-05-26 22:47:04 UTC  
> Url: https://github.com/boostorg/beast/issues/3011#issuecomment-2910724177  

> is it possible  
  
yes, of course that's it.    
however, it's worthwhile to review boost libraries to see how frequently this feature is used. maybe rarely. If that is the case,   specific index.html files could be generated, or hyperlink out to github.com's directories.   Are there any other examples of the issue...

---

## Comment 6

> Username: vinniefalco  
> Created at: 2025-05-27 17:16:47 UTC  
> Updated at: 2025-05-27 17:17:01 UTC  
> Url: https://github.com/boostorg/beast/issues/3011#issuecomment-2913350788  

It would be better for library documentation to not rely on an external facility which converts a filesystem directory into an index page. Maybe it could be a tool which the library explicitly invokes?

---

## Comment 7

> Username: Zen0x7  
> Created at: 2025-05-27 18:45:14 UTC  
> Url: https://github.com/boostorg/beast/issues/3011#issuecomment-2913577389  

I'll link the related issue from the other repository.  
  
IMHO, this should aim for a retroactive solution (considering older versions too), and ideally follow a Liskov-like principle — in other words, it would be great if the previous behavior could be restored with as little overhead as possible.  
  
https://github.com/boostorg/website-v2/issues/1813
