# #500 - Suggestion: add features that control installation prefixes [Open]

> Username: grisumbras  
> Created at: 2019-10-29 06:40:49 UTC  
> Updated at: 2021-05-29 18:22:40 UTC  
> Url: https://github.com/boostorg/build/issues/500  

Currently, in order to allow installation prefix to be substitutable one uses options. For example:  
  
```jam  
import package ;  
package.install install myproject : : myapp : mylib ;  
```  
on *nix installs `{bindir}/myapp` and `{libdir}/libmylib.so`.  
  
But when one builds several configurations simultaneously this will lead to a conflict.  
  
I propose several additions to b2:  
* A set of features that control installation prefix: `<install-prefix>`, `<install-bindir>` etc.  
* Add support to these features to  `install` rule. The prefix should be enclosed in parentheses and put at the beginning of either `<location>` value or the target name.  
```jam  
alias install  
  : [ install (bindir) : myapp ]  
  : [ install _ : mylib : <location>(libdir) ]  
  ;  
```  
  
As a result, given this `project-config.jam`  
```  
using gcc ;  
using gcc : musl : ... ;  
  
project  
  : requirements  
    <toolset>gcc:<install-prefix>build/stage/gnu  
    <toolset>gcc:<install-libdir>lib/x86_64-linux-gnu  
  
    <toolset>gcc-musl:<install-prefix>build/stage/musl  
    <toolset>gcc-musl:<install-libdir>lib/x86_64-linux-musl  
  ;  
```  
invoking `b2 install toolset=gcc,gcc-musl` installs  
*  `build/stage/gnu/bin/myapp`  
*  `build/stage/gnu/lib/x86_64-linux-gnu/libmylib.so`  
*  `build/stage/gnu/musl/myapp`  
*  `build/stage/gnu/musl/x86_64-linux-musl/libmylib.so`  
  
In addition, I suggest adding `<stage-prefix>` feature that replaces `<install-prefix>` for actual installation paths when provided. This is needed when targets have to know their installed location (or some related location), but actually putting them there is either impossible or not desirable. In order to compose the full installation path of a target a rule will be added to `stage` module. Example:  
  
```jam  
exe foo : foo.cpp : <conditional>@get-confdir ;  
install install foo : <location>(bindir) ;  
  
rule get-confdir ( properties * ) {  
  local confdir = [ stage.get-dir : sysconfdir ] ;  
  return <define>CONFDIR=$(confdir) ;  
}  
```  
Invoking `b2 install install-prefix=/usr install-sysconfdir=/etc stage-prefix=build/stage` installs `build/stage/bin/foo`, but the installed executable will search for its configuration files in `/etc`.

---

## Comment 1

> Username: stale[bot]  
> Created at: 2021-05-29 18:22:10 UTC  
> Url: https://github.com/boostorg/build/issues/500#issuecomment-850877352  

Thank you for your contributions. Main development of B2 has moved to https://github.com/bfgroup/b2  
This issue has been automatically marked as "transition" to indicate the potential for needing transition to the new B2 development project.
