# #413 - boost/gil/extension/io/targa.hpp self sufficiency [Closed]

> Username: ohhmm  
> Created at: 2019-12-21 20:43:56 UTC  
> Updated at: 2019-12-31 18:37:59 UTC  
> Closed at: 2019-12-22 12:44:30 UTC  
> Url: https://github.com/boostorg/gil/issues/413  

<img width="699" alt="Screenshot 2019-12-21 at 22 41 46" src="https://user-images.githubusercontent.com/190779/71313574-4eb33e80-2443-11ea-870c-0a73a5d2ce37.png">  
  
### Environment  
  
<!-- Any details that may help GIL maintainers help you -->  
  
- Version (Git ref or `<boost/version.hpp>`): 1.72.0

---

## Comment 1

> Username: mloskot  
> Created at: 2019-12-22 12:44:30 UTC  
> Url: https://github.com/boostorg/gil/issues/413#issuecomment-568258799  

First, `boost/gil/version.hpp` has been removed as deprecated in PR #403  abd I believe it has been effectively removed from all source files documentation and build configuration.  
  
I can not see any left over `version.hpp` in the [master branch](https://github.com/boostorg/gil/tree/master) or [boost-1.72.0](https://github.com/boostorg/gil/tree/boost-1.72.0) tag.  
  
I also downloaded `boost_1_72_0.7z` from https://dl.bintray.com/boostorg/release/1.72.0/source/, unpacked and searched `libs/gil/` content for `version.hpp` - nothing found:  
  
![image](https://user-images.githubusercontent.com/80741/71321887-a17f0b80-24c0-11ea-9213-8d31b8a8adee.png)  
  
I cannot reproduce your problem.
