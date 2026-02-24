# #447 Support values for instruction-set feature for Xilinx ZYNQ. [Closed]

> Username: tee3  
> Created at: 2019-05-29 13:56:10 UTC  
> Updated at: 2021-10-02 20:59:32 UTC  
> Closed at: 2021-05-31 17:43:32 UTC  
> Url: https://github.com/boostorg/build/pull/447  

Note that this description is a lot of words describing the change as well  as asking some larger questions about how we might want to support more processor tuning.  I welcome all feedback in specific and in general.  
  
This pull request adds support for several very specific ARM instruction sets or processors (including extensions) to the `gcc` `toolset`.  These processors are used in various Xilinx ZYNQ FPGA systems.  The naming format used is one described in the GCC 8.3.0 manual (see Section 3.18.4) for lack of a more authoritative source.  GCC supports adding extensions such as FPU using a `+` syntax that seems pretty flexible.  It's basically `processor-name+extension-0+extension-1...`.  While the naming format is flexible, this pull request only adds four `instruction-set` configurations, which covers all existing Xilinx ZYNQ.  
  
Some notes:  
  
* only `gcc` is supported  
* only four processors (with extensions) are supported: `cortex-r5`, `cortex-a53`, `cortex-a9+vfpv3`, `cortex-r5+vfpv3-d16`  
* an unsupported `instruction-set` just gets no additional options for `gcc`  
* a `toolset` that does not support an `instruction-set`   
  
The motivation for this change is to support these processors in a module to generate a BSP (and linker command file) using the Xilinx SDK.  This module is still in development.  If a processor is not supported in the toolset (e.g. `gcc`), then properties which normally propagate (e.g. `instruction-set`) do propagate so that the path reflects the `instruction-set`, the compiler options do not.  Note that the Xilinx SDK builds libraries and so defines what instruction set and extensions are required by programs that use those libraries.  
  
If this change is too narrow since it doesn't cover all (or most) ARM instruction sets or processors, both with extensions, what else can I do?  I'd really like to be able to support all ARM architecture (plus extensions) or instruction-set (plus extensions), but that is a much bigger change.  The combinations of options is large and they should probably be generated.  Adding general support for ARM might require changing how `cpu-flags` are defined and / or used in `gcc`.  Any `toolset` that supports the ARM architecture should also have support for this as well, but I have not looked at other toolsets that support ARM and I don't necessarily have access to all those compilers.  
  
Note that there are many more ARM processors and it would be really nice to support directly.  Unfortunately, versions of GCC prior to 8 do not support that format directly in the `-mcpu` option.  In order to support all these combinations, we would have to be able to use the `-mcpu` and `-mfpu` along with any other required options.  I don't think this would be a problem since GCC tends to be backwards compatible, but there is no guarantee every combination would be valid on any compiler or compiler version.

---

## Comment 1

> Username: tee3  
> Created_at: 2019-05-29 17:52:42 UTC  
> Url: https://github.com/boostorg/build/pull/447#issuecomment-497042128  

Sorry about the rebase, I had committed using the wrong author.

---

## Comment 2

> Username: grafikrobot  
> Created_at: 2019-06-03 19:58:51 UTC  
> Url: https://github.com/boostorg/build/pull/447#issuecomment-498403603  

The only/best alternative for supporting the variety of CPU types is to create additional features for the extensions.

---

## Comment 3

> Username: tee3  
> Created_at: 2019-06-04 11:03:37 UTC  
> Url: https://github.com/boostorg/build/pull/447#issuecomment-498625210  

Thanks @grafikrobot.  I'm taking your comment as a request for me to propose a way to do this by adding features for each extension and trying to provide a comprehensive solution for ARM.  I'll create a separate pull request to work this out in the next several days unless I hear otherwise.

---

## Comment 4

> Username: tee3  
> Created_at: 2019-07-20 01:06:48 UTC  
> Url: https://github.com/boostorg/build/pull/447#issuecomment-513421829  

FYI I got busy with some other things in the meantime, but this is still on my list of things to do.

---

## Comment 5

> Username: stale[bot]  
> Created_at: 2021-05-29 16:21:47 UTC  
> Url: https://github.com/boostorg/build/pull/447#issuecomment-850859489  

Thank you for your contributions. Main development of B2 has moved to https://github.com/bfgroup/b2  
This issue has been automatically marked as "transition" to indicate the potential for needing transition to the new B2 development project.

---

## Comment 6

> Username: tee3  
> Created_at: 2021-05-31 18:17:08 UTC  
> Url: https://github.com/boostorg/build/pull/447#issuecomment-851624634  

I did not mean to close this, it happened due to me migrating my branches to bfgroup/b2.  I will try to reinstate it and create a new pull request.

---

## Comment 7

> Username: tee3  
> Created_at: 2021-05-31 18:22:33 UTC  
> Url: https://github.com/boostorg/build/pull/447#issuecomment-851626480  

See bfgroup/b2#34.

---

## Comment 8

> Username: github-actions[bot]  
> Created_at: 2021-10-02 20:59:31 UTC  
> Url: https://github.com/boostorg/build/pull/447#issuecomment-932819663  

Thank you for your contributions. Main development of B2 has moved to https://github.com/bfgroup/b2 Please consider following up at https://github.com/bfgroup/b2/pulls

---
