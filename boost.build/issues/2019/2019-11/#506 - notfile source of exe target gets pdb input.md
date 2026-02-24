# #506 - notfile source of exe target gets pdb input [Open]

> Username: Kojoley  
> Created at: 2019-11-13 17:55:46 UTC  
> Updated at: 2021-05-29 17:22:30 UTC  
> Url: https://github.com/boostorg/build/issues/506  

A while ago a used `notfile` to simplify toolset information gathering in boost/config (https://github.com/boostorg/config/pull/274). A simplified version of it:   
  
```bjam  
exe config_info_printer : config_info.cpp ;  
explicit config_info_printer ;  
  
actions print-run  
{  
      $(>)  
}  
  
notfile print_config_info : @print-run : config_info_printer ;  
explicit print_config_info ;  
```  
  
Everything was fine, but suddenly with a particular cxxstd list and only on Appveyor it tried to execute `config_info_printer.pdb`. When I were writing the thing I run it with `variant=release` and `$(>)` contained only exe path, so I never notices before that `$(>)` could contain two elements (it is with `variant=debug`), however, by the pure luck(?) they were always in `config_info_printer.exe config_info_printer.pdb` order, but in that particular case items swapped.  
  
1. Is it expected that `$(>)` contains not only exe, but also pdb path?  
2. Is the order undefined?  
3. Is there a way to force the order or to receive only exe path?  
4. Or, probably, you can suggest other way to run exe target with printing to console its output with b2?  
  
The `notfile` documentation is scarse, I did not find answers for the questions above in it.

---

## Comment 1

> Username: Kojoley  
> Created at: 2020-06-17 00:18:44 UTC  
> Url: https://github.com/boostorg/build/issues/506#issuecomment-645075468  

Well, it comes from https://github.com/boostorg/build/blob/269272327ee37f5ef705c9bbefc757903b1d70f6/src/tools/msvc.jam#L1725-L1733  
  
I still do not know how to workaround that or what is an alternative solution

---

## Comment 2

> Username: stale[bot]  
> Created at: 2021-05-29 17:22:00 UTC  
> Url: https://github.com/boostorg/build/issues/506#issuecomment-850868255  

Thank you for your contributions. Main development of B2 has moved to https://github.com/bfgroup/b2  
This issue has been automatically marked as "transition" to indicate the potential for needing transition to the new B2 development project.
