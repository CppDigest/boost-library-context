# #225 - Add documentation on how to install [Open]

> Username: xobs  
> Created at: 2018-08-02 04:19:42 UTC  
> Updated at: 2022-07-18 16:40:10 UTC  
> Url: https://github.com/boostorg/python/issues/225  

The README.md file says to build the package by running `faber`.  It does not say how to install, nor does it say how to specify where it installs to.  What is the `boostorg/python` equivalent to `./configure --prefix=/tmp; make; make install"`?

---

## Comment 1

> Username: stefanseefeld  
> Created at: 2018-08-02 11:01:52 UTC  
> Url: https://github.com/boostorg/python/issues/225#issuecomment-409889259  

See my response to #226 for a link to the official documentation on building and installing.  
Support for `Faber` as an alternative (and arguably much simpler) build system is ongoing, so installing and packaging isn't quite supported yet. I'm working on that, though (and would appreciate contributions, notably to the [Faber](https://stefanseefeld.github.io/faber) project itself).

---

## Comment 2

> Username: boegel  
> Created at: 2018-08-24 08:34:04 UTC  
> Url: https://github.com/boostorg/python/issues/225#issuecomment-415691984  

+1 on @xobs point.  
  
If `faber` is still incomplete, then it shouldn't (yet) be the recommended (well, only...) build procedure being mentioned in the README...  
  
At the very least, you should include a link to http://boostorg.github.io/python/doc/html/building/installing_boost_python_on_your_.html and http://boostorg.github.io/python/doc/html/building/configuring_boost_build.html (since the interface with the arrows is *very* confusing, it just looks like the documentation is incomplete) in the README.

---

## Comment 3

> Username: Nekto89  
> Created at: 2022-07-18 16:40:10 UTC  
> Url: https://github.com/boostorg/python/issues/225#issuecomment-1187722650  

Are there any advanced examples how to use faber with Visual Studio? I've tried to use it and it's not very intuitive. That's how I tried to get release build for VS2019 Update 11 with debug information. I'm not sure that it's the correct way.  
faber --builddir C:\temp\python_bld -l 4 "cppflags=/O2 /Ob2 /GL /Zo /Zi" "define=NDEBUG" "ldflags=/DEBUG /INCREMENTAL:NO /OPT:REF /OPT:ICF /LTCG" cxxstd=17 variant=release msvc.version=16.11.16 --with-boost-include=C:/3d/Boost-1.79.0-msvc16u11/include
