# #1052 - boost/math/tools/constexpr_workarounds.hpp not found [Closed]

> Username: WillemvJ  
> Created at: 2023-11-17 21:01:55 UTC  
> Updated at: 2023-11-18 09:14:00 UTC  
> Closed at: 2023-11-18 07:30:35 UTC  
> Url: https://github.com/boostorg/math/issues/1052  

I am trying to use boost/math as a submodule in a program with c++20, but I am getting problems. I added boost.math as a submodule like so:  
  
git submodule add https://github.com/boostorg/math.git src/extern/boost_math  
  
(I also tried adding specifically the master branch)   
  
I include it in my root cmakelists like so:  
  
add_subdirectory(src/extern/boost_math)  
  
I add an executable and link it like so:  
  
target_link_libraries(${targetname} PRIVATE <other dependencies> Boost::math)  
  
Now, I make a small test program in which I do  
  
#include <boost/math/distributions/binomial.hpp>  
  
However, I get:  
  
Error	C1083	Cannot open include file: 'boost/math/tools/constexpr_workarounds.hpp': No such file or directory	  
  
this is inside math/special_functions/round.hpp and one other spot.   
  
Am I trying something which is not supported? Or am I doing something wrong? The file really does not seem to reside in tools folder.

---

## Comment 1

> Username: Lastique  
> Created at: 2023-11-17 23:21:17 UTC  
> Url: https://github.com/boostorg/math/issues/1052#issuecomment-1817242037  

This affects Boost.Integer tests, see [here](https://ci.appveyor.com/project/Lastique/integer/builds/48552063/job/84cb18k4idqctfv4#L189) for example.  
  
And the problem is also present in master, so it also affects the upcoming 1.84 release.  
  
Looks like this was introduced in d938d3e836. CC @jzmaddock.

---

## Comment 2

> Username: pdimov  
> Created at: 2023-11-18 01:31:22 UTC  
> Url: https://github.com/boostorg/math/issues/1052#issuecomment-1817311307  

Yeah, I also noticed this and was going to open an issue, but I see others have already done so. :-)  
  
Looks like some code changes got committed by mistake.

---

## Comment 3

> Username: ckormanyos  
> Created at: 2023-11-18 03:48:50 UTC  
> Updated at: 2023-11-18 03:49:07 UTC  
> Url: https://github.com/boostorg/math/issues/1052#issuecomment-1817359470  

Hi Matt and John (@jzmaddock) and (@mborland).  
  
I can [confirm this error](https://github.com/ckormanyos/wide-decimal/actions/runs/6910963485/job/18804880146#step:5:30) also in nightly CI for several of my own projects that use math.  
  
Maybe forgot to add the file to source control?

---

## Comment 4

> Username: mborland  
> Created at: 2023-11-18 07:30:35 UTC  
> Url: https://github.com/boostorg/math/issues/1052#issuecomment-1817424399  

Commit has been reverted on master. Like Peter said it looks like a few working changes accidentally got picked up in the doc update. Sorry about that.

---

## Comment 5

> Username: ckormanyos  
> Created at: 2023-11-18 08:38:47 UTC  
> Url: https://github.com/boostorg/math/issues/1052#issuecomment-1817446147  

> Commit has been reverted on master.   
  
... and develop.  
  
Thank you Matt (@mborland). Now perfectly fine are all 3 of my failing CIs in unrelated projects.

---

## Comment 6

> Username: jzmaddock  
> Created at: 2023-11-18 09:14:00 UTC  
> Url: https://github.com/boostorg/math/issues/1052#issuecomment-1817453167  

OMG, really really sorry about that, and I do always review the diffs before committing, but somehow missed that! :(
