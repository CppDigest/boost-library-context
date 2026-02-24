# #129 - msvc-based stacktrace should also allow to determine the module image path [Open]

> Username: Dani-Hub  
> Created at: 2022-10-20 16:44:23 UTC  
> Updated at: 2024-09-12 08:18:22 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/129  

The current msvc-based stacktrace only determines the module name - not its complete path - contrary to the full featured unixoid implementation. It turns out that this is rather simple to realize and valuable when attempting to analyze application problems by inspecting the referred to image paths. It means that you first calls [IDebugSymbols::GetModuleByOffset](https://learn.microsoft.com/en-us/windows-hardware/drivers/ddi/dbgeng/nf-dbgeng-idebugsymbols-getmodulebyoffset) followed by [IDebugSymbols::GetModuleNames](https://learn.microsoft.com/en-us/windows-hardware/drivers/ddi/dbgeng/nf-dbgeng-idebugsymbols-getmodulenames) using the `ImageNameBuffer`, `ImageNameBufferSize`, and `ImageNameSize` parameters. I'm using this extended information in my own stacktrace implementation.   
  
There is another way of improving the quality of msvc stacktraces related to the problem described [capturestackbacktrace-randomly-fails-after-initial](https://developercommunity.visualstudio.com/t/capturestackbacktrace-randomly-fails-after-initial/1383213). I'm aware that the current stacktrace implementation has eliminated any explicit call of `CoInitializeEx`, but that does not prevent the user to make such a call. Instead of rewriting `RtlCaptureStackBackTrace` as suggested in this article, a very simple workaround seems to help here. Before calling `RtlCaptureStackBackTrace` with a static buffer (128), just call `RtlCaptureStackBackTrace` with the `FramesToCapture` equal to just `1` ignoring this stub call, which prevents the potential empty stacktrace situation for the second call.  
  
Would there be interest for a corresponding PULL request?

---

## Comment 1

> Username: apolukhin  
> Created at: 2024-09-12 08:18:20 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/129#issuecomment-2345575154  

Yes. Let's take a look at the PR.
