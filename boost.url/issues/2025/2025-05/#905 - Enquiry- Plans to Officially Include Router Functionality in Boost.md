# #905 - Enquiry: Plans to Officially Include Router Functionality in Boost [Closed]

> Username: kkalbhor-git  
> Created at: 2025-05-06 08:15:54 UTC  
> Updated at: 2025-05-21 21:19:56 UTC  
> Closed at: 2025-05-21 21:19:56 UTC  
> Url: https://github.com/boostorg/url/issues/905  

Hi Boost Maintainers,  
  
While working with Boost 1.83.0 (on Debian Trixie), we noticed that the router functionality is available under the (https://github.com/boostorg/url/tree/develop/example/router), but it is not part of the official Boost libraries.  
  
We are currently maintaining our own routing logic as part of our project, but we would prefer to rely on a standardized, supported Boost component if possible.  
  
Could you please confirm:  
  
- If there are any ongoing efforts or future plans to promote the router example into an officially supported Boost library?  
- If not, is there a roadmap or recommended path for contributions in this area?  
  
Looking forward to your guidance.  
  
Best regards,  
Ketan Kalbhor  
ketan.kalbhor@hitachivantara.com

---

## Comment 1

> Username: alandefreitas  
> Created at: 2025-05-06 15:11:36 UTC  
> Url: https://github.com/boostorg/url/issues/905#issuecomment-2854931124  

> If there are any ongoing efforts or future plans to promote the router example into an officially supported Boost library?  
  
Not that I know of. You could ask in the mailing list if you really want to be sure, but I don't think so.  
  
> If not, is there a roadmap or recommended path for contributions in this area?  
  
It depends on the format of your contribution. If you want to write a boost library for routing, I'd suggest you discuss that in the #boost slack channel first (http://cpplang.slack.com) and then start a formal discussion in the ML if people seem positive about it.
