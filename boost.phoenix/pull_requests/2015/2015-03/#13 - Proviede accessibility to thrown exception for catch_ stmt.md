# #13 Proviede accessibility to thrown exception for catch_ stmt. [Merged]

> Username: Flast  
> Created at: 2015-03-27 04:02:12 UTC  
> Updated at: 2015-03-27 08:48:24 UTC  
> Merged at: 2015-03-27 08:36:22 UTC  
> Closed at: 2015-03-27 08:36:22 UTC  
> Url: https://github.com/boostorg/phoenix/pull/13  

Hi @fletchjp and @djowel !  
  
This PR contains a feature enhancement which provides accessibility to thrown exception for catch_ stmt.  
e.g.  
  
``` c++  
try_  
[  
    throw_(runtime_error("error"))  
]  
.catch_<exception>(local_names::e_) // Note, accepts local variables only.  
[  
    std::cout << bind(&exception::what, local_names::e_) << std::endl;  
]  
```  
  
If OK to merge, I will push one more patch which just regenerating preprocessed files: 401968b7622fd6f672ce6fa4db7a36a359ec733c.  
  
see also: [#5604](https://svn.boost.org/trac/boost/ticket/5604)

---

## Comment 1

> Username: djowel  
> Created_at: 2015-03-27 05:23:31 UTC  
> Url: https://github.com/boostorg/phoenix/pull/13#issuecomment-86829166  

nice! looks good to me!

---

## Comment 2

> Username: fletchjp  
> Created_at: 2015-03-27 08:31:20 UTC  
> Url: https://github.com/boostorg/phoenix/pull/13#issuecomment-86868324  

Kohei  
  
Thank you again.  This looks good.  
  
John  
  
---  
  
From: Joel de Guzman [notifications@github.com]  
Sent: 27 March 2015 05:23  
To: boostorg/phoenix  
Cc: Fletcher, John P  
Subject: Re: [phoenix] Proviede accessibility to thrown exception for catch_ stmt. (#13)  
  
nice! looks good to me!  
  
�  
Reply to this email directly or view it on GitHubhttps://github.com/boostorg/phoenix/pull/13#issuecomment-86829166.

---

## Comment 3

> Username: Flast  
> Created_at: 2015-03-27 08:48:24 UTC  
> Url: https://github.com/boostorg/phoenix/pull/13#issuecomment-86870996  

Thanks!

---
