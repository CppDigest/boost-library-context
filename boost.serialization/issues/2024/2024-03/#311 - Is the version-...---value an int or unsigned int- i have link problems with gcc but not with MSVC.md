# #311 - [Question] Is the version<...>::value an int or unsigned int? i have link problems with gcc but not with MSVC [Open]

> Username: LowLevelMahn  
> Created at: 2024-03-25 15:43:46 UTC  
> Updated at: 2024-06-10 16:49:47 UTC  
> Url: https://github.com/boostorg/serialization/issues/311  

i've got an tiny example (its a cut-out, reduced, self-contained test from part of an ongoing Windows/MSVC-->Linux/gcc porting project) - using Boost 1.84  
  
https://github.com/LowLevelMahn/linux_link_error   
build-intructions for windows/linux: https://github.com/LowLevelMahn/linux_link_error/blob/main/build.txt  
  
that links with MSVC but not with gcc   
  
it links on linux if i change the following line from `unsigned int` to `int`  
  
https://github.com/LowLevelMahn/linux_link_error/blob/9961ddb30cbbb927989df0fecb02b27c30ac45d2/linux_link_error_lib/serialize_MyType.hpp#L15  
  
questions:  
  
its unclear to me why it works with MSVC but not with gcc/ld/lld/mold linker  
but i see differences in some signatures, the BOOST_CLASS_VERSION macro, for example, im not using it, is using `int` for the version::value - most examples using the macro BOOST_STATIC_CONSTANT with `unsigned int` - isn't that incorrect and it should be at all places `int` or `unsigned int` but not mixed? also the save and load functions using `unsigned int` for the version but it seems that the value "should" be or is `int` inside - im a little confused here  
  
any idea why the code links without problems on windows/MSVC?  
  
my system:  
  
Windows 10/MSVC 2022 latest  
  
Linux:  
recent Tumbleweed SUSE  
gcc 13.2.1  
clang 18.1.1  
ld: 2.42.0  
lld: 18.1.1  
mold: 2.30.0  
  
my boost build:  
  
my boost build: from source on linux and windows  
wget   
https://boostorg.jfrog.io/artifactory/main/release/1.84.0/source/boost_1_84_0.tar.bz2  
tar -xf boost_1_84_0.tar.bz2  
cd boost_1_84_0  
./bootstrap.sh --prefix=/home/testing/boost_1_84_0_install  
./b2 install

---

## Comment 1

> Username: LowLevelMahn  
> Created at: 2024-06-10 07:54:18 UTC  
> Url: https://github.com/boostorg/serialization/issues/311#issuecomment-2157592590  

any ideas on that?

---

## Comment 2

> Username: robertramey  
> Created at: 2024-06-10 16:49:46 UTC  
> Url: https://github.com/boostorg/serialization/issues/311#issuecomment-2158858642  

sorry I haven't found time to look into this.  It's not uninteresting to me.  Feel free to remind me again if I don't get on it soon enough.
