# #66 Partly mitigate bad Clang inlining decision [Merged]

> Username: Kojoley  
> Created at: 2019-04-14 01:41:22 UTC  
> Updated at: 2019-05-09 12:16:10 UTC  
> Merged at: 2019-05-09 07:01:59 UTC  
> Closed at: 2019-05-09 07:01:59 UTC  
> Url: https://github.com/boostorg/variant/pull/66  

Because a visitor is wrapped several times during visitation it cases extra temporaries usage and useless store and loads that can only be optimized if the `visitation_impl` is inlined into the function that creates the wrapper. Clang inliner decides not to inline functions even with small-sized switches, resulting in a poor visitation code. Forceinline mark on those internal functions perceptibly improves the situation, though does not mitigate it completely.  
  
LLVM ticket https://bugs.llvm.org/show_bug.cgi?id=41491  
  
Before:  
```asm  
foo:  
	sub	rsp, 88  
	lea	rax, [rsp + 64]  
	mov	qword ptr [rsp + 72], rax  
	lea	rax, [rsp + 72]  
	mov	qword ptr [rsp + 80], rax  
	mov	eax, dword ptr [rcx]  
	lea	r9, [rcx + 4]  
	mov	edx, eax  
	sar	edx, 31  
	xor	edx, eax  
	xorps	xmm0, xmm0  
	movups	xmmword ptr [rsp + 48], xmm0  
	lea	r8, [rsp + 80]  
	mov	ecx, eax  
	call	tail  
	nop  
	add	rsp, 88  
	ret  
  
tail:  
	add	edx, -1  
	cmp	edx, 8  
	ja	.LBB1_2  
	lea	rax, [rip + .LJTI1_0]  
	movsxd	rcx, dword ptr [rax + 4*rdx]  
	add	rcx, rax  
	jmp	rcx  
```  
  
After:  
```asm  
foo:  
	sub	rsp, 56  
	lea	rax, [rsp + 40]  
	mov	qword ptr [rsp + 48], rax  
	lea	rdx, [rsp + 48]  
	call	tail  
	nop  
	add	rsp, 56  
	ret  
  
tail:  
	mov	ecx, dword ptr [rcx]  
	mov	eax, ecx  
	sar	eax, 31  
	xor	eax, ecx  
	add	eax, -1  
	cmp	eax, 8  
	ja	.LBB1_2  
	lea	rcx, [rip + .LJTI1_0]  
	movsxd	rax, dword ptr [rcx + 4*rax]  
	add	rax, rcx  
	jmp	rax  
```

---

## Comment 1

> Username: coveralls  
> Created_at: 2019-04-14 01:59:05 UTC  
> Updated_at: 2019-04-14 02:06:09 UTC  
> Url: https://github.com/boostorg/variant/pull/66#issuecomment-482911648  

[![Coverage Status](https://coveralls.io/builds/22794714/badge)](https://coveralls.io/builds/22794714)  
  
Coverage increased (+0.1%) to 95.404% when pulling **dd9b0c9b84f4227098cb38c5a51ae92c2124e42a on Kojoley:partly-mitigate-bad-clang-inlining-decision** into **74ea828cdea510d1011cd5110af1ef4f6adfaf7d on boostorg:develop**.

---

## Comment 2

> Username: apolukhin  
> Created_at: 2019-04-23 06:02:30 UTC  
> Url: https://github.com/boostorg/variant/pull/66#issuecomment-485655227  

I'm not a big fan of `force inline`... Is `inline` not enough for getting the better code?

---

## Comment 3

> Username: Kojoley  
> Created_at: 2019-04-23 14:30:03 UTC  
> Url: https://github.com/boostorg/variant/pull/66#issuecomment-485827707  

> Is `inline` not enough for getting the better code?  
  
Nope, as you can see I have literally replaced `inline` with `BOOST_FORCEINLINE`. It seems that LLVM inliner's cost model pessimizes switches too much.

---

## Comment 4

> Username: apolukhin  
> Created_at: 2019-05-02 10:22:56 UTC  
> Url: https://github.com/boostorg/variant/pull/66#issuecomment-488622839  

Clang has heuristics on explicit `inline`. You have added force inline in some cases where there was no explicit `inline`. Try replacing all the force inlines with just `inline`, it should give clang optimizer more info.  
  
I do not like force inline, as it is too often misused. I think that the compiler should decide when to inline and when not. If the compiler takes the wrong decision - then the compiler should be fixed, not the code. Fixing codegen with force inline one one platform could make the codegen on other platform worse.

---

## Comment 5

> Username: Kojoley  
> Created_at: 2019-05-02 12:04:22 UTC  
> Url: https://github.com/boostorg/variant/pull/66#issuecomment-488646827  

> Clang has heuristics on explicit `inline`.  
  
Did not know that, seems to be true. https://godbolt.org/z/q8JUle  
  
> You have added force inline in some cases where there was no explicit `inline`. Try replacing all the force inlines with just `inline`, it should give clang optimizer more info.  
  
This will not change anything. The thing is that clang seems to decide inlining or not exclusively on function size. Explicit `inline` slightly increases the inline cost limit, allowing to have one additional case in switch before it will decide not to inline the function. Globally it changes nothing.  
  
> I do not like force inline, as it is too often misused.  
  
It is not the case. The functions I have added forceinline to are exclusively internal. That's why I did not add the forceinline to `variant<T>::apply_visitor` member function, while it would allow optimizing out wrapper in `apply_visitor` free function.  
  
> I think that the compiler should decide when to inline and when not.  
  
In ideal world yes. But you know, "zero cost abstractions" are not always zero cost in reality.  
  
> If the compiler takes the wrong decision - then the compiler should be fixed, not the code.  
  
There is nothing wrong decision on compiler side. It did not offer perfect inlining, and I do not think inlining is something that can be done perfectly. GCC seems to be simply much more aggressive, and its just other side of the coin. The code is not compiler friendly, optimizing memory pointers is a very tough problem. If the visitor were stored and passed by value the situation should have been probably be much better.

---

## Comment 6

> Username: apolukhin  
> Created_at: 2019-05-09 07:02:04 UTC  
> Url: https://github.com/boostorg/variant/pull/66#issuecomment-490769777  

Many thanks!

---
