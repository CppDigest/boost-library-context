# #86 - have a plan to support riscv platform? [Closed]

> Username: jianjunjiang  
> Created at: 2018-10-27 02:34:17 UTC  
> Updated at: 2019-08-06 08:58:32 UTC  
> Closed at: 2018-11-26 01:35:40 UTC  
> Url: https://github.com/boostorg/context/issues/86  

riscv is very popular, have a plan to support riscv platform?

---

## Comment 1

> Username: olk  
> Created at: 2018-10-28 09:33:45 UTC  
> Url: https://github.com/boostorg/context/issues/86#issuecomment-433689707  

I'm too busy with other projects - but you are welcome to provide RSICV support via a pull-request.

---

## Comment 2

> Username: jianjunjiang  
> Created at: 2018-11-24 12:13:13 UTC  
> Updated at: 2018-11-24 14:55:08 UTC  
> Url: https://github.com/boostorg/context/issues/86#issuecomment-441363615  

```  
/*  
 *  -------------------------------------------------  *  
 *  |  0  |  1  |  2  |  3  |  4  |  5  |  6  |  7  |  *  
 *  -------------------------------------------------  *  
 *  | 0x0 | 0x4 | 0x8 | 0xc | 0x10| 0x14| 0x18| 0x1c|  *  
 *  -------------------------------------------------  *  
 *  |    fs0    |    fs1    |    fs2    |    fs3    |  *  
 *  -------------------------------------------------  *  
 *  -------------------------------------------------  *  
 *  |  8  |  9  |  10 |  11 |  12 |  13 |  14 |  15 |  *  
 *  -------------------------------------------------  *  
 *  | 0x20| 0x24| 0x28| 0x2c| 0x30| 0x34| 0x38| 0x3c|  *  
 *  -------------------------------------------------  *  
 *  |    fs4    |    fs5    |    fs6    |    fs7    |  *  
 *  -------------------------------------------------  *  
 *  -------------------------------------------------  *  
 *  |  16 |  17 |  18 |  19 |  20 |  21 |  22 |  23 |  *  
 *  -------------------------------------------------  *  
 *  | 0x40| 0x44| 0x48| 0x4c| 0x50| 0x54| 0x58| 0x5c|  *  
 *  -------------------------------------------------  *  
 *  |    fs8    |    fs9    |    fs10   |    fs11   |  *  
 *  -------------------------------------------------  *  
 *  -------------------------------------------------  *  
 *  |  24 |  25 |  26 |  27 |  28 |  29 |  30 |  31 |  *  
 *  -------------------------------------------------  *  
 *  | 0x60| 0x64| 0x68| 0x6c| 0x70| 0x74| 0x78| 0x7c|  *  
 *  -------------------------------------------------  *  
 *  |    s0     |    s1     |    s2     |    s3     |  *  
 *  -------------------------------------------------  *  
 *  -------------------------------------------------  *  
 *  |  32 |  33 |  34 |  35 |  36 |  37 |  38 |  39 |  *  
 *  -------------------------------------------------  *  
 *  | 0x80| 0x84| 0x88| 0x8c| 0x90| 0x94| 0x98| 0x9c|  *  
 *  -------------------------------------------------  *  
 *  |    s4     |    s5     |    s6     |     s7    |  *  
 *  -------------------------------------------------  *  
 *  -------------------------------------------------  *  
 *  |  40 |  41 |  42 |  43 |  44 |  45 |  46 |  47 |  *  
 *  -------------------------------------------------  *  
 *  | 0xa0| 0xa4| 0xa8| 0xac| 0xb0| 0xb4| 0xb8| 0xbc|  *  
 *  -------------------------------------------------  *  
 *  |    s8     |    s9     |    s10    |     s11   |  *  
 *  -------------------------------------------------  *  
 *  -------------------------------------------------  *  
 *  |  48 |  49 |  50 | 51  |           |           |  *  
 *  -------------------------------------------------  *  
 *  | 0xc0| 0xc4| 0xc8| 0xcc|           |           |  *  
 *  -------------------------------------------------  *  
 *  |     ra    |    pc     |           |           |  *  
 *  -------------------------------------------------  *  
 */  
	.text  
	.global make_fcontext  
	.align 3  
	.type make_fcontext, %function  
make_fcontext:  
	/* shift address in a0 (allocated stack) to lower 16 byte boundary */  
	andi a0, a0, ~0xf  
  
	/* reserve space for context-data on context-stack */  
	addi a0, a0, -(26 * 8)  
  
	/* third arg of make_fcontext() == address of context-function */  
	/* store address as a pc to jump in */  
	addi t0, a0, 0xc8  
	sd a2, (t0)  
  
	/* save address of finish as return-address for context-function */  
	/* will be entered after context-function returns (ra register) */  
	la a1, finish  
	addi t0, a0, 0xc0  
	sd a1, (t0)  
  
	/* return pointer to context-data (a0) */  
	ret  
  
finish:  
	/* exit code is zero */  
	li a0, 0  
	/* exit application */  
	call exit  
	ret  
  
/*  
 *  -------------------------------------------------  *  
 *  |  0  |  1  |  2  |  3  |  4  |  5  |  6  |  7  |  *  
 *  -------------------------------------------------  *  
 *  | 0x0 | 0x4 | 0x8 | 0xc | 0x10| 0x14| 0x18| 0x1c|  *  
 *  -------------------------------------------------  *  
 *  |    fs0    |    fs1    |    fs2    |    fs3    |  *  
 *  -------------------------------------------------  *  
 *  -------------------------------------------------  *  
 *  |  8  |  9  |  10 |  11 |  12 |  13 |  14 |  15 |  *  
 *  -------------------------------------------------  *  
 *  | 0x20| 0x24| 0x28| 0x2c| 0x30| 0x34| 0x38| 0x3c|  *  
 *  -------------------------------------------------  *  
 *  |    fs4    |    fs5    |    fs6    |    fs7    |  *  
 *  -------------------------------------------------  *  
 *  -------------------------------------------------  *  
 *  |  16 |  17 |  18 |  19 |  20 |  21 |  22 |  23 |  *  
 *  -------------------------------------------------  *  
 *  | 0x40| 0x44| 0x48| 0x4c| 0x50| 0x54| 0x58| 0x5c|  *  
 *  -------------------------------------------------  *  
 *  |    fs8    |    fs9    |    fs10   |    fs11   |  *  
 *  -------------------------------------------------  *  
 *  -------------------------------------------------  *  
 *  |  24 |  25 |  26 |  27 |  28 |  29 |  30 |  31 |  *  
 *  -------------------------------------------------  *  
 *  | 0x60| 0x64| 0x68| 0x6c| 0x70| 0x74| 0x78| 0x7c|  *  
 *  -------------------------------------------------  *  
 *  |    s0     |    s1     |    s2     |    s3     |  *  
 *  -------------------------------------------------  *  
 *  -------------------------------------------------  *  
 *  |  32 |  33 |  34 |  35 |  36 |  37 |  38 |  39 |  *  
 *  -------------------------------------------------  *  
 *  | 0x80| 0x84| 0x88| 0x8c| 0x90| 0x94| 0x98| 0x9c|  *  
 *  -------------------------------------------------  *  
 *  |    s4     |    s5     |    s6     |     s7    |  *  
 *  -------------------------------------------------  *  
 *  -------------------------------------------------  *  
 *  |  40 |  41 |  42 |  43 |  44 |  45 |  46 |  47 |  *  
 *  -------------------------------------------------  *  
 *  | 0xa0| 0xa4| 0xa8| 0xac| 0xb0| 0xb4| 0xb8| 0xbc|  *  
 *  -------------------------------------------------  *  
 *  |    s8     |    s9     |    s10    |     s11   |  *  
 *  -------------------------------------------------  *  
 *  -------------------------------------------------  *  
 *  |  48 |  49 |  50 | 51  |           |           |  *  
 *  -------------------------------------------------  *  
 *  | 0xc0| 0xc4| 0xc8| 0xcc|           |           |  *  
 *  -------------------------------------------------  *  
 *  |     ra    |    pc     |           |           |  *  
 *  -------------------------------------------------  *  
 */  
	.text  
	.global jump_fcontext  
	.align 3  
	.type jump_fcontext, %function  
jump_fcontext:  
	/* prepare stack */  
	addi sp, sp, -(26 * 8)  
  
	/* save fs0 - fs11 */  
#if __riscv_flen == 64  
	fsd fs0, 0 * 8(sp)  
	fsd fs1, 1 * 8(sp)  
	fsd fs2, 2 * 8(sp)  
	fsd fs3, 3 * 8(sp)  
	fsd fs4, 4 * 8(sp)  
	fsd fs5, 5 * 8(sp)  
	fsd fs6, 6 * 8(sp)  
	fsd fs7, 7 * 8(sp)  
	fsd fs8, 8 * 8(sp)  
	fsd fs9, 9 * 8(sp)  
	fsd fs10, 10 * 8(sp)  
	fsd fs11, 11 * 8(sp)  
#endif  
  
	/* save s0 - s11 */  
	sd s0, 12 * 8(sp)  
	sd s1, 13 * 8(sp)  
	sd s2, 14 * 8(sp)  
	sd s3, 15 * 8(sp)  
	sd s4, 16 * 8(sp)  
	sd s5, 17 * 8(sp)  
	sd s6, 18 * 8(sp)  
	sd s7, 19 * 8(sp)  
	sd s8, 20 * 8(sp)  
	sd s9, 21 * 8(sp)  
	sd s10, 22 * 8(sp)  
	sd s11, 23 * 8(sp)  
  
	/* save ra as pc */  
	sd ra, 25 * 8(sp)  
  
	/* store sp (pointing to context-data) in a0 */  
	mv t0, sp  
  
	/* restore sp (pointing to context-data) from a1 */  
	mv sp, a0  
  
	/* load fs0 - fs11 */  
#if __riscv_flen == 64  
	fld fs0, 0 * 8(sp)  
	fld fs1, 1 * 8(sp)  
	fld fs2, 2 * 8(sp)  
	fld fs3, 3 * 8(sp)  
	fld fs4, 4 * 8(sp)  
	fld fs5, 5 * 8(sp)  
	fld fs6, 6 * 8(sp)  
	fld fs7, 7 * 8(sp)  
	fld fs8, 8 * 8(sp)  
	fld fs9, 9 * 8(sp)  
	fld fs10, 10 * 8(sp)  
	fld fs11, 11 * 8(sp)  
#endif  
  
	/* load s0 - s11 */  
	ld s0, 12 * 8(sp)  
	ld s1, 13 * 8(sp)  
	ld s2, 14 * 8(sp)  
	ld s3, 15 * 8(sp)  
	ld s4, 16 * 8(sp)  
	ld s5, 17 * 8(sp)  
	ld s6, 18 * 8(sp)  
	ld s7, 19 * 8(sp)  
	ld s8, 20 * 8(sp)  
	ld s9, 21 * 8(sp)  
	ld s10, 22 * 8(sp)  
	ld s11, 23 * 8(sp)  
  
	/* return transfer_t from jump */  
	/* pass transfer_t as first arg in context function */  
	mv a0, t0  
  
	/* load pc */  
	ld ra, 25 * 8(sp)  
  
	/* restore stack */  
	addi sp, sp, (26 * 8)  
  
	ret  
```

