# #1281 Fixes issue produced by exchanges that send non-printable characters … [Closed]

> Username: konradstrachan  
> Created at: 2018-10-30 17:18:55 UTC  
> Updated at: 2019-11-02 15:22:02 UTC  
> Closed at: 2019-11-02 15:22:02 UTC  
> Url: https://github.com/boostorg/beast/pull/1281  

…mid-way through the header (for example, as part of the cookie)

---

## Comment 1

> Username: vinniefalco  
> Created_at: 2018-10-30 20:10:44 UTC  
> Url: https://github.com/boostorg/beast/pull/1281#issuecomment-434451445  

I appreciate the contribution, but this would make the parser accept invalid HTTP messages. Expecting a library to account for all ways in the world that servers can send malformed data is not only unrealistic, but would negatively impact the security of the library (see:  https://vinniefalco.github.io/BeastAssets/Beast%20-%20Hybrid%20Application%20Assessment%202017%20-%20Assessment%20Report%20-%2020171114.pdf).  
  
This issue has been raised before, so I am thinking that a better way forward might be to add a hook to the parser's derived class which allows it to optionally rewrite each line of the header. Then you can make a parser instance that handles the specific problem you are trying to solve, and it won't affect any downstream Beast users.

---

## Comment 2

> Username: konradstrachan  
> Created_at: 2018-10-31 09:42:06 UTC  
> Url: https://github.com/boostorg/beast/pull/1281#issuecomment-434622328  

> I appreciate the contribution, but this would make the parser accept invalid HTTP messages. Expecting a library to account for all ways in the world that servers can send malformed data is not only unrealistic, but would negatively impact the security of the library (see: https://vinniefalco.github.io/BeastAssets/Beast%20-%20Hybrid%20Application%20Assessment%202017%20-%20Assessment%20Report%20-%2020171114.pdf).  
>   
> This issue has been raised before, so I am thinking that a better way forward might be to add a hook to the parser's derived class which allows it to optionally rewrite each line of the header. Then you can make a parser instance that handles the specific problem you are trying to solve, and it won't affect any downstream Beast users.  
  
Hi Vinnie, thanks for the quick response. As a temporary measure, how about we #if 0 it as per your work-around for '\n' terminator? Failing that, do you have an idea for when such a parser change might be ready?

---

## Comment 3

> Username: vinniefalco  
> Created_at: 2019-01-22 00:18:21 UTC  
> Url: https://github.com/boostorg/beast/pull/1281#issuecomment-456230517  

Heads up, I haven't forgotten about this by any means, but currently I am in the middle of big revamping of websocket for 1.70.

---

## Comment 4

> Username: vinniefalco  
> Created_at: 2019-11-02 15:22:02 UTC  
> Url: https://github.com/boostorg/beast/pull/1281#issuecomment-549053379  

Can't do this, non-compliant parsing is out of scope.

---
