# #557 - Command to find the BOOST_INC, BOOST_LIB_IO and BOOST_LIB_PO [Closed]

> Username: Shicheng-Guo  
> Created at: 2021-09-09 00:41:07 UTC  
> Updated at: 2021-09-09 00:45:58 UTC  
> Closed at: 2021-09-09 00:45:58 UTC  
> Url: https://github.com/boostorg/boost/issues/557  

Which command should be used so that I can figure out the path I should use for the following setting?   
```  
BOOST_INC=/usr/include  
BOOST_LIB_IO=/usr/lib/x86_64-linux-gnu/libboost_iostreams.a  
BOOST_LIB_PO=/usr/lib/x86_64-linux-gnu/libboost_program_options.a  
```

---

## Comment 1

> Username: Shicheng-Guo  
> Created at: 2021-09-09 00:45:53 UTC  
> Url: https://github.com/boostorg/boost/issues/557#issuecomment-915671518  

Thank you. I think I find the command.   
  
```  
[mkandes@login01 ~]$ module list  
Currently Loaded Modules:  
  1) shared  2) slurm/expanse/20.02.3  3) cpu/0.15.4  4) DefaultModules  
  
[mkandes@login01 ~]$ module load gcc/10.2.0   
[mkandes@login01 ~]$ module load boost/1.74.0  
[mkandes@login01 ~]$ printenv | grep BOOST  
BOOST_ROOT=/cm/shared/apps/spack/cpu/opt/spack/linux-centos8-zen2/gcc-10.2.0/boost-1.74.0-hurj3p4dpe26k2rjwsxa6pubxopjioo7  
BOOSTHOME=/cm/shared/apps/spack/cpu/opt/spack/linux-centos8-zen2/gcc-10.2.0/boost-1.74.0-hurj3p4dpe26k2rjwsxa6pubxopjioo7  
```
