# #419 - missing argument project-module [Closed]

> Username: Kojoley  
> Created at: 2019-03-29 23:10:10 UTC  
> Updated at: 2019-03-30 20:31:35 UTC  
> Closed at: 2019-03-30 20:31:35 UTC  
> Url: https://github.com/boostorg/build/issues/419  

Am I doing something wrong?  
```  
raven@raven-debian:~/boost-root$ ./b2 toolset=gcc helpmeplease  
Performing configuration checks  
  
    - default address-model    : 64-bit  
    - default architecture     : x86  
    - symlinks supported       : yes (cached)  
    - has std atomic           : yes  
/home/debian/boost-root/tools/build/src/build/property.jam:143: in property.evaluate-conditionals-in-context  
*** argument error  
* rule project.target ( project-module : allow-missing ? )  
* called with: (  : allow-missing )  
* missing argument project-module  
/home/debian/boost-root/tools/build/src/build/project.jam:957:see definition of rule 'project.target' being called  
/home/debian/boost-root/tools/build/src/build/configure.jam:502: in object(check-target-builds-worker)@6286.check  
/home/debian/boost-root/tools/build/src/kernel/modules.jam:107: in modules.call-in  
/home/debian/boost-root/tools/build/src/util/indirect.jam:105: in indirect.call  
/home/debian/boost-root/tools/build/src/build/property.jam:144: in property.evaluate-conditionals-in-context  
/home/debian/boost-root/tools/build/src/build/targets.jam:1087: in evaluate-requirements  
/home/debian/boost-root/tools/build/src/build/targets.jam:1121: in common-properties2  
/home/debian/boost-root/tools/build/src/build/targets.jam:1017: in targets.common-properties  
/home/debian/boost-root/tools/build/src/build/targets.jam:1313: in class@basic-target.generate  
/home/debian/boost-root/tools/build/src/build/targets.jam:812: in generate-really  
/home/debian/boost-root/tools/build/src/build/targets.jam:784: in class@main-target.generate  
/home/debian/boost-root/tools/build/src/build/targets.jam:273: in class@project-target.generate  
/home/debian/boost-root/tools/build/src/build-system.jam:797: in load  
/home/debian/boost-root/tools/build/src/kernel/modules.jam:295: in import  
/home/debian/boost-root/tools/build/src/kernel/bootstrap.jam:139: in boost-build  
/home/debian/boost-root/boost-build.jam:17: in module scope  
```  
  
`Jamfile`:  
```jam  
import testing ;  
  
lib std_atomic : : <name>atomic ;  
  
obj has_std_atomic : std_atomic.cpp ;  
explicit has_std_atomic ;  
  
exe has_std_atomic_without_link : has_std_atomic ;  
explicit has_std_atomic_without_link ;  
  
run dummy_test.cpp : :  
  : [ check-target-builds has_std_atomic_without_link "check std atomic w/o linking"  
      :  
      : [ check-target-builds has_std_atomic "has std atomic"  
          : <library>std_atomic : <library>/boost/atomic//boost_atomic ] ]  
  ;  
```  
  
  
`std_atomic.cpp`,`dummy_test.cpp`:  
```cpp  
#include <atomic>  
  
struct X { int a[20]; };  
  
int main()  
{  
    return std::atomic<X>().is_lock_free();  
}  
```  
  
Boost.Build is from the latest develop branch.

---

## Comment 1

> Username: swatanabe  
> Created at: 2019-03-29 23:20:10 UTC  
> Url: https://github.com/boostorg/build/issues/419#issuecomment-478177685  

AMDG  
  
It's a bug.  Specifically, this happens for nested uses  
of check-target-builds.  
  
In Christ,  
Steven Watanabe

---

## Comment 2

> Username: Kojoley  
> Created at: 2019-03-29 23:29:01 UTC  
> Url: https://github.com/boostorg/build/issues/419#issuecomment-478179059  

That's what I thought... Is it a known one? I have no ideas how to do the check other way, maybe you can suggest something?

---

## Comment 3

> Username: swatanabe  
> Created at: 2019-03-29 23:40:42 UTC  
> Url: https://github.com/boostorg/build/issues/419#issuecomment-478180837  

AMDG  
  
On 3/29/19 5:29 PM, Nikita Kniazev wrote:  
> That's what I thought... Is it a known one?  
  
It's new to me.  
  
> I have no ideas how to do the check other way, maybe you can suggest something?  
>   
  
Split it into multiple separate check-target-builds.  
  
You can effectively create an `and` condition with an alias:  
  
[ check-target-builds a : [ check-target-builds b : <foo>bar ] ]  
becomes:  
alias a_and_b : a b ;  
[ check-target-builds a_and_b : <foo>bar ]  
  
In Christ,  
Steven Watanabe

---

## Comment 4

> Username: Kojoley  
> Created at: 2019-03-30 18:01:35 UTC  
> Url: https://github.com/boostorg/build/issues/419#issuecomment-478271999  

I have updated the repro because it had other problem: placing check on project requirement produces recursion, because even if checks are defined before `project` they will have project requirements.  
  
I have managed to workaround the original issue with `alias` this way:  
```jam  
lib std_atomic : : <name>atomic ;  
  
obj has_std_atomic : std_atomic.cpp ;  
explicit has_std_atomic ;  
  
exe has_std_atomic_without_link : has_std_atomic ;  
explicit has_std_atomic_without_link ;  
  
  
alias use_atomic :  
  : [ check-target-builds has_std_atomic_without_link "check std atomic w/o linking"  
      : <build>no ]  
  :  
  : [ check-target-builds has_std_atomic "has std atomic"  
      : <library>std_atomic  
      : <library>/boost/atomic//boost_atomic ]  
  ;  
  
run dummy_test.cpp use_atomic ;  
```
