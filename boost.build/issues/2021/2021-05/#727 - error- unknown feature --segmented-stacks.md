# #727 - error: unknown feature "<segmented-stacks>" [Closed]

> Username: verma16Ayush  
> Created at: 2021-05-26 10:33:39 UTC  
> Updated at: 2021-05-26 17:24:32 UTC  
> Closed at: 2021-05-26 17:24:32 UTC  
> Url: https://github.com/boostorg/build/issues/727  

I was trying to build boost from source as per instructions from [this](https://github.com/boostorg/wiki/wiki/Getting-Started%3A-Cheat-Sheet) primer. after running `./bootstrap.sh` when I run `./b2` i get the following output.  
```bash  
/home/avr/Projects/boost/tools/build/src/build/feature.jam:327: in validate-feature from module feature  
error: unknown feature "<segmented-stacks>"  
/home/avr/Projects/boost/tools/build/src/build/feature.jam:361: in expand-subfeatures-aux from module feature  
/home/avr/Projects/boost/tools/build/src/build/feature.jam:422: in feature.expand-subfeatures from module feature  
/home/avr/Projects/boost/tools/build/src/build/property.jam:568: in property.translate from module property  
/home/avr/Projects/boost/tools/build/src/build/property-set.jam:507: in property-set.create-from-user-input from module property-set  
/home/avr/Projects/boost/tools/build/src/build/property-set.jam:562: in property-set.refine-from-user-input from module property-set  
/home/avr/Projects/boost/tools/build/src/build/project.jam:752: in class@project-attributes.set from module object(project-attributes)@2132  
/home/avr/Projects/boost/tools/build/src/build/project.jam:1151: in project from module project-rules  
libs/fiber/build/Jamfile.v2:35: in modules.load from module Jamfile</home/avr/Projects/boost/libs/fiber/build>  
/home/avr/Projects/boost/tools/build/src/build/project.jam:372: in load-jamfile from module project  
/home/avr/Projects/boost/tools/build/src/build/project.jam:64: in load from module project  
/home/avr/Projects/boost/tools/build/src/build/project.jam:89: in load-used-projects from module project  
/home/avr/Projects/boost/tools/build/src/build/project.jam:75: in load from module project  
/home/avr/Projects/boost/tools/build/src/build/project.jam:142: in project.find from module project  
/home/avr/Projects/boost/tools/build/src/build-system.jam:618: in load from module build-system  
/home/avr/Projects/boost/tools/build/src/kernel/modules.jam:295: in import from module modules  
/home/avr/Projects/boost/tools/build/src/kernel/bootstrap.jam:139: in boost-build from module  
/home/avr/Projects/boost/boost-build.jam:17: in module scope from module  
```  
  
I have spent hours searching about this but the only relatable thing I found is this issue [here](https://svn.boost.org/trac10/ticket/11376)  
  
Additional information:  
Compiler: gcc  
version: I am on the master branch of boost

---

## Comment 1

> Username: verma16Ayush  
> Created at: 2021-05-26 17:24:28 UTC  
> Url: https://github.com/boostorg/build/issues/727#issuecomment-848965809  

False alarm. Apparently, it was caused because of network irregularities while cloning the repo. Cloning again a fresh copy fixed it for me
