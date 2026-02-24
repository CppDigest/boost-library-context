# #1068 Update release-cmake.yml [Closed]

> Username: userdocs  
> Created at: 2025-08-01 23:42:20 UTC  
> Updated at: 2025-08-05 22:14:14 UTC  
> Closed at: 2025-08-05 22:14:14 UTC  
> Url: https://github.com/boostorg/boost/pull/1068  

do not make beta releases latest releases. As you don't specify `make_latest` it defaults to github api defaults, which make any new release latest.  
  
https://github.com/softprops/action-gh-release?tab=readme-ov-file#inputs  
  
This will set it to true for non beta releases and false for beta releases.  
  
here is an example run  
  
https://github.com/userdocs/test/releases  
  
<img width="1246" height="875" alt="image" src="https://github.com/user-attachments/assets/29d0415f-5315-4a0a-ac02-ff2bb21f0183" />  
  
<img width="1267" height="1023" alt="image" src="https://github.com/user-attachments/assets/42d93222-8953-4e6a-9833-1ab3b1d60547" />

---
