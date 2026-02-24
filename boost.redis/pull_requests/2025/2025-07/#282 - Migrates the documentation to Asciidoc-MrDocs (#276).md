# #282 Migrates the documentation to Asciidoc/MrDocs (#276) [Merged]

> Username: mzimbres  
> Created at: 2025-07-03 07:44:06 UTC  
> Updated at: 2025-07-08 19:07:52 UTC  
> Merged at: 2025-07-08 19:07:52 UTC  
> Closed at: 2025-07-08 19:07:52 UTC  
> Url: https://github.com/boostorg/redis/pull/282  



---

## Comment 1

> Username: anarthal  
> Created_at: 2025-07-08 19:07:33 UTC  
> Url: https://github.com/boostorg/redis/pull/282#issuecomment-3050022486  

I've talked to the web team and the glitches should be fixed by https://github.com/boostorg/website-v2/pull/1840  
Sam confirmed me on slack that we won't break the build even if we didn't do #281 yet  
I've re-run the failing jobs and they're now successful. They were failing due to an Asio regression that's been fixed now.  
  
I'm merging this today for the beta.

---
