# #94 - Executable permission bit on Windows [Closed]

> Username: uecasm  
> Created at: 2018-12-06 03:09:44 UTC  
> Updated at: 2020-05-04 23:12:33 UTC  
> Closed at: 2020-05-04 23:12:33 UTC  
> Url: https://github.com/boostorg/filesystem/issues/94  

Probably nobody particularly cares (including me), but while answering a question on the Boost-Users list today I noticed a bug in the way that Boost.Filesystem reports the executable bit for files on Windows.  
  
https://github.com/boostorg/filesystem/blob/ab4806b7f66d0374049bc1d157989fc3a19b5603/src/operations.cpp#L579-L585  
  
This code will set the bit when the path extension matches one of these literal strings.  
  
~Firstly, it's performing a case-sensitive comparison; while it does explicitly match both all-uppercase and all-lowercase variants, it will incorrectly fail to match mixed-case variants.~ (*Edit*: no, never mind, this does work correctly, it was just unobvious.)  
  
Secondly, the list is wrong -- both in being too short and too long.  It's too short because it doesn't consider the other extensions from the `PATHEXT` environment variable which will be automatically appended when trying to find a command to run from the command line.  It's too long because the batch file extensions cannot be directly run by `CreateProcess` but instead have to be run by `ShellExecute` -- but the latter can "run" any registered document files as well.  It should probably pick one of these intended definitions and stick with it rather than being inconsistent with either.

---

## Comment 1

> Username: pdimov  
> Created at: 2018-12-06 05:02:38 UTC  
> Updated at: 2018-12-06 05:04:03 UTC  
> Url: https://github.com/boostorg/filesystem/issues/94#issuecomment-444749252  

`equal_extension` does match mixed case.  
  
Unix shell scripts can't be directly run with ~`fork`~ `exec` so I'm not sure if the `CreateProcess` analogy holds. From a C++ viewpoint, executables are those that can be run with `std::system` so `PATHEXT` would probably be more appropriate.

---

## Comment 2

> Username: Lastique  
> Created at: 2020-05-04 23:12:33 UTC  
> Url: https://github.com/boostorg/filesystem/issues/94#issuecomment-623755879  

I don't think `PATHEXT` is applicable as it contains extensions of seemingly non-executable files. On my system it lists:  
  
```  
.COM;.EXE;.BAT;.CMD;.VBS;.VBE;.JS;.JSE;.WSF;.WSH;.MSC  
```  
  
I don't think you are supposed to run JavaScript from command line, for instance. My understanding is that it is the extensions system shell automatically adds to a file name as it tries to find it in some contexts. That context might not be limited to just executing it.  
  
Executable bit on Windows is a best effort emulation, and I agree with Peter in that it is not tied to a specific API. An executable file is something that can be run from shell command line. Personally, though, I would rather remove that code and not treat anything as executable.
