# #160 - Android NDK clang on OSX [Open]

> Username: dschmitt006  
> Created at: 2017-02-07 14:30:38 UTC  
> Updated at: 2021-06-26 03:10:24 UTC  
> Url: https://github.com/boostorg/build/issues/160  

build/src/tools/clang-darwin.jam does  
  
    actions piecemeal archive  
    {  
        "$(.AR)" $(AROPTIONS) rc "$(<)" "$(>)"  
        "ranlib" -cs "$(<)"  
    }  
  
which ignores `<archiver>` and `<ranlib>` from any .bjam config.  
  
This is bad when trying to set up an Android cross compile using clang (and it's very disturbing when everything "just worked" for Android g++/NDK....)  
  
Everything installs nicely, but with a minor warning  
  
    warning: ranlib: warning for library: /tmp/build/armeabi/boost/boost/bin.v2/libs/system/build/clang-darwin-android/release/link-static/target-os-android/threading-multi/libboost_system.a the table of contents is empty (no object file members in the library define global symbols)  
  
and when you try to use the library with CMake/NDK it fails with  
  
    libboost_program_options.a: no archive symbol table (run ranlib)  
  
It seems odd that the Android NDK bundles up everything as the same version (takes the same flags, makes the same output) across Windows/OSX/linux but the boost configuration options shard the handling across the host type.

---

## Comment 1

> Username: studiofuga  
> Created at: 2017-03-23 22:38:45 UTC  
> Url: https://github.com/boostorg/build/issues/160#issuecomment-288881979  

I was able to fix this manually, running `arm-linux-androideabi-ranlib` on each static library.

---

## Comment 2

> Username: dschmitt006  
> Created at: 2017-03-23 22:58:08 UTC  
> Url: https://github.com/boostorg/build/issues/160#issuecomment-288885760  

Yes, running the *-ranlib is the correct fix, the build system should do it (and choose the right one to use etc.)

---

## Comment 3

> Username: swatanabe  
> Created at: 2018-01-09 23:32:43 UTC  
> Url: https://github.com/boostorg/build/issues/160#issuecomment-356448932  

You should be able to use clang-linux explicitly instead of just clang to avoid the OSX specific behavior.  I've been meaning to merge the various clang toolsets together as there's really no good reason to split them up.  Also, clang in develop now respects <archiver> and <ranlib>, although I haven't duplicated the gcc logic for finding them (g++ -print-prog-name=ar), yet.

---

## Comment 4

> Username: stale[bot]  
> Created at: 2021-06-26 03:09:46 UTC  
> Url: https://github.com/boostorg/build/issues/160#issuecomment-868936535  

Thank you for your contributions. Main development of B2 has moved to https://github.com/bfgroup/b2  
This issue has been automatically marked as "transition" to indicate the potential for needing transition to the new B2 development project.
