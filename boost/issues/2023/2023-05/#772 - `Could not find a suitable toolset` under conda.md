# #772 - `Could not find a suitable toolset` under conda [Open]

> Username: multimeric  
> Created at: 2023-05-04 01:16:44 UTC  
> Updated at: 2023-05-04 01:18:56 UTC  
> Url: https://github.com/boostorg/boost/issues/772  

Boost seems to have a unique way of identifying toolsets which does not integrate well with Conda's model of installing and providing a compiler. In essence the problem is that Conda's compilers have a path such as `/opt/conda/bin/x86_64-conda-linux-gnu-gcc`, so the binary `gcc` or `g++` is *not* on the `$PATH`. However, Conda correctly sets the `$CC` and `$CXX` etc variables which most build systems are able to detect. However due to the way that `bootstrap.sh` and `build.sh` seem to work, this is ignored, and instead a user will receive `Could not find a suitable toolset`.   
  
I think it would be great to think about how Boost could support a workflow such as this.  
  
## Example  
  
Below is a reproducible example. It runs in docker or podman because this is the easiest way to demonstrate a system that has no system gcc but also has conda pre-installed. I use `[...]` to indicate omitted output for brevity.  
  
```bash  
$ podman run -it continuumio/miniconda3 bash  
$ which gcc  
$ echo $CC  
$ echo $CXX  
```  
At this point note that we have no system compiler installed at all. This is important for demonstration.  
  
```bash  
$ conda install -c conda-forge gcc_linux-64=10.4.0 gxx_linux-64=10.4.0 cmake=3.26.3 make=4.3  
Collecting package metadata (current_repodata.json): done  
Solving environment: failed with initial frozen solve. Retrying with flexible solve.  
Collecting package metadata (repodata.json): done  
Solving environment: done  
[...]  
$ which gcc  
$ echo $CC  
/opt/conda/bin/x86_64-conda-linux-gnu-cc  
$ echo $CXX  
/opt/conda/bin/x86_64-conda-linux-gnu-c++  
```  
  
Now that we have installed a compiler via conda, note that `$CC` and `$CXX` are set, but that there is no `gcc` in the PATH.  
  
```bash  
$ cd /root  
$ wget https://boostorg.jfrog.io/artifactory/main/release/1.82.0/source/boost_1_82_0.tar.gz  
--2023-05-04 00:54:07--  https://boostorg.jfrog.io/artifactory/main/release/1.82.0/source/boost_1_82_0.tar.gz  
Resolving boostorg.jfrog.io (boostorg.jfrog.io)... 54.184.3.201, 34.213.254.59, 54.184.100.230, ...  
Connecting to boostorg.jfrog.io (boostorg.jfrog.io)|54.184.3.201|:443... connected.  
HTTP request sent, awaiting response... 302  
[...]  
$ tar xzf boost_1_82_0.tar.gz  
$ cd boost_1_82_0  
$ ./bootstrap.sh  
Could not find a suitable toolset.  
  
You can specify the toolset as the argument, i.e.:  
    ./build.sh [options] gcc  
[...]  
$ ./bootstrap.sh --with-toolset=gcc  
Building B2 engine..  
  
A C++11 capable compiler is required for building the B2 engine.  
Toolset 'gcc' does not appear to support C++11.  
  
> g++ -x c++ -std=c++11  check_cxx11.cpp  
./tools/build/src/engine/build.sh: 120: g++: not found  
> g++ -x c++ -std=c++11 -D_GNU_SOURCE  check_cxx11.cpp  
./tools/build/src/engine/build.sh: 120: g++: not found  
> g++ -x c++ -std=c++11 -pthread  check_cxx11.cpp  
./tools/build/src/engine/build.sh: 120: g++: not found  
[...]  
```

---

## Comment 1

> Username: multimeric  
> Created at: 2023-05-04 01:18:56 UTC  
> Url: https://github.com/boostorg/boost/issues/772#issuecomment-1533942116  

Note also that conda has to patch Boost to resolve this issue: https://github.com/conda-forge/boost-feedstock/blob/main/recipe/5eff1ecc8413b0dc93a1ab047d7fed751e6cb40e.patch.  
Although this is sufficient for installing Boost via conda, it doesn't resolve the use case I'm encountering, where a third party script tries to install Boost from source inside a conda environment, but without using conda to do the installation.
