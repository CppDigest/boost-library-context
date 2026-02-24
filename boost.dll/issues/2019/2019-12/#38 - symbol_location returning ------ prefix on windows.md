# #38 - symbol_location returning "\\?\" prefix on windows [Closed]

> Username: johnkit  
> Created at: 2019-12-31 16:14:06 UTC  
> Updated at: 2019-12-31 17:04:03 UTC  
> Closed at: 2019-12-31 17:04:03 UTC  
> Url: https://github.com/boostorg/dll/issues/38  

We have an application that calls boost::dll::symbol_location(). On windows, the returned path looks like `\\?\C:\path-to-the-binary-object-holding-the-symbol`. We don't see anything like that on linux or macOS. My knowledge of Windows is quite limited -- does this "\\?\" indicate an error of some kind?  
  
We also have not seen this prefix in earler boost versions. I think we previously were using 1.66, but moved to 1.71 recently.

---

## Comment 1

> Username: johnkit  
> Created at: 2019-12-31 17:04:03 UTC  
> Url: https://github.com/boostorg/dll/issues/38#issuecomment-569959604  

My apologies. I just learned that Windows "extended file names" start with `\\?\`.
