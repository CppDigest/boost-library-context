# #761 Add support for QNX 7.1 build [Open]

> Username: chachoi  
> Created at: 2023-04-05 21:56:04 UTC  
> Updated at: 2023-04-05 21:56:04 UTC  
> Url: https://github.com/boostorg/boost/pull/761  

Added QNX build files under the build_qnx folder.  
  
Instructions for building with QNX 7.1:  
  
1. Setup the boost repo:  
```bash  
git clone https://github.com/chachoi/boost.git && cd boost  
git checkout develop  
```  
2. Replace "url = ../" in .gitmodules with "url = https://github.com/boostorg/". This step won't be necessary once this PR is merged.  
3. Init submodules:  
```bash  
git submodule update --init --recursive  
```  
4. Change "QCC" in ./tools/build/src/tools/qcc.jam line 41 to "qcc". I submitted a [PR](https://github.com/bfgroup/b2/pull/237) for this change. This step won't be necessary once that PR is merged into https://github.com/boostorg/build.  
  
5. Source QNX 7.1 environment script:  
```bash  
source <path-to-script>/qnxsdp-env.sh  
```  
6. Build and install  
```bash  
cd build_qnx  
make -j 4 install  
```  
7. Optional: build a test:  
```bash  
# Example: make test.<lib name> -j 4  
make test.math -j 4  
```

---
