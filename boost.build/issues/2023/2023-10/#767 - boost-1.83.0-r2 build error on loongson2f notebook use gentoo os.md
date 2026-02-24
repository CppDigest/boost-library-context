# #767 - boost-1.83.0-r2 build error on loongson2f notebook use gentoo os [Closed]

> Username: emtone  
> Created at: 2023-10-28 03:37:37 UTC  
> Updated at: 2023-10-28 03:54:08 UTC  
> Closed at: 2023-10-28 03:54:08 UTC  
> Url: https://github.com/boostorg/build/issues/767  

error info:  
b2 --user-config=/var/tmp/portage/dev-libs/boost-1.83.0-r2/work/boost_1_83_0-abi_mips_n32.n32/user-config.jam python=3.10 python=3.11 python=3.12 --prefix=/usr gentoorelease -j1 -q -d+2 pch=off --without-mpi --boost-build=/usr/share/b2/src --layout=system threading=multi link=shared -sNO_BZIP2=0 -sNO_LZMA=1 -sNO_ZLIB=0 -sNO_ZSTD=1  
/usr/share/b2/src/build/project.jam:820: in class@project-attributes.set from module object(project-attributes)@7768  
error: Invalid project attribute 'common-requirements' specified for project at 'libs/url/build'  
/usr/share/b2/src/build/project.jam:1151: in project from module project-rules  
libs/url/build/Jamfile:22: in modules.load from module Jamfile</var/tmp/portage/dev-libs/boost-1.83.0-r2/work/boost_1_83_0-abi_mips_n32.n32/libs/url/build>  
/usr/share/b2/src/build/project.jam:372: in load-jamfile from module project  
/usr/share/b2/src/build/project.jam:64: in load from module project  
/usr/share/b2/src/build/project.jam:89: in load-used-projects from module project  
/usr/share/b2/src/build/project.jam:75: in load from module project  
/usr/share/b2/src/build/project.jam:142: in project.find from module project  
/usr/share/b2/src/build-system.jam:618: in load from module build-system  
/usr/share/b2/src/kernel/modules.jam:294: in import from module modules  
/usr/share/b2/src/kernel/bootstrap.jam:135: in module scope from module  
  
 ^[[31;01m*^[[0m ERROR: dev-libs/boost-1.83.0-r2::gentoo failed (compile phase):  
 ^[[31;01m*^[[0m   (no error message)  
 ^[[31;01m*^[[0m  
 ^[[31;01m*^[[0m Call stack:  
 ^[[31;01m*^[[0m     ebuild.sh, line  136:  Called src_compile  
 ^[[31;01m*^[[0m   environment, line 3322:  Called multilib-minimal_src_compile  
 ^[[31;01m*^[[0m   environment, line 2079:  Called multilib_foreach_abi 'multilib-minimal_abi_src_compile'  
 ^[[31;01m*^[[0m   environment, line 2346:  Called multibuild_foreach_variant '_multilib_multibuild_wrapper' 'multilib-minimal_abi_src_compile'  
 ^[[31;01m*^[[0m   environment, line 2051:  Called _multibuild_run '_multilib_multibuild_wrapper' 'multilib-minimal_abi_src_compile'  
 ^[[31;01m*^[[0m   environment, line 2049:  Called _multilib_multibuild_wrapper 'multilib-minimal_abi_src_compile'  
 ^[[31;01m*^[[0m   environment, line  501:  Called multilib-minimal_abi_src_compile  
 ^[[31;01m*^[[0m   environment, line 2073:  Called multilib_src_compile  
 ^[[31;01m*^[[0m   environment, line 2566:  Called die  
 ^[[31;01m*^[[0m The specific snippet of code:  
 ^[[31;01m*^[[0m       ejam --prefix="${EPREFIX}"/usr "${OPTIONS[@]}" || die;  
 ^[[31;01m*^[[0m  
 ^[[31;01m*^[[0m If you need support, post the output of `emerge --info '=dev-libs/boost-1.83.0-r2::gentoo'`,  
 ^[[31;01m*^[[0m the complete build log and the output of `emerge -pqv '=dev-libs/boost-1.83.0-r2::gentoo'`.  
 ^[[31;01m*^[[0m The complete build log is located at '/var/tmp/portage/dev-libs/boost-1.83.0-r2/temp/build.log'.  
 ^[[31;01m*^[[0m The ebuild environment file is located at '/var/tmp/portage/dev-libs/boost-1.83.0-r2/temp/environment'.  
 ^[[31;01m*^[[0m Working directory: '/var/tmp/portage/dev-libs/boost-1.83.0-r2/work/boost_1_83_0-abi_mips_n32.n32'  
 ^[[31;01m*^[[0m S: '/var/tmp/portage/dev-libs/boost-1.83.0-r2/work/boost_1_83_0'

---

## Comment 1

> Username: github-actions[bot]  
> Created at: 2023-10-28 03:54:06 UTC  
> Url: https://github.com/boostorg/build/issues/767#issuecomment-1783687671  

Thank you for your contributions. Main development of B2 has moved to https://github.com/bfgroup/b2 Please consider following up at https://github.com/bfgroup/b2/issues
