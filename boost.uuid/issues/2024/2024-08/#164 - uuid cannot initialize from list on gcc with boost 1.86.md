# #164 - uuid cannot initialize from list on gcc with boost 1.86 [Closed]

> Username: johnwason  
> Created at: 2024-08-18 20:11:56 UTC  
> Updated at: 2025-09-04 00:46:19 UTC  
> Closed at: 2025-09-04 00:46:19 UTC  
> Url: https://github.com/boostorg/uuid/issues/164  

I am seeing a weird error on gcc that I haven't seen before when compiling against boost 1.86:  
  
```  
/home/conda/feedstock_root/build_artifacts/robotraconteur_1723912067148/work/RobotRaconteurCore/src/HardwareTransport_linux.cpp:285:98: error: could not convert '{37, 187, 11, 98, 134, 26, 73, 116, 161, 184, 24, 237, 84, 149, 170, 7}' from '<brace-enclosed initializer list>' to 'boost::uuids::uuid'  
  285 |                                                    0xa1, 0xb8, 0x18, 0xed, 0x54, 0x95, 0xaa, 0x07};  
      |                                                                                                  ^  
      |                                                                                                  |  
      |                                                                                                  <brace-enclosed initializer list>  
```  
  
The offending code is this:  
  
```  
boost::uuids::uuid svc_uuid = {0x25, 0xbb, 0x0b, 0x62, 0x86, 0x1a, 0x49, 0x74,  
                                                   0xa1, 0xb8, 0x18, 0xed, 0x54, 0x95, 0xaa, 0x07};  
```  
  
This has been working for years with boost version 1.58 through version 1.85.

---

## Comment 1

> Username: pdimov  
> Created at: 2024-08-18 22:34:38 UTC  
> Url: https://github.com/boostorg/uuid/issues/164#issuecomment-2295419356  

The fix is  
```  
boost::uuids::uuid svc_uuid = {{0x25, 0xbb, 0x0b, 0x62, 0x86, 0x1a, 0x49, 0x74,  
                                                   0xa1, 0xb8, 0x18, 0xed, 0x54, 0x95, 0xaa, 0x07}};  
```  
(use double braces.)  
  
This has always been the "correct" way to initialize `uuid`, and the single braces version happened to work due to the brace elision rules because `uuid` was a simple `struct`. This is no longer the case after the 1.86 changes.  
  
I tried hard to keep all existing valid uses working, but that's basically impossible once any nontrivial changes are made.

---

## Comment 2

> Username: johnwason  
> Created at: 2024-08-18 22:42:52 UTC  
> Url: https://github.com/boostorg/uuid/issues/164#issuecomment-2295421174  

Interesting. I see the change now in the documentation between 1.85 and 1.86. Version 1.85 had single brackets.