---

## Comment 3

> Username: jianjunjiang  
> Created at: 2018-11-24 14:50:22 UTC  
> Url: https://github.com/boostorg/context/issues/86#issuecomment-441373196  

This code for riscv64 platform, using bare-metal elf format

---

## Comment 4

> Username: olk  
> Created at: 2018-11-24 18:32:54 UTC  
> Url: https://github.com/boostorg/context/issues/86#issuecomment-441386988  

hmm - well, could you clone boost.context ... apply your changes for RISCV64 ... test it (exec unit-tests) ... and if unit-tests are successful, create a pull request, please?

---

## Comment 5

> Username: jianjunjiang  
> Created at: 2018-11-26 01:35:40 UTC  
> Url: https://github.com/boostorg/context/issues/86#issuecomment-441494241  

I don't have a complete development environment for riscv64, just a bare metal in c language using qemu-virt

---

## Comment 6

> Username: vinipsmaker  
> Created at: 2019-08-06 08:58:32 UTC  
> Url: https://github.com/boostorg/context/issues/86#issuecomment-518578802  

Hey, I've noticed support for RISC-V already landed: https://github.com/boostorg/context/commit/c8fb4a42f3a8453948b5d893cfecd97c6827eee5  
  
But documentation still is outdated and it doesn't mention it: https://www.boost.org/doc/libs/1_70_0/libs/context/doc/html/context/architectures.html
