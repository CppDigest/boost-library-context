# #1063 - Error on download files from JFrog - account deactivated [Closed]

> Username: sersoftin  
> Created at: 2025-07-17 10:16:06 UTC  
> Updated at: 2025-07-17 13:37:34 UTC  
> Closed at: 2025-07-17 13:37:34 UTC  
> Url: https://github.com/boostorg/boost/issues/1063  

When Im trying to install my conan deps (boost/1.85.0) - im getting error.  
  
```  
boost/1.85.0: WARN: Could not download from the URL https://boostorg.jfrog.io/artifactory/main/release/1.85.0/source/boost_1_85_0.tar.bz2: sha256 signature failed for 'boost_1_85_0.tar.bz2' file.   
 Provided signature: 7009fe1faa1697476bdc7027703a2badb84e849b7b0baad5086b087b971f8617    
 Computed signature: 07911da8fe22fb10e3918528a8f0a5676f03c0b3b135ac19c26779f6804baebe.  
```  
  
When I navigate to this URL with browser - getting landing page about account reactivate.  
  
How to fix it?

---

## Comment 1

> Username: mclow  
> Created at: 2025-07-17 13:37:34 UTC  
> Url: https://github.com/boostorg/boost/issues/1063#issuecomment-3084107569  

Boost stopped using jfrog for downloads more than a year ago.  
The URL that you want is to download from is `https://archives.boost.io/release/1.85.0/source/boost_1_85_0.tar.bz2`
