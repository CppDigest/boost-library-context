# #744 - PATCH b2 fails when toolset is intel-linux and icpx is not in PATH, but icpc is. [Closed]

> Username: Mark-E-Hamilton  
> Created at: 2021-11-11 18:28:15 UTC  
> Updated at: 2021-11-12 01:52:53 UTC  
> Closed at: 2021-11-12 01:52:52 UTC  
> Url: https://github.com/boostorg/build/issues/744  

We are using intel-2021.3 with the classic compilers (icc, icpc, ifort) in PATH instead of the OneAPI compilers (icpx, et.al.) b2 fails because it doesn't check to see if it found the absolute path to icpx.  
  
system 377% type icpx  
-bash: type: icpx: not found  
system 378% type icpc  
icpc is /usr/projects/hpcsoft/cle7.0/common/x86_64/oneapi/2021.3.0.3219/compiler/2021.3.0/linux/bin/intel64/icpc  
  
system 379% ./b2 --prefix=/lscratch1/mhamilt/boost toolset=intel-linux 2>&1 | tee b2.log  
/lscratch1/mhamilt/boost_1_77_0/tools/build/src/tools/intel-linux.jam:96: in intel-linux.init  
*** argument error  
* rule common.get-absolute-tool-path ( command )  
* called with: (  )  
* missing argument command  
/lscratch1/mhamilt/boost_1_77_0/tools/build/src/tools/common.jam:329:see definition of rule 'common.get-absolute-tool-path' being called  
/lscratch1/mhamilt/boost_1_77_0/tools/build/src/build/toolset.jam:44: in toolset.using  
/lscratch1/mhamilt/boost_1_77_0/tools/build/src/tools/intel.jam:73: in intel.init  
/lscratch1/mhamilt/boost_1_77_0/tools/build/src/build/toolset.jam:44: in toolset.using  
/lscratch1/mhamilt/boost_1_77_0/tools/build/src/build-system.jam:543: in process-explicit-toolset-requests  
/lscratch1/mhamilt/boost_1_77_0/tools/build/src/build-system.jam:610: in load  
/lscratch1/mhamilt/boost_1_77_0/tools/build/src/kernel/modules.jam:295: in import  
/lscratch1/mhamilt/boost_1_77_0/tools/build/src/kernel/bootstrap.jam:139: in boost-build  
/lscratch1/mhamilt/boost_1_77_0/boost-build.jam:17: in module scope  
  
This patch adds if checks for an empty detected_command  
  
  
*** tools/build/src/tools/intel-linux.jam	Thu Nov 11 09:23:32 2021  
--- tools/build/src/tools/intel-linux.jam.orig	Thu Nov 11 09:37:40 2021  
***************  
*** 93,115 ****  
          {  
              local bin_paths = $(.bin(oneAPI)) ;  
              detected_command = [ common.find-tool icpx : $(bin_paths) ] ;  
!             command_abs_path = [ common.get-absolute-tool-path $(detected_command) ] ;  
!             command_lib_path = $(command_abs_path)/../compiler/lib/intel64 ;  
          }  
          if ! $(detected_command)  
          {  
              local bin_paths = $(.bin(12.0)) $(.bin(11.1)) $(.bin(11.0)) ;  
              detected_command = [ common.find-tool icpc : $(bin_paths) ] ;  
!             command_abs_path = [ common.get-absolute-tool-path $(detected_command) ] ;  
!             command_lib_path = $(command_abs_path)/../lib/x86_64 ;  
          }  
          if ! $(detected_command)  
          {  
              local bin_paths = $(.bin(10.1)) $(.bin(9.1)) $(.bin(9.0))  
                  $(.bin(8.1)) $(.bin(8.0)) ;  
              detected_command = [ common.find-tool icpc : $(bin_paths) ] ;  
!             command_abs_path = [ common.get-absolute-tool-path $(detected_command) ] ;  
!             command_lib_path = $(command_abs_path)/../lib ;  
          }  
          if $(detected_command)  
          {  
--- 93,124 ----  
          {  
              local bin_paths = $(.bin(oneAPI)) ;  
              detected_command = [ common.find-tool icpx : $(bin_paths) ] ;  
!             if $(detected_command)  
!             {  
!                 command_abs_path = [ common.get-absolute-tool-path $(detected_command) ] ;  
!                 command_lib_path = $(command_abs_path)/../compiler/lib/intel64 ;  
!             }  
          }  
          if ! $(detected_command)  
          {  
              local bin_paths = $(.bin(12.0)) $(.bin(11.1)) $(.bin(11.0)) ;  
              detected_command = [ common.find-tool icpc : $(bin_paths) ] ;  
!             if $(detected_command)  
!             {  
!                 command_abs_path = [ common.get-absolute-tool-path $(detected_command) ] ;  
!                 command_lib_path = $(command_abs_path)/../lib/x86_64 ;  
!             }  
          }  
          if ! $(detected_command)  
          {  
              local bin_paths = $(.bin(10.1)) $(.bin(9.1)) $(.bin(9.0))  
                  $(.bin(8.1)) $(.bin(8.0)) ;  
              detected_command = [ common.find-tool icpc : $(bin_paths) ] ;  
!             if $(detected_command)  
!             {  
!                 command_abs_path = [ common.get-absolute-tool-path $(detected_command) ] ;  
!                 command_lib_path = $(command_abs_path)/../lib ;  
!             }  
          }  
          if $(detected_command)  
          {

---

## Comment 1

> Username: github-actions[bot]  
> Created at: 2021-11-12 01:52:51 UTC  
> Url: https://github.com/boostorg/build/issues/744#issuecomment-966754408  

Thank you for your contributions. Main development of B2 has moved to https://github.com/bfgroup/b2 Please consider following up at https://github.com/bfgroup/b2/issues
