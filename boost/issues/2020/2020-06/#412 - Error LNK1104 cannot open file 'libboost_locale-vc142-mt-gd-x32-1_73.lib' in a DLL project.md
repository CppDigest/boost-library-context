# #412 - Error LNK1104 cannot open file 'libboost_locale-vc142-mt-gd-x32-1_73.lib' in a DLL project. [Closed]

> Username: KulaGGin  
> Created at: 2020-06-22 18:04:11 UTC  
> Updated at: 2020-06-24 18:22:30 UTC  
> Closed at: 2020-06-24 18:22:30 UTC  
> Url: https://github.com/boostorg/boost/issues/412  

I am following the [Getting Started guide](https://www.boost.org/doc/libs/1_73_0/more/getting_started/windows.html#build-from-the-visual-studio-ide) and it works in the example command line project but in a DLL project I get this error when I try to build the solution:  
![devenv_IOODSFFMqc](https://user-images.githubusercontent.com/16989713/85331181-58932880-b4de-11ea-86e4-5dc569491338.png)  
  
I downloaded boost_1_73_0 from here: https://www.boost.org/users/history/version_1_73_0.html  
  
I have the downloaded and extracted library at `D:\Projects\Libraries\boost_1_73_0`:  
![explorer_p0pfs1LXdq](https://user-images.githubusercontent.com/16989713/85331360-aa3bb300-b4de-11ea-8d52-4bb74568c190.png)  
  
I have set the path in the `Configuration Properties > C/C++ > General > Additional Include Directories`:  
![devenv_xMYDHCklYZ](https://user-images.githubusercontent.com/16989713/85331429-c5a6be00-b4de-11ea-9ca1-456d45f76a73.png)  
  
Here's my barebones solution with the problem:  
https://github.com/KulaGGin/TestLibrary/tree/LNK1104LibBoost  
  
It's on the LNK1104LibBoost branch.

---

## Comment 1

> Username: KulaGGin  
> Created at: 2020-06-24 18:22:28 UTC  
> Url: https://github.com/boostorg/boost/issues/412#issuecomment-648987691  

It was my mistake, I used a header that require compilation.
