# #477 - How to relocate the example project? [Open]

> Username: Sowee0  
> Created at: 2025-03-19 14:44:17 UTC  
> Updated at: 2025-03-19 14:44:17 UTC  
> Url: https://github.com/boostorg/python/issues/477  

Hello!   
I've managed to build the example project but needed to relocate it to my project's folder. The documentation doesn't really reflect the current state of the code and I'm a bit lost when updating the jamfile to match this new folder.   
  
Reading the [currently available documentation](https://www.boost.org/doc/libs/1_87_0/libs/python/doc/html/building/no_install_quickstart.html#building.no_install_quickstart.modifying_the_example_project.relocate_the_project) it suggests changing some lines in the jamfile that point to the boost source folder, however as of boost 1.82.0 these lines don't exist in the jamfile anymore.  In the docs we have  
  
> change the first path in boost-build.jam from  
> ../../../../tools/build/src  
> to  
> /home/dave/boost_1_34_0/tools/build/src  
>   
> and change the first path in Jamroot from  
> ../../../..  
> to  
> /home/dave/boost_1_34_0  
  
however the current jamfile looks like this  
```  
import python ;  
  
project tutorial  
  : requirements  
    <location>.  
    ;  
  
python-extension hello_ext : hello.cpp ;  
  
run-test hello : hello_ext hello.py ;  
  
alias test : hello ;  
explicit test ;  
```  
which results in an error when building,  
`ERROR: rule "run-test" unknown in module "Jamfile</home/userfolder/projectfolder>"`  
which I imagine stems from this misconfiguration as it builds fine in the original folder. I am not familiar enough with the jam build system and would appreciate some directions here.  
Thanks in advance!
