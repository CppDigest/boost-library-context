# #586 - clang-win with 'runtime-debugging=on' generates '-d-' instead of '-gd' [Open]

> Username: greenkalx  
> Created at: 2020-04-30 08:33:03 UTC  
> Updated at: 2021-05-29 17:22:41 UTC  
> Url: https://github.com/boostorg/build/issues/586  

From the documentation, I would expect building with `runtime-debugging=on` would create libs with names:  
`boost_chrono-clangw10-mt-gd-x64-1_73.lib`  
  
But they are built as:  
`boost_chrono-clangw10-mt-d-x64-1_73.lib`  
  
Doc:  
https://www.boost.org/doc/libs/1_73_0/more/getting_started/windows.html  
  
As an alternative, is there a way to direct Visual Studio to link with '-d-' libs?  
  
Building with:  
`b2 toolset=clang-win variant=debug,release address-model=64 threading=multi link=shared runtime-link=shared runtime-debugging=on --with-atomic --stagedir=c:\boostlibs`  
  
I saw someone else with the same problem:  
https://stackoverflow.com/questions/59375923/how-to-build-debug-boost-static-libraries-with-clang-win-toolset  
  
I have no trouble with the Release libs, just Debug.  
  
I also have no luck building 32 bit libs with `address-model=32`, but I see those builds are not running on CI (appveyor), so they are not supported?  
https://github.com/boostorg/boost-ci/blob/af9e44d4c5ddc26cd698fcc830954fe20b448673/.appveyor.yml

---

## Comment 1

> Username: Kojoley  
> Created at: 2020-05-01 19:29:30 UTC  
> Url: https://github.com/boostorg/build/issues/586#issuecomment-622529434  

https://github.com/boostorg/build/blob/7d9866256d1ce46abaf8355bb618cdebab9cfe62/src/tools/common.jam#L1024-L1034  
  
The hardcoded toolsets list need to be be rewritten into `<stdlib>msvc`

---

## Comment 2

> Username: greenkalx  
> Created at: 2020-05-01 22:28:09 UTC  
> Url: https://github.com/boostorg/build/issues/586#issuecomment-622593080  

I will go with BOOST_ALL_NO_LIB for manual linking for now.

---

## Comment 3

> Username: pdimov  
> Created at: 2020-05-13 18:56:39 UTC  
> Url: https://github.com/boostorg/build/issues/586#issuecomment-628181935  

Thanks, made a PR (#605) to add clang-win to the list.

---

## Comment 4

> Username: greenkalx  
> Created at: 2020-05-15 01:28:10 UTC  
> Url: https://github.com/boostorg/build/issues/586#issuecomment-628971981  

Great, thank you!  
I've since compiled for   
toolset=msvc-14.1  
toolset=msvc-14.2  
  
And they create the '-gd' libs as expected. So it seems that just clang-win didn't match.  
  
I've been working with the clang-win Release libs instead, and msvc.

---

## Comment 5

> Username: stale[bot]  
> Created at: 2021-05-29 17:22:11 UTC  
> Url: https://github.com/boostorg/build/issues/586#issuecomment-850868291  

Thank you for your contributions. Main development of B2 has moved to https://github.com/bfgroup/b2  
This issue has been automatically marked as "transition" to indicate the potential for needing transition to the new B2 development project.
