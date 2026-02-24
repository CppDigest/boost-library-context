# #121 - Default-constructing boost::posix_time::ptime and possibly other types is slow [Closed]

> Username: p12tic  
> Created at: 2020-02-03 21:21:27 UTC  
> Updated at: 2022-08-01 10:34:37 UTC  
> Closed at: 2022-08-01 10:34:37 UTC  
> Url: https://github.com/boostorg/date_time/issues/121  

I've noticed `boost::posix_time::ptime` default constructor in profiler traces when profiling an application that does non-insignificant amount of date/time handling. This was unexpected, because the compiler should be able to optimize the constructor to storing some constants to memory.  
  
Turns out that the compiler fails to inline `boost::gregorian::date::date(special_values)`. The following is the test case:  
  
```  
#include <boost/date_time/posix_time/ptime.hpp>  
  
void do_stuff(boost::posix_time::ptime& time)  
{  
    time = boost::posix_time::ptime();  
}  
```  
  
The following or equivalent assembly has been observed on GCC 6.5.0, 7.4.0 and 8.3.0 on `-O2` or `-O3` optimization levels on x86_64 with stack protector disabled (`-fno-stack-protector`):  
  
```  
_Z8do_stuffRN5boost10posix_time5ptimeE:  
	push	rbx  
	mov	rbx, rdi  
	xor	esi, esi  
	sub	rsp, 16  
	lea	rdi, 12[rsp]  
	call	_ZN5boost9gregorian4dateC1ENS_9date_time14special_valuesE  
	movabs	rax, 9223372036854775806  
	mov	QWORD PTR [rbx], rax  
	add	rsp, 16  
	pop	rbx  
	ret  
  
_ZN5boost9gregorian4dateC2ENS_9date_time14special_valuesE:  
	cmp	esi, 4  
	ja	.L641  
	lea	rdx, .L643[rip]  
	mov	esi, esi  
	movsx	rax, DWORD PTR [rdx+rsi*4]  
	add	rax, rdx  
	jmp	rax  
.L643:  
	.long	.L642-.L643  
	.long	.L644-.L643  
	.long	.L649-.L643  
	.long	.L646-.L643  
	.long	.L647-.L643  
.L647:  
	mov	DWORD PTR [rdi], 5373484  
	ret  
.L646:  
	mov	DWORD PTR [rdi], 2232400  
	ret  
.L649:  
	mov	eax, -1  
	mov	DWORD PTR [rdi], eax  
	ret  
.L644:  
	xor	eax, eax  
	mov	DWORD PTR [rdi], eax  
	ret  
.L642:  
	mov	eax, -2  
	mov	DWORD PTR [rdi], eax  
	ret  
.L641:  
	mov	DWORD PTR [rdi], -2  
	ret  
```  
  
As you can see, this is rather a large amount of instructions for a function that should just store some constant to memory.  
  
After marking `boost::gregorian::date::date(special_values)` as BOOST_FORCEINLINE, we get the following:   
  
```  
_Z8do_stuffRN5boost10posix_time5ptimeE:  
	movabs	rax, 9223372036854775806  
	mov	QWORD PTR [rdi], rax  
	ret  
```  
  
I propose that we apply this workaround to the library as it would improve the code size and speed for the majority of user workloads that are affected.   
  
The only possibility this could be a regression would be in rarely executed call sites that pass non-constant value of `special_values` directly or indirectly to `boost::gregorian::date::date`. However, forcing inlining in such a case would just inflate code size a little bit and I suspect that the library user would still save code size overall due to significant reductions of code size around calls to default constructor of `ptime` and related types.

---

## Comment 1

> Username: JeffGarland  
> Created at: 2020-03-01 23:31:06 UTC  
> Url: https://github.com/boostorg/date_time/issues/121#issuecomment-593164810  

Note that I haven't forgotten about this.  However, I'm going down a bit of a different track which may lead to similar or better impact without the non-standard forcing of inline.  Specifically, I'm starting to put constexpr into the core so that the compiler can indeed calculate more at compile time.  Which should hopefully also cover this case.  So keeping the issue open for now.
