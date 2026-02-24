# #120 - Additional tools for none-eabi programs [Closed]

> Username: klemens-morgenstern  
> Created at: 2016-02-15 11:12:01 UTC  
> Updated at: 2016-03-03 19:32:25 UTC  
> Closed at: 2016-03-03 19:32:25 UTC  
> Url: https://github.com/boostorg/build/issues/120  

Hi,   
  
I would like to use boost.build for some embedded systems but I need a few more tools to do so. Now the question is if I should try to get it into boost build or just keep it as my own extension.  
  
There are a few things, several compilers would support, like the following:  
- Disassembly  
- Symbol table  
- Map files  
  
Additionally I would like to add those things.  
- HexFiles (i.e. objcopy)  
- Linker Scripts (as arguments when linking, not as target)  
- GCov support (i.e. registering the files as targets when using the coverage flag).  
  
Would that fit into boost/build or should I keep it in my own extension? There is a partial implementation [here](https://github.com/MorgWal/emjam/blob/master/none-eabi.jam).

---

## Comment 1

> Username: swatanabe  
> Created at: 2016-02-15 17:00:22 UTC  
> Url: https://github.com/boostorg/build/issues/120#issuecomment-184301116  

AMDG  
  
On 02/15/2016 04:12 AM, klemens-morgenstern wrote:  
  
> I would like to use boost.build for some embedded systems but I need a few more tools to do so. Now the question is if I should try to get it into boost build or just keep it as my own extension.  
>   
> There are a few things, several compilers would support, like the following:  
> - Disassembly  
> - Symbol table  
> - Map files  
>   
> Additionally I would like to add those things.  
> - HexFiles (i.e. objcopy)  
> - Linker Scripts (as arguments when linking, not as target)  
> - GCov support (i.e. registering the files as targets when using the coverage flag).  
>   
> Would that fit into boost/build or should I keep it in my own extension? There is a partial implementation [here](https://github.com/MorgWal/emjam/blob/master/none-eabi.jam).  
  
  This could be added to Boost.Build as long as  
you make it sufficiently general.  Also,  
remember to write documentation and tests.  
  
In Christ,  
Steven Watanabe

---

## Comment 2

> Username: klemens-morgenstern  
> Created at: 2016-03-03 18:25:18 UTC  
> Url: https://github.com/boostorg/build/issues/120#issuecomment-191895216  

Hi Steven,  
  
I'm currently trying to implement linker-scripts for gcc, by adding the following to `gcc-linking-generator.run` (additional to adding feature and linker-scripts-type) :  
  
```  
        # get all the linker-scripts  
        local .linker-scripts = [ $(property-set).get <linker-script> ] ;  
        for local ld in $(.linker-scripts)  
        {  
            # and now add them as linkflags  
            local tmp = [ $(ld).name ] ;  
            property-set = [ $(property-set).add-raw <linkflags>-T$(tmp) <dependency>$(ld) ] ;  
        }  
```  
  
Problem is: if I do it that way, the main.cpp does also depend on the linker-script in the following example:  
  
```  
exe test : main.cpp : <linker-script>script.ld ;  
```  
  
Is there a non-intrusive way to add this dependency? I could also add the the linker-script as a parameter in the following way, though I'd rather have it as a parameter:  
  
```  
exe test : main.cpp script.ld ;  
```  
  
Thanks,  
  
Klemens

---

## Comment 3

> Username: swatanabe  
> Created at: 2016-03-03 18:52:29 UTC  
> Url: https://github.com/boostorg/build/issues/120#issuecomment-191911299  

AMDG  
  
On 03/03/2016 11:25 AM, klemens-morgenstern wrote:  
  
> I'm currently trying to implement linker-scripts for gcc, by adding the following to `gcc-linking-generator.run` (additional to adding feature and linker-scripts-type) :  
>   
> ```  
>         # get all the linker-scripts  
>         local .linker-scripts = [ $(property-set).get <linker-script> ] ;  
>         for local ld in $(.linker-scripts)  
>         {  
>             # and now add them as linkflags  
>             local tmp = [ $(ld).name ] ;  
>             property-set = [ $(property-set).add-raw <linkflags>-T$(tmp) <dependency>$(ld) ] ;  
>         }  
> ```  
>   
> Problem is: if I do it that way, the main.cpp does also depend on the linker-script in the following example:  
  
  Do you mean that main.o depends on script.ld?  
The dependency should only be added to targets  
created as part of the test target.  
  
You can solve this by overriding  
  
rule generated-targets ( sources + : property-set : project name ? )  
  
and putting your new code in it.  
  
Rationale: run takes the source targets as they  
appear in your Jamfile.  Part of its job is to  
call other generators to convert the sources to  
the correct input types.  generated-targets is  
the function that actually handles linking, after  
we've created the targets for the object files.  
  
> ```  
> exe test : main.cpp : <linker-script>script.ld ;  
> ```  
>   
> Is there a non-intrusive way to add this dependency?  
  
I don't know what you mean by non-intrusive.  
  
> I could also add the the linker-script as a parameter in the following way, though I'd rather have it as a parameter:  
>   
> ```  
> exe test : main.cpp script.ld ;  
> ```  
  
In Christ,  
Steven Watanabe

---

## Comment 4

> Username: klemens-morgenstern  
> Created at: 2016-03-03 19:32:25 UTC  
> Url: https://github.com/boostorg/build/issues/120#issuecomment-191927025  

Dominus tecum,  
  
Thank you! That was exactly what I was looking for.  
  
My non-intrusive I meant just adding code to gcc.jam not toolset.jam or something like that.  
  
I'll annoy you with possible further questions in the [PR](https://github.com/boostorg/build/pull/124).  
  
Pax,  
  
Klemens
