# #243 - Custom tools configured in site-config cause problems when building Boost [Closed]

> Username: hia3  
> Created at: 2017-09-28 15:04:33 UTC  
> Updated at: 2017-12-19 02:13:11 UTC  
> Closed at: 2017-12-19 02:13:11 UTC  
> Url: https://github.com/boostorg/build/issues/243  

I have `site-config.jam` with some tool configured:  
  
    using some_tool ;  
  
and `some_tool.jam` (near my `Jamroot`) with rule `init` in it. Everything works nicely - BB loads my `some_tool.jam`, calls `init` when I build my project.  
  
Building Boost, however, does not work:  
  
    some_tool.jam: No such file or directory  
    F:/boost_1_65_1/tools/build/src/build\toolset.jam:43: in toolset.using  
    ERROR: rule "some_tool.init" unknown in module "toolset".  
    F:/boost_1_65_1/tools/build/src/build\project.jam:1052: in using  
    C:\Windows\site-config.jam:55: in modules.load  
    F:/boost_1_65_1/tools/build/src\build-system.jam:249: in load-config  
    F:/boost_1_65_1/tools/build/src\build-system.jam:351: in load-configuration-files  
    F:/boost_1_65_1/tools/build/src\build-system.jam:524: in load  
    F:\boost_1_65_1\tools\build\src/kernel\modules.jam:295: in import  
    F:\boost_1_65_1\tools\build\src/kernel/bootstrap.jam:139: in boost-build  
    F:\boost_1_65_1\boost-build.jam:17: in module scope  
  
I think `toolset.using` should ignore tool if there is no module for it.

---

## Comment 1

> Username: swatanabe  
> Created at: 2017-12-17 21:42:05 UTC  
> Url: https://github.com/boostorg/build/issues/243#issuecomment-352287828  

I think this is a bad idea, as it makes it too easy to have silent errors in configuration files.  Your usage is quite fragile, as it depends on the module being found in the current working directory and will fail if the project is built from a different directory or as a dependency of another project.  It would be better to put such project specific configuration in project-config.jam.

---

## Comment 2

> Username: hia3  
> Created at: 2017-12-17 23:05:42 UTC  
> Updated at: 2017-12-19 02:11:43 UTC  
> Url: https://github.com/boostorg/build/issues/243#issuecomment-352293363  

@swatanabe Thanks for your answer!  
  
I should've mentioned that this `using some_tool ;` in `site-config.jam` actually has absolute path in it and looks like this:   
  
    using some_tool : : "C:/Program Files/some_tool/some_version/some_tool.exe" ;  
  
This path is machine(and platform)-specific and I can't put this into `project-config.jam`.  
  
As a workaround I did this:  
  
    modules.load some_tool ;  
    if [ RULENAMES some_tool ] # workaround for https://github.com/boostorg/build/issues/243  
    {  
        using some_tool : : "C:/Program Files/some_tool/some_version/some_tool.exe" ;  
    }  
  
When I build Boost, this condition is `false` and tool is not configured. But it is `true` when I build my project.  
  
Is there a better way to have a tool that is only used by some projects?

---

## Comment 3

> Username: swatanabe  
> Created at: 2017-12-19 01:42:09 UTC  
> Url: https://github.com/boostorg/build/issues/243#issuecomment-352613488  

AMDG  
  
On 12/17/2017 04:05 PM, hia3 wrote:  
> Is there a better way to have a tool that is only used by some projects?  
>   
  
No, there isn't.  
  
  Directly adding this to toolset.using as is, would  
not work in general, as it's too easy for the module  
not to be found when it's actually needed.  We  
could perhaps work around this by saving the options  
and trying again later if/when we successfully load the  
module.  The problem is that the module load order  
might be important.  Also, the only reliable trigger  
is another using for the same module, but not all  
modules handle multiple initialization gracefully.  
  
In Christ,  
Steven Watanabe
