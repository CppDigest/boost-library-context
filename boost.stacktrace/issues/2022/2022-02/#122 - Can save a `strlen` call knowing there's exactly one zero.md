# #122 - [Windows] Can save a `strlen` call knowing there's exactly one zero [Closed]

> Username: AlexGuteniev  
> Created at: 2022-02-09 13:24:01 UTC  
> Updated at: 2022-09-01 18:18:52 UTC  
> Closed at: 2022-09-01 16:50:18 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/122  

Not sure if it is really needed, as perf gain and simplification are both very small.  
  
Referring to this:  
https://github.com/boostorg/stacktrace/blob/75b7986f9799184ecd679d86273532cb54e6a0dc/include/boost/stacktrace/detail/frame_msvc.ipp#L222-L234  
  
According to the updated [IDebugSymbols::GetNameByOffset documentation](https://docs.microsoft.com/en-us/windows-hardware/drivers/ddi/dbgeng/nf-dbgeng-idebugsymbols-getnamebyoffset), sizes will include the space for exactly one null terminator:  
  
> This size includes the space for the '\0' terminating character.  
  
So the assignment in the else branch can be like:  
```c++  
result.assign(name, size - 1);  
```  
and `trim_right_zeroes` can be avoided by resizing string as `result.resize(size - 1)` prior the second call.  
  
Ditto for `GetLineByOffset` below.

---

## Comment 1

> Username: apolukhin  
> Created at: 2022-09-01 16:50:18 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/122#issuecomment-1234535532  

Done in https://github.com/boostorg/stacktrace/commit/7c6778e9f4d43f29d215b3dd910a6fee6a3db840  
  
@AlexGuteniev  note that the docs for `GetLineByOffset()` still have no `This size includes the space for the '\0' terminating character.`, so I left the code as is for that call. The impact should be unnoticeable.

---

## Comment 2

> Username: AlexGuteniev  
> Created at: 2022-09-01 18:18:52 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/122#issuecomment-1234628069  

@apolukhin, I did a bulk fix of these https://github.com/MicrosoftDocs/windows-driver-docs-ddi/pull/1255 , but unfortunately missed this one 😕
