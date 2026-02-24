# #191 - CMake Error When Installing [Open]

> Username: ricejasonf  
> Created at: 2017-09-12 18:59:54 UTC  
> Updated at: 2017-09-18 21:43:04 UTC  
> Url: https://github.com/boostorg/hof/issues/191  

It's not clear to me what Fit's dependencies are, if any, but the following error looks like it expects `boost.hpp` to be present in your repo. This is on the `boost` branch.  
  
```  
-- Installing: /opt/install/include/boost/fit/decorate.hpp  
-- Installing: /opt/install/include/boost/fit/lazy.hpp  
-- Installing: /opt/install/include/boost/fit/version.hpp  
-- Installing: /opt/install/include/boost/fit/static.hpp  
CMake Error at cmake_install.cmake:40 (file):  
  file INSTALL cannot find  
  "/usr/local/src/Fit-a5bb8365ee7b1387ba0ec3a21ac393c7cc962488/include/boost.hpp".  
```  
  
I'm running the following script in the root of the repo:  
  
```  
#!/bin/bash  
  
mkdir build && cd build \  
&& cmake \  
    -DCMAKE_TOOLCHAIN_FILE=/opt/toolchain.cmake \  
    -DCMAKE_INSTALL_PREFIX=/opt/install \  
    -DCMAKE_BUILD_TYPE=Release \  
    .. \  
&& make install  
```  
  
cmake version 3.9.0

---

## Comment 1

> Username: pfultz2  
> Created at: 2017-09-12 20:37:46 UTC  
> Url: https://github.com/boostorg/hof/issues/191#issuecomment-328977238  

Yea, this looks like this broke with the rename to boost. This line is supposed to install the fit.hpp header, but its not necessary when moved under the boost directory.

---

## Comment 2

> Username: pfultz2  
> Created at: 2017-09-12 20:41:47 UTC  
> Url: https://github.com/boostorg/hof/issues/191#issuecomment-328978300  

Ah, and this was caught by travis: https://travis-ci.org/pfultz2/Fit/jobs/273190517#L4119

---

## Comment 3

> Username: ricejasonf  
> Created at: 2017-09-18 20:42:23 UTC  
> Url: https://github.com/boostorg/hof/issues/191#issuecomment-330350017  

Also, `fit.hpp` is not in the `boost` directory.

---

## Comment 4

> Username: pfultz2  
> Created at: 2017-09-18 21:43:04 UTC  
> Url: https://github.com/boostorg/hof/issues/191#issuecomment-330365057  

Oops, I forgot that too. Well, I can't change anything until after the review, but thanks for the heads up.
