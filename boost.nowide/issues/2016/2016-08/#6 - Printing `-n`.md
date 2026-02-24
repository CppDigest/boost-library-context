# #6 - Printing `\n`? [Closed]

> Username: MikaelSmith  
> Created at: 2016-08-12 20:57:12 UTC  
> Updated at: 2017-06-10 14:29:25 UTC  
> Closed at: 2017-06-10 14:28:06 UTC  
> Url: https://github.com/boostorg/nowide/issues/6  

The `boost::nowide::cout` implementation appears to skip doing conversion of `\n` to `\r\n` on Windows, which I believe is how `std::cout` normally does it (when not a binary stream). This makes it tricker to get correct line endings on Windows.

---

## Comment 1

> Username: MikaelSmith  
> Created at: 2016-08-12 21:47:46 UTC  
> Url: https://github.com/boostorg/nowide/issues/6#issuecomment-239567895  

Nevermind. This may be a peculiarity of running Windows under Docker.

---

## Comment 2

> Username: MikaelSmith  
> Created at: 2016-08-12 22:40:45 UTC  
> Updated at: 2016-08-12 22:41:38 UTC  
> Url: https://github.com/boostorg/nowide/issues/6#issuecomment-239576839  

Ok, this does seem to deviate from `std::cout` behavior on Microsoft Nano Server under Docker (see https://github.com/Microsoft/Virtualization-Documentation/blob/live/virtualization/windowscontainers/quick_start/quick_start_windows_10.md for instructions if you're interested in trying that out).  
  
It seems to behave correctly in Windows 10 however, so I'm not exactly sure what might need fixing.  
  
My example is pretty much  
  
```  
  std::cout << "Hello\n";  
  std::cout << "World\n" << std::flush;  
  
  boost::nowide::cout << "Hello\n";  
  boost::nowide::cout << "World\n" << std::flush;  
```  
  
piped to a file, where I then check line endings. The `std::cout` version uses `crlf`, where-as the `nowide::cout` version only has `lf`, but only under Docker.

---

## Comment 3

> Username: artyom-beilis  
> Created at: 2017-06-10 14:29:25 UTC  
> Url: https://github.com/boostorg/nowide/issues/6#issuecomment-307568481  

Fixed in 9b3c367868a1efa7caa953f27af5eaccd7db8b68
