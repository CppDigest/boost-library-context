# #632 - [Windows] Staging file-target with action produces invalid path [Open]

> Username: hia3  
> Created at: 2020-06-27 10:32:12 UTC  
> Updated at: 2021-05-29 17:22:28 UTC  
> Url: https://github.com/boostorg/build/issues/632  

`Jamroot`:  
  
    import common ;  
    import stage ;  
  
    project test ;  
  
    make my : Jamroot  : @common.copy ;  
  
    local global-build-dir = [ modules.peek project : .global-build-dir ] ;  
    path-constant INSTALL_TO : [ modules.peek : INSTALL_TO ] ;  
  
    stage.install stage : my : <install-source-root>$(global-build-dir) <location>$(INSTALL_TO) ;  
  
invocation:   
  
    %the_boost%\b2.exe -a -sBOOST_ROOT=%the_boost% -sINSTALL_TO=C:\i --build-dir=C:\o  
  
output:  
  
        if not exist "C:\i\..\test\C:\o\\" mkdir "C:\i\..\test\C:\o"  
    ...failed common.mkdir C:\i\..\test\C:\o..  
    ...skipped C:\i\..\test\C:\o\test for lack of C:\i\..\test\C:\o...  
    ...skipped <pC:\i>..\test\C:\o\test\my for lack of C:\i\..\test\C:\o\test...  
    ...failed updating 1 target...  
    ...skipped 2 targets..  
    ...updated 1 target...  
  
[file-target.path](https://github.com/boostorg/build/blob/512ea1f0730b59ba6232709bf9da20fde5dce3ad/src/build/virtual-target.jam#L685) returns a **native** path if there is an action. In stage.jam [copy-file](https://github.com/boostorg/build/blob/512ea1f0730b59ba6232709bf9da20fde5dce3ad/src/tools/stage.jam#L520) generates wrong path because [path.root](https://github.com/boostorg/build/blob/512ea1f0730b59ba6232709bf9da20fde5dce3ad/src/util/path.jam#L178) thinks that that absolute native `path` is **relative**.

---

## Comment 1

> Username: stale[bot]  
> Created at: 2021-05-29 17:21:58 UTC  
> Url: https://github.com/boostorg/build/issues/632#issuecomment-850868248  

Thank you for your contributions. Main development of B2 has moved to https://github.com/bfgroup/b2  
This issue has been automatically marked as "transition" to indicate the potential for needing transition to the new B2 development project.
