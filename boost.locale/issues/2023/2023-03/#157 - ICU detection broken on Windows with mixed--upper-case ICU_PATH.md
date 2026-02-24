# #157 - ICU detection broken on Windows with mixed-/upper-case ICU_PATH [Closed]

> Username: Cogitri  
> Created at: 2023-03-15 11:13:18 UTC  
> Updated at: 2023-08-23 08:19:36 UTC  
> Closed at: 2023-08-23 08:19:35 UTC  
> Url: https://github.com/boostorg/locale/issues/157  

Hello,  
  
I was trying to build Boost.Locale 1.81.0 w/ ICU support via Conan. For this the following flags are passed to `b2`:  
  
```  
b2 -q target-os=windows architecture=x86 address-model=64 binary-format=pe abi=ms --layout=system --user-config=C:\.conan\ab25f2\1\src\tools\build\user-config.jam -sNO_ZLIB=0 -sNO_BZIP2=0 -sNO_LZMA=1 -sNO_ZSTD=1 boost.locale.icu=on boost.locale.iconv=off --disable-iconv runtime-link=shared runtime-debugging=on threading=multi visibility=hidden link=shared variant=release --with-atomic --with-chrono --with-container --with-date_time --with-exception --with-filesystem --with-iostreams --with-locale --with-log --with-program_options --with-random --with-regex --with-system --with-test --with-thread --with-url toolset=msvc cxxflags=/std:c++17 pch=on -sICU_PATH=C:\Users\vagrant\.conan\data\icu\72.1\_\_\package\37c0ff0745e1f400836948682306cb21048ba438 linkflags="" cxxflags="-fPIC" install --prefix=C:\.conan\4f301d\1 -j10 --abbreviate-paths -d0 --debug-configuration --build-dir="C:\.conan\2669f9\1\build-release"  
```  
  
With these flags, ICU isn't found during configuration and as such, ICU support is disabled. Changing `ICU_PATH` to be in lower case fixes the detection:  
  
```  
b2 -q target-os=windows architecture=x86 address-model=64 binary-format=pe abi=ms --layout=system --user-config=C:\.conan\ab25f2\1\src\tools\build\user-config.jam -sNO_ZLIB=0 -sNO_BZIP2=0 -sNO_LZMA=1 -sNO_ZSTD=1 boost.locale.icu=on boost.locale.iconv=off --disable-iconv runtime-link=shared runtime-debugging=on threading=multi visibility=hidden link=shared variant=release --with-atomic --with-chrono --with-container --with-date_time --with-exception --with-filesystem --with-iostreams --with-locale --with-log --with-program_options --with-random --with-regex --with-system --with-test --with-thread --with-url toolset=msvc cxxflags=/std:c++17 pch=on -sICU_PATH=c:\users\vagrant\.conan\data\icu\72.1\_\_\package\37c0ff0745e1f400836948682306cb21048ba438 linkflags="" cxxflags="-fPIC" install --prefix=C:\.conan\4f301d\1 -j10 --abbreviate-paths -d0 --debug-configuration --build-dir="C:\.conan\2669f9\1\build-release"  
```  
  
The diff between these is only the ICU_PATH:  
  
```diff  
-C:\Users\vagrant\.conan\data\icu\72.1\_\_\package\37c0ff0745e1f400836948682306cb21048ba438  
+c:\users\vagrant\.conan\data\icu\72.1\_\_\package\37c0ff0745e1f400836948682306cb21048ba438  
```  
  
One side-note which doesn't seem to affect all this but got me a bit off-track at first: In the logs of `b2`, Boost.Locale logs the following:  
  
```  
Search 64 bit ICU in /c:/users/vagrant/.conan/data/icu/72.1/_/_/package/0c952a6d19217f10d57daca31c441d7acd23430b/  
```  
  
The trailing slash here seems to be wrong on Windows (but I think that's just a faulty log message).

---

## Comment 1

> Username: Flamefire  
> Created at: 2023-03-15 17:45:45 UTC  
> Url: https://github.com/boostorg/locale/issues/157#issuecomment-1470483513  

> ```  
> Search 64 bit ICU in /c:/users/vagrant/.conan/data/icu/72.1/_/_/package/0c952a6d19217f10d57daca31c441d7acd23430b/  
> ```  
>   
> The trailing slash here seems to be wrong on Windows (but I think that's just a faulty log message).  
  
I think this is actually the issue: On Windows filesystems it is `C:/path` on POSIX-like filesystems (e.g. MinGW, Cygwin...) it is `/c/path` or similar. So this looks like a issue with your environment and/or B2, not Boost.Locale as there is nothing one can do here to fix this as far as I can tell.  
  
At least more information on the used environment would be required, maybe from the Conan guys.

---

## Comment 2

> Username: Cogitri  
> Created at: 2023-03-20 07:33:34 UTC  
> Url: https://github.com/boostorg/locale/issues/157#issuecomment-1475748172  

Hm, but `ICU_PATH` is passed correctly to `b2`, which is where this log message derives its value from, isn't it?. Additionally, even though this log message is faulty both with mixed-case and lower-case paths, the detection of ICU works with the latter.  
  
> At least more information on the used environment would be required, maybe from the Conan guys.  
  
Sure, what do you need? :)

---

## Comment 3

> Username: Flamefire  
> Created at: 2023-03-20 15:23:34 UTC  
> Url: https://github.com/boostorg/locale/issues/157#issuecomment-1476434856  

> Sure, what do you need? :)  
  
Pretty much everything, especially the used shell. I.e. is this Windows CMD, PowerShell, Bash, ... where the command is invoked from. AFAIK on Windows it would need to be (literally)  `C:` as the prefix, while on Bash (e.g MinGW, Cygwin...) it would need to start with a slash.   
  
This being on Windows means that case shouldn't matter unless a POSIX like environment is used where it may matter. The drive letter may need to be uppercase, but not fully sure if Windows would accept a lower case letter.  
  
> Additionally, even though this log message is faulty both with mixed-case and lower-case paths, the detection of ICU works with the latter.  
  
Ok to get this clear: Is the log message the same in both cases? Can you maybe attach the log output as a file/files?  
  
And finally: Can you reproduce this (especially the difference) manually on a clean Boost release download (i.e. especially no/empty build & bin.v2 folder)? I.e. after running bootstrap and then the mentioned command(s).  
  
Sorry I'm a bit in the dark here as I don't use Conan so I can't test this myself and I don't see where the issue may come from.

---

## Comment 4

> Username: Cogitri  
> Created at: 2023-08-23 08:19:35 UTC  
> Url: https://github.com/boostorg/locale/issues/157#issuecomment-1689503695  

Sorry, I didn't have time to work on this in the last months. Specifying `ICU_ICUUC_NAME` etc. fixed it.
