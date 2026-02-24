# #944 - MSVC 1941 is not supported when using boost from cmake [Closed]

> Username: imrichardcole  
> Created at: 2024-08-23 11:48:54 UTC  
> Updated at: 2024-08-24 07:55:48 UTC  
> Closed at: 2024-08-24 07:55:36 UTC  
> Url: https://github.com/boostorg/boost/issues/944  

The generated cmake file - BoostDetectToolset-1.85.0.cmake includes the following:  
  
`92 - if((MSVC_VERSION GREATER 1929) AND (MSVC_VERSION LESS 1940))`  
  
Which is used to detect the vc143 toolset.  I'm using the latest compiler for v143 and this version comes back as 1941 so I get an unsupported toolset error when trying to use boost in my cmake project.  
  
If I change the above to:  
  
`92 - if((MSVC_VERSION GREATER 1929) AND (MSVC_VERSION LESS 1941))`  
  
And it works.  I'm happy to raise a PR for this if people agree it's a good step forward.

---

## Comment 1

> Username: imrichardcole  
> Created at: 2024-08-23 11:59:42 UTC  
> Url: https://github.com/boostorg/boost/issues/944#issuecomment-2306944805  

Apologies - it seems this is already fixed in 1.86.0:  
  
https://github.com/boostorg/boost_install/commit/3f67136ec1e7855f29c40db8949e763ff73c840f

---

## Comment 2

> Username: imrichardcole  
> Created at: 2024-08-24 07:55:48 UTC  
> Url: https://github.com/boostorg/boost/issues/944#issuecomment-2308179305  

Not needed as fixed in latest version
