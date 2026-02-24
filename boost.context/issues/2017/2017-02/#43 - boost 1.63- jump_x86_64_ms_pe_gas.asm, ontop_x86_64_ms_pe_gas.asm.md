# #43 - boost 1.63: jump_x86_64_ms_pe_gas.asm, ontop_x86_64_ms_pe_gas.asm [Closed]

> Username: viccpp  
> Created at: 2017-02-14 14:16:34 UTC  
> Updated at: 2017-02-15 07:06:04 UTC  
> Closed at: 2017-02-14 16:30:35 UTC  
> Url: https://github.com/boostorg/context/issues/43  

I use MinGW-w64 (`b2 toolset=gcc` on Windows)  
  
```  
Program received signal SIGSEGV, Segmentation fault.  
jump_fcontext () at jump_x86_64_ms_pe_gas.asm:191  
191         movq  %r9, (%rax)  
```  
  
The line in `jump_x86_64_ms_pe_gas.asm`  
  
```  
movq  0x110(%rsp), %rcx  /* restore hidden address of transport_t */  
```  
  
differs from the corresponding one in `jump_x86_64_ms_pe_masm.asm` which contains  
  
```  
mov rax, [rsp+0110h] ; restore hidden address of transport_t  
```

---

## Comment 1

> Username: viccpp  
> Created at: 2017-02-14 14:21:35 UTC  
> Url: https://github.com/boostorg/context/issues/43#issuecomment-279719481  

The same in `ontop_x86_64_ms_pe_gas.asm`

---

## Comment 2

> Username: olk  
> Created at: 2017-02-14 14:46:32 UTC  
> Url: https://github.com/boostorg/context/issues/43#issuecomment-279726478  

The code for 1.64 (branch develop) has already changed - could you test the code from branch develop and verify that the fix is working, please?

---

## Comment 3

> Username: viccpp  
> Created at: 2017-02-14 14:57:58 UTC  
> Url: https://github.com/boostorg/context/issues/43#issuecomment-279729803  

Replacing `%rcx` with `%rax` in 1.63 works for me.  
Has anything else been changed? If so, I can try 1.64 too.

---

## Comment 4

> Username: viccpp  
> Created at: 2017-02-14 15:09:39 UTC  
> Url: https://github.com/boostorg/context/issues/43#issuecomment-279733230  

I see also two `(%rax)` replaced with `0x0(%rax)` here https://github.com/boostorg/context/commit/3ea3b5cc904191fd7e5b9c98c01e85db326e094a  
  
Is it essential?

---

## Comment 5

> Username: viccpp  
> Created at: 2017-02-14 15:12:43 UTC  
> Url: https://github.com/boostorg/context/issues/43#issuecomment-279734156  

And, taking the opportunity, I'd like to inquire. Why GNU as sources on windows have `.asm` extension instead of `.S`?

---

## Comment 6

> Username: olk  
> Created at: 2017-02-14 16:30:32 UTC  
> Url: https://github.com/boostorg/context/issues/43#issuecomment-279758747  

yes

---

## Comment 7

> Username: viccpp  
> Created at: 2017-02-15 06:59:47 UTC  
> Url: https://github.com/boostorg/context/issues/43#issuecomment-279932460  

> The code for 1.64 (branch develop) has already changed - could you test the code from branch develop and verify that the fix is working, please?  
  
Simple test works fine.

---

## Comment 8

> Username: olk  
> Created at: 2017-02-15 07:06:04 UTC  
> Url: https://github.com/boostorg/context/issues/43#issuecomment-279933356  

ty
