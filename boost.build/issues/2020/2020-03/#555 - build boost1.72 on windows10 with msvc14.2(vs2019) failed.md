# #555 - build boost1.72 on windows10 with msvc14.2(vs2019) failed [Open]

> Username: liqi198786  
> Created at: 2020-03-25 07:06:34 UTC  
> Updated at: 2021-05-29 18:22:23 UTC  
> Url: https://github.com/boostorg/build/issues/555  

it will always occur the error as below:  
"failed to write output file 'build\boost\architecture\msvc-14.2\debug\threading-multi\32.obj.rsp'!"  
i searched the error on the net, some people suggest adding some options such as "cxxflags=/FS" "pch=off", but these didn't work for my case.  
and here is all my cmd:  
"b2 -j32^  
    headers^  
    --layout=versioned^  
    --build-dir=.\build^  
    --with-system^  
    --with-date_time^  
    --with-filesystem^  
    --with-python^  
    --project-config=project-config.jam^  
    -d 1^  
    architecture=x86^  
    address-model=64^  
    toolset=msvc-14.2^  
    variant=release^  
    link=static^  
    threading=multi^  
    runtime-link=shared^  
    --prefix="C:\Users\szx3gg\Downloads\boost_1_72_0"^  
    --libdir="C:\Users\szx3gg\Downloads\boost_1_72_0\lib"^  
    --includedir="C:\Users\szx3gg\Downloads\boost_1_72_0"^  
    install  
    "  
i also have tried to test remove some options in the cmdline or just keep them like "threading=multi^" , but still failed with the error.

---

## Comment 1

> Username: grafikrobot  
> Created at: 2020-03-25 12:27:21 UTC  
> Url: https://github.com/boostorg/build/issues/555#issuecomment-603811443  

Try adding either `--abbreviated-paths` or `--hash` options (https://boostorg.github.io/build/manual/master/index.html#bbv2.reference.buildprocess.targetpath). If that works it means your setup is creating long paths. Depending on which version of Windows you have it might be possible to enable long path support in the OS.

---

## Comment 2

> Username: liqi198786  
> Created at: 2020-03-25 14:11:35 UTC  
> Url: https://github.com/boostorg/build/issues/555#issuecomment-603861481  

> Try adding either `--abbreviated-paths` or `--hash` options (https://boostorg.github.io/build/manual/master/index.html#bbv2.reference.buildprocess.targetpath). If that works it means your setup is creating long paths. Depending on which version of Windows you have it might be possible to enable long path support in the OS.  
  
hi @grafikrobot , thanks for your answer, but it seems that doesn't work for me either. i now really wonder what .rsp file is, and why the .obj file needs to be transformed or something like to the .rsp file format.

---

## Comment 3

> Username: grafikrobot  
> Created at: 2020-03-25 14:17:47 UTC  
> Url: https://github.com/boostorg/build/issues/555#issuecomment-603864792  

The RSP files are "response" files. They contain the arguments tot he compiler for building that particular source file. They get used to work around compiler command invocation argument length limits.  
  
Not sure what else, other than the path length, could be going wrong here :-(

---

## Comment 4

> Username: liqi198786  
> Created at: 2020-03-25 14:33:23 UTC  
> Url: https://github.com/boostorg/build/issues/555#issuecomment-603873375  

hi @grafikrobot , i also tried the simple example in the b2 folder to build a project and built it with b2.exe. the project is as below:  
![image](https://user-images.githubusercontent.com/22949100/77547351-0d21ac00-6ee8-11ea-85a6-7c4753b6734c.png)  
  
however it still fails with the same error.  
  
![image](https://user-images.githubusercontent.com/22949100/77547732-8ae5b780-6ee8-11ea-8a99-2b86c94ce93f.png)  
  
i don't think the path is too long....

---

## Comment 5

> Username: stale[bot]  
> Created at: 2021-05-29 18:21:53 UTC  
> Url: https://github.com/boostorg/build/issues/555#issuecomment-850877281  

Thank you for your contributions. Main development of B2 has moved to https://github.com/bfgroup/b2  
This issue has been automatically marked as "transition" to indicate the potential for needing transition to the new B2 development project.
