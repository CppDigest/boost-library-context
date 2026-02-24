# #201 - Is the website for the document down? [Closed]

> Username: jkooooo99  
> Created at: 2024-10-24 03:06:05 UTC  
> Updated at: 2024-10-25 02:44:41 UTC  
> Closed at: 2024-10-24 22:39:01 UTC  
> Url: https://github.com/boostorg/parser/issues/201  

Hi, I just found out that I can no longer access the website "https://tzlaine.github.io/parser/"...  
  
I am still a newbie and heavily rely on straightforward tutorials when I'm learning C++ and Boost. I found sprirtX3 first, and then it seems like Parser has a more beginner-friendly tutorial/document. I really hope the website could be back! Thanks!!

---

## Comment 1

> Username: tzlaine  
> Created at: 2024-10-24 22:39:01 UTC  
> Url: https://github.com/boostorg/parser/issues/201#issuecomment-2436462781  

The bad news: Yes, the Github docs are down, and this is permanent; the project is now part of Boost, and the Boost.Parser docs will be part of the official Boot docs on the Boost website.  The good news: the docs are in the repo, and you can still access them.  So, until the docs are up on the Boost website, you can do this:  
  
```term  
git clone [github path] parser  
cd parser  
git switch gh-pages  
```  
  
Then point your browser to `parser/doc/html/index.html`.

---

## Comment 2

> Username: jkooooo99  
> Created at: 2024-10-25 02:44:40 UTC  
> Url: https://github.com/boostorg/parser/issues/201#issuecomment-2436698790  

BIG THANKS!!!!!!
