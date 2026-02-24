# #113 - Can you add redirects [Open]

> Username: afabri  
> Created at: 2022-04-24 08:30:26 UTC  
> Updated at: 2022-05-06 00:06:13 UTC  
> Url: https://github.com/boostorg/unordered/issues/113  

In the documentation of the CGAL library we have links to unordered map and set. As this has changed we have  broken links (see [our issue](https://github.com/CGAL/cgal/issues/6511)).   
Wouldn't it make sense that you add  redirections in the .htacess file  at [https://github.com/boostorg/website/](https://github.com/boostorg/website/ )?

---

## Comment 1

> Username: pdimov  
> Created at: 2022-04-24 16:37:50 UTC  
> Url: https://github.com/boostorg/unordered/issues/113#issuecomment-1107875544  

@glenfe may suggest a course of action here. Adding per-library things to the global .htaccess doesn't seem the right thing to do. Maybe Unordered should have stub html redirects in its dir.

---

## Comment 2

> Username: albert-github  
> Created at: 2022-04-25 09:06:27 UTC  
> Url: https://github.com/boostorg/unordered/issues/113#issuecomment-1108291839  

Problem occurs for:  
- unordered  
- utility

---

## Comment 3

> Username: glenfe  
> Created at: 2022-04-25 13:38:37 UTC  
> Updated at: 2022-04-25 13:38:53 UTC  
> Url: https://github.com/boostorg/unordered/issues/113#issuecomment-1108588224  

Boost as whole only guarantees for a given library or tool that links relative to a specific version will continue to work. These are links relative to doc/libs/VERSION e.g. for Unordered for version 1.78.0 that is doc/libs/1_78_0/doc/html/unordered   
  
i.e. It doesn't guarantee that links relative to libs/unordered or doc/libs/release will continue to work. Individual library authors can guarantee this themselves if they want, but we do not add redirects to .htaccess for this. So Unordered can add HTML files that redirect if they want to preserve compatibility, or they can suggest to users to link to specific versions (not "release" if they plan on breaking those links).  
  
For parts of the Boost website not specific to library documentation, we can add redirects to .htaccess

---

## Comment 4

> Username: cmazakas  
> Created at: 2022-04-25 16:05:38 UTC  
> Updated at: 2022-04-25 16:05:53 UTC  
> Url: https://github.com/boostorg/unordered/issues/113#issuecomment-1108764079  

Ideally dependents of Unordered would use versioned links so these kinds of things don't pop up but if you roll with the Boost release, pinning to `release` makes sense.  
  
It seems like it's pretty straightforward to add a few dummy pages that contain the redirection tag. All we'd have to do is adding something like:  
```  
<meta http-equiv="Refresh" content="0; url='https://www.w3docs.com'" />  
```  
and this problem should sort itself out.

---

## Comment 5

> Username: afabri  
> Created at: 2022-04-26 06:02:45 UTC  
> Url: https://github.com/boostorg/unordered/issues/113#issuecomment-1109379539  

I don't know what you mean with pinned links, but we used `https://www.boost.org/doc/libs/release/doc/html/boost/unordered_set.html`   which got redirected correctly for up to boost version 1.78.0. Starting with 1.79 this fails.

---

## Comment 6

> Username: pdimov  
> Created at: 2022-04-26 09:15:07 UTC  
> Updated at: 2022-04-26 09:16:05 UTC  
> Url: https://github.com/boostorg/unordered/issues/113#issuecomment-1109553654  

Pinned links would mean linking to https://www.boost.org/doc/libs/1_78_0/doc/html/boost/unordered_set.html instead of the latest release.

---

## Comment 7

> Username: afabri  
> Created at: 2022-04-26 17:54:43 UTC  
> Url: https://github.com/boostorg/unordered/issues/113#issuecomment-1110088220  

But even if we had put 1.78 as pinned version there is something you have to fix:    
![image](https://user-images.githubusercontent.com/3263539/165361909-c29ae09c-3a92-4ba4-acb4-6e99ca1c1413.png)  
And when you follow the invitation to "click here" this brings you to  
  
![image](https://user-images.githubusercontent.com/3263539/165362165-b466d486-f1da-446d-a598-1e7c84f201eb.png)  
  
which is the latest release of boost, but not the package.

---

## Comment 8

> Username: glenfe  
> Created at: 2022-05-05 23:21:25 UTC  
> Url: https://github.com/boostorg/unordered/issues/113#issuecomment-1119133359  

@afabri Yes. We need to have a better solution for things like that.

---

## Comment 9

> Username: pdimov  
> Created at: 2022-05-06 00:06:13 UTC  
> Url: https://github.com/boostorg/unordered/issues/113#issuecomment-1119152314  

In this case it's also that the documentation for Unordered switched from "integrated" to "standalone" so the links changed anyway.
