# #30 - How to build the tests? [Closed]

> Username: codimoc  
> Created at: 2022-02-19 00:12:28 UTC  
> Updated at: 2022-10-01 18:07:20 UTC  
> Closed at: 2022-10-01 18:07:20 UTC  
> Url: https://github.com/boostorg/describe/issues/30  

Hi  
I am interested in understanding this library for some possible extension to the enumerations utilities.  
  
However I am struggling to build and run the tests with the cmake provided.  
  
This is what I am doing:  
git clone ....  into new directory describe  
cd describe  
mkdir build  
cd build  
cmake ..  
cmake --build .  
  
No errors but the test are not built and the final build stage returns immediately.  
  
What am I doing wrong?  
Thanks

---

## Comment 1

> Username: pdimov  
> Created at: 2022-02-19 00:23:06 UTC  
> Updated at: 2022-02-19 16:05:46 UTC  
> Url: https://github.com/boostorg/describe/issues/30#issuecomment-1045418761  

The tests don't work without the Boost superproject. You would have to clone it first  
```  
git clone -b develop --depth 1 https://github.com/boostorg/boost.git boost-root  
```  
then update the necessary submodules  
```  
cd boost-root  
git submodule update --init tools/boostdep  
python tools/boostdep/depinst/depinst.py -u describe  
```  
and then configure, build and test using CMake  
```  
mkdir __build__ && cd __build__  
cmake -DBOOST_INCLUDE_LIBRARIES=describe -DBUILD_TESTING=ON ..  
cmake --build . --target tests  
ctest --output-on-failure --no-tests=error  
```  
I see that that I haven't included CMake tests in my GHA CI, so maybe I need to do so.

---

## Comment 2

> Username: codimoc  
> Created at: 2022-02-19 09:40:26 UTC  
> Url: https://github.com/boostorg/describe/issues/30#issuecomment-1045978508  

Super. Thx!
