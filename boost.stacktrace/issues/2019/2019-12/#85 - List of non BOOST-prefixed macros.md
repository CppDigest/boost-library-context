# #85 - List of non BOOST-prefixed macros [Closed]

> Username: ned14  
> Created at: 2019-12-16 14:30:10 UTC  
> Updated at: 2019-12-16 18:17:24 UTC  
> Closed at: 2019-12-16 18:17:24 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/85  

The following macros are missing a BOOST_ prefix, which is against Boost library guidelines:  
  
```  
./boost_1_72_0/boost/stacktrace/detail/frame_msvc.ipp:#ifdef __CRT_UUID_DECL // for __MINGW32__  
./boost_1_72_0/boost/stacktrace/detail/frame_msvc.ipp:    __CRT_UUID_DECL(IDebugClient,0x27fe5639,0x8407,0x4f47,0x83,0x64,0xee,0x11,0x8f,0xb0,0x8a,0xc8)  
./boost_1_72_0/boost/stacktrace/detail/frame_msvc.ipp:    __CRT_UUID_DECL(IDebugControl,0x5182e668,0x105e,0x416e,0xad,0x92,0x24,0xef,0x80,0x04,0x24,0xba)  
./boost_1_72_0/boost/stacktrace/detail/frame_msvc.ipp:    __CRT_UUID_DECL(IDebugSymbols,0x8c31e98c,0x983a,0x48a5,0x90,0x16,0x6f,0xe5,0xd6,0x67,0xa9,0x50)  
```

---

## Comment 1

> Username: apolukhin  
> Created at: 2019-12-16 17:50:19 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/85#issuecomment-566168810  

Sorry @ned14 , those are not mine.  
  
Those are a COM abominations from COM specific headers and I have no idea of how to avoid those.

---

## Comment 2

> Username: ned14  
> Created at: 2019-12-16 18:17:24 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/85#issuecomment-566178930  

And you don't define them either. Sorry, I was in mechanical copy and paste for a ton of libraries today. I had stopped paying attention to the content.
