# #24 - Maybe implement a config based on dbghelp or msdia [Closed]

> Username: segevfiner  
> Created at: 2017-07-13 19:23:17 UTC  
> Updated at: 2024-07-16 05:52:20 UTC  
> Closed at: 2024-07-16 05:52:20 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/24  

It's a bit more lightweight than dbgeng/windbg (I think it's used by them) and it wouldn't have the issues with COM (It's what Rust uses for backtraces in the MSVC version).  
  
Note that I think you must also have symsrv.dll (Distributed with windbg) for symbol loading with it to work. The dbghelp.dll installed with Windows is often unable to get symbols probably because it doesn't have that dll available or maybe it just doesn't support it at all.  
  
MSDN: https://msdn.microsoft.com/en-us/library/windows/desktop/ms679309(v=vs.85).aspx  
  
P.S. I guess that you must have dbgeng.dll and all it's dependencies for the windbg config to work (i.e. You must vendor them, add them to PATH, etc...), might want to document that.  
  
**EDIT:** I saw in some pr that you said you don't use dbghelp because it's single threaded. But I think windbg uses it under the hood... They even have warnings against using dbghelp in windbg extensions... It's possible that you might be affected by it's limitations despite using the dbgeng/windbg API.  
  
There is also the option of msdia (https://msdn.microsoft.com/en-us/library/x93ctkx8.aspx) which you get with MSVC, but I'm not sure about licensing and how easy it is to use it to parse backtraces. It's another COM api but it can be used directly though an undocumented export in it's dll (NoRegCoCreate).

---

## Comment 1

> Username: apolukhin  
> Created at: 2017-07-17 16:52:31 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/24#issuecomment-315812690  

I'd love to use something more lightweight for decoding function names from addresses (gathering addresses is already as fast as possible). But that lightweight stuff **must** be thread safe.  
  
Some functions of `dbghelp` are known not to work in multi-threaded envs. Other parts of `dbghelp` must be documented as thread safe first! Because otherwise vendor may silently change the safety in other version of `dbghelp` and that issue may be unnoticed for decades.  
  
So, if you wish this issue to progress, then please fill a feature request for MS to clarify the safety of `dbghelp` functions. After that, please provide a link to the issue here, so that I can watch the progress.

---

## Comment 2

> Username: segevfiner  
> Created at: 2017-07-17 17:34:24 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/24#issuecomment-315825082  

@apolukhin Have you read the "edit", I think you missed it: 😉  
> **EDIT:** I saw in some pr that you said you don't use dbghelp because it's single threaded. But I think windbg uses it under the hood... They even have warnings against using dbghelp in windbg extensions... It's possible that you might be affected by it's limitations despite using the dbgeng/windbg API.  
>  
>There is also the option of msdia (https://msdn.microsoft.com/en-us/library/x93ctkx8.aspx) which you get with MSVC, but I'm not sure about licensing and how easy it is to use it to parse backtraces. It's another COM api but it can be used directly though an undocumented export in it's dll (NoRegCoCreate).

---

## Comment 3

> Username: apolukhin  
> Created at: 2017-07-17 17:44:29 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/24#issuecomment-315827915  

> though an undocumented export in it's dll (NoRegCoCreate).  
  
Same issues as above. Without docs there's no guarantee that it works as expected and is thread safe.

---

## Comment 4

> Username: segevfiner  
> Created at: 2017-07-19 18:08:03 UTC  
> Updated at: 2017-07-19 18:12:03 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/24#issuecomment-316470137  

I would tend to assume that dia is thread safe for separate objects like you can normally assume for most sane API's (i.e. not dbghelp, which documents this).  
  
`NoRegCoCreate` is indeed undocumented in MSDN as far as I can tell (dia doesn't have a very detailed documentation anyhow), but you do have an header for it: "diacreate.h", as part of the DIA SDK you get with Visual Studio. Using it can save messing with registering msdia globally or *shudders* reg free COM (This requires DIA to have a manifest which I don't think it has).  
  
The note about not using dbghelp with windbg that I remembered is here: https://docs.microsoft.com/en-us/windows-hardware/drivers/debugger/writing-dbgeng-extension-code. It's a bit vague and only mentions debugger extensions. But, correct me if I'm wrong, I really think windbg uses dbghelp under the hood.

---

## Comment 5

> Username: apolukhin  
> Created at: 2018-10-09 18:51:17 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/24#issuecomment-428307901  

The decision remains the same: if docs say that it is thread unsafe, then it won't be used in Boost.Stacktrace. Otherwise it will do more harm than profit.

---

## Comment 6

> Username: segevfiner  
> Created at: 2018-10-09 19:13:22 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/24#issuecomment-428315333  

@apolukhin   
  
`dbghelp` isn't safe. That's clearly stated in the docs. msdia should be safe if you don't share the same object between threads, like any other API. Microsoft doesn't state that explicity for any API unless there are additional thread safety or unsafety stipulations about it, to the best of my knowledge.  
  
I really think you might want than to reconsider msdia. It is redistributable according to https://docs.microsoft.com/he-il/visualstudio/productinfo/2015-redistribution-vs, unlike dbgeng. I really think it is thread safe, as long as you don't share the same object across threads, as is the general assumption with WinAPI. It doesn't use dbghelp internally, which is practically the only dll I ever had thread safety issues with. And to top it off, **I think dbgeng uses dbghelp under the hood, which is thread unsafe!** there are even explicit warnings to avoid mixing code that uses dbghelp and dbgeng due to that and due to the fact that dbgeng affects global dbghelp state.  
  
I really think msdia is actually the safe choice, while it is possible that the current dbgeng solution might be hiding thread unsafety under the hood from dbghelp!

---

## Comment 7

> Username: apolukhin  
> Created at: 2018-10-09 19:45:21 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/24#issuecomment-428325550  

Sounds reasonable.  
  
Not shure that I'll have time to implement that any time soon. So PR are wellcomed

---

## Comment 8

> Username: AlexGuteniev  
> Created at: 2022-02-09 14:35:29 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/24#issuecomment-1033826815  

> there are even explicit warnings to avoid mixing code that uses dbghelp and dbgeng due to that and due to the fact that dbgeng affects global dbghelp state.  
  
Where?  
  
I do see that dbgeng calls dbghelp, but I don't see this thread unsafety documented.

---

## Comment 9

> Username: segevfiner  
> Created at: 2022-02-09 14:45:51 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/24#issuecomment-1033837245  

Briefly mentioned here https://docs.microsoft.com/en-us/windows-hardware/drivers/debugger/writing-dbgeng-extension-code

---

## Comment 10

> Username: segevfiner  
> Created at: 2022-02-09 16:45:47 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/24#issuecomment-1033969111  

Yeah. As you undoubtedly figured out, the documentation is not all that clear. 😛

---

## Comment 11

> Username: AlexGuteniev  
> Created at: 2022-02-09 16:59:05 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/24#issuecomment-1033982934  

I helped where I could:  
 * https://github.com/MicrosoftDocs/windows-driver-docs-ddi/pull/1255  
 * https://github.com/MicrosoftDocs/windows-driver-docs-ddi/pull/1256  
  
I'm now trying to work on `std::stacktrace` PR for MSVC STL. Did dbghelp version, but thread unsafety is not an option. Was suggested to try dbgeng, implemented via it, and discovered it is not thread safe.  
  
Now waiting for maintainers decision what to do.  
DIA with its need to `CoInitializeEx` is IMO not an option for a `std::` facility due to impact on the user's work with COM. If there's a way exists to use DIA without `CoInitialize[Ex]`, but it is undocumented, it also is not likely to be approved.  
Parsing .pdb manually is tedious.  
One thing I can think of is to spawn a process to do symbolling, but also does not look like a good idea.  
  
If this saga will be successful, I think I'll also be able to contribute the ultimate solution here.

---

## Comment 12

> Username: segevfiner  
> Created at: 2022-02-09 17:03:08 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/24#issuecomment-1033987267  

There is `NoRegCoCreate` exported from the DIA dll and I think the sample dia2dump that comes with MSVC does use it.

---

## Comment 13

> Username: AlexGuteniev  
> Created at: 2022-02-09 17:11:57 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/24#issuecomment-1033997079  

No, the sample uses the usual `CoCreateInstance`, but at least `NoRegCoCreate` is exposed via headers with a comment

---

## Comment 14

> Username: segevfiner  
> Created at: 2022-02-22 19:50:51 UTC  
> Updated at: 2022-02-22 21:08:26 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/24#issuecomment-1048155162  

I used `NoRegCoCreate` for this: https://github.com/segevfiner/pydia2  
  
Some notes:  
* The actual `NoRegCoCreate` function is in `diaguids.lib` not in the DLL.  
* You might also need `symsrv.dll` from the Debugging Tools for Windows so that `loadDataForExe` will look in symbols servers. (Haven't tested yet).  
* I'm not sure if you need or not to use `CoInitializeEx` while creating the objects that way. (`comtypes` calls it implicitly for the main thread, and I haven't tried it in a thread without COM initialization yet).

---

## Comment 15

> Username: apolukhin  
> Created at: 2024-07-16 05:52:20 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/24#issuecomment-2230079045  

The Boost.Stacktrace now does not use CoInitializeEx or soke other haevy COM initializations. Looks like the issue is fixed
