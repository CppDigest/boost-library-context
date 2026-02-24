# #203 - Missing files/directories when building Boost from master branch (VS2017/win64) [Closed]

> Username: oysteinskotheim  
> Created at: 2017-06-13 18:51:31 UTC  
> Updated at: 2017-06-13 19:06:21 UTC  
> Closed at: 2017-06-13 19:06:21 UTC  
> Url: https://github.com/boostorg/build/issues/203  

I am trying to build and install latest master branch of Boost by running the following commands:  
   
  bootstrap  
  b2 --build-type=complete address-model=64 toolset=msvc runtime-link=shared stage  
  b2 --build-type=complete address-model=64 toolset=msvc runtime-link=shared install  
  
I am using Visual Studio 2017 (x64) on Windows 10.  
  
No errors are encountered during the build. However, there are a number of missing files and folders in the C:\Boost\include\boost-1_65 after running the install step. (The symlinks/junctions for these files are also missing in the boost subdirectory that I have cloned from GIT)  
  
Below I show the additional files that get installed when I build and install Boost 1.64. These files and folders do not appear when building and installing the master branch.   
  
* Missing directories (top-level)  
  accumulators  
  assign  
  bimap  
  circular_buffer  
  compatibility  
  compute  
  concept_check  
  convert  
  coroutine2  
  dll  
  endian  
  flyweight  
  format  
  geometry  
  gil  
  hana  
  heap  
  icl  
  lambda  
  local_function  
  lockfree  
  logic  
  metaparse  
  msm  
  multiprecision  
  multi_array  
  polygon  
  process  
  ptr_container  
  qvm  
  signals2  
  sort  
  statechart  
  tr1  
  tti  
  units  
  uuid  
  vmd  
  
* Missing files (top-level)  
  align.hpp  
  asio.hpp  
  assign.hpp  
  bimap.hpp  
  circular_buffer.hpp  
  circular_buffer_fwd.hpp  
  compute.hpp  
  convert.hpp  
  crc.hpp  
  cstdfloat.hpp  
  cxx11_char_types.hpp  
  date_time.hpp  
  dll.hpp  
  filesystem.hpp  
  flyweight.hpp  
  format.hpp  
  functional.hpp  
  function_output_iterator.hpp  
  generator_iterator.hpp  
  geometry.hpp  
  hana.hpp  
  last_value.hpp  
  locale.hpp  
  local_function.hpp  
  make_default.hpp  
  make_unique.hpp  
  metaparse.hpp  
  mpi.hpp  
  multi_array.hpp  
  nondet_random.hpp  
  parameter.hpp  
  phoenix.hpp  
  pointer_cast.hpp  
  polymorphic_pointer_cast.hpp  
  process.hpp  
  program_options.hpp  
  progress.hpp  
  random.hpp  
  ratio.hpp  
  regex.h  
  scope_exit.hpp  
  shared_container_iterator.hpp  
  signal.hpp  
  signals.hpp  
  signals2.hpp  
  spirit.hpp  
  wave.hpp

---

## Comment 1

> Username: grafikrobot  
> Created at: 2017-06-13 19:06:21 UTC  
> Url: https://github.com/boostorg/build/issues/203#issuecomment-308217126  

https://github.com/boostorg/boost/wiki/Getting-Started#installing-boost
