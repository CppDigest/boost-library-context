# #52 - Bit-field support? [Closed]

> Username: triplejam  
> Created at: 2025-05-25 05:40:08 UTC  
> Updated at: 2025-05-28 12:57:07 UTC  
> Closed at: 2025-05-28 12:57:06 UTC  
> Url: https://github.com/boostorg/describe/issues/52  

Doesn't seem to work with bit-fields because of taking their address is prohibited.

---

## Comment 1

> Username: triplejam  
> Created at: 2025-05-28 12:57:06 UTC  
> Url: https://github.com/boostorg/describe/issues/52#issuecomment-2916216139  

Actually I just noticed that's in the limitations section of the docs., so I will close this.  
  
But I think it might be possible using a proxy object, though at that point then it may as well be its own little library. Apparently you can get the bit lengths of the fields at compile time according to [this](https://stackoverflow.com/questions/20194009/size-of-a-bitfield-member)
