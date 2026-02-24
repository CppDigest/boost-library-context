# #308 - AArch64: add BTI hint to *fcontext trampolines - fixes BTI crash on Graviton4 [Closed]

> Username: zaydr-al  
> Created at: 2025-07-23 00:29:53 UTC  
> Updated at: 2025-08-26 11:22:24 UTC  
> Closed at: 2025-08-26 11:22:24 UTC  
> Url: https://github.com/boostorg/context/issues/308  

### Summary  
When Boost.Context is built with **PAC + BTI hardening** (``-mbranch-protection=standard``), the first indirect jump into any trampoline raises **SIGILL** on BTI-enforcing CPUs (Graviton 4).  
Cause: the trampolines are missing the mandatory **btic** hint.  
  
LLVM is currently fixing the analogous omission for compiler-generated landing pads in [llvm.llvm-project #149267](https://github.com/llvm/llvm-project/issues/149267) ([PR llvm/llvm-project#149680](https://github.com/llvm/llvm-project/pull/149680)).  
Hand-written assembly still needs the hint.  
  
### Minimal repro (Amazon Linux 2023, Boost develop, Clang 15):  
```  
#1. clone Boost super-repo  
git clone --branch develop --recursive https://github.com/boostorg/boost.git boost  
cd boost  
  
#2. build Boost.Context only, with PAC+BTI  
./bootstrap.sh >/dev/null  
./b2 -j$"(nproc)" -q --with-context toolset=clang \  
        cxxflags="-O2 -fPIC -mbranch-protection=standard" \  
        asmflags="-O2 -fPIC -mbranch-protection=standard" \  
        linkflags="-Wl,-z,force-bti" \  
        install --prefix=stage  
  
#3. small test that throws inside a fiber  
cat > test.cpp <<'CPP'  
#include <boost/context/fiber.hpp>  
#include <iostream>  
int main(){  
    boost::context::fiber f([](boost::context::fiber&& s){  
        try { throw 1; } catch(int){ std::cout<<"caught\n"; }  
        return std::move(s);  
    });   
    f = std::move(f).resume();  
}  
CPP  
  
clang++ -target aarch64-linux-gnu -mbranch-protection=standard \  
        -I$(pwd)/stage/include \  
        test.cpp -Lstage/lib -lboost_context \  
        -Wl,-z,force-bti \  
        -Wl,-rpath,$ORIGIN/../lib -Wl,-rpath,$(pwd)/stage/lib \  
        -o test_fiber  
  
# 4. run on Arm host that enforces BTI (Graviton 4)  
./test_fiber  
  
```  
  
### Proposed Fix  
Insert at the top of each AArch64 trampoline:  
```  
#if defined(__ARM_FEATURE_BTI_DEFAULT) && (__ARM_FEATURE_BTI_DEFAULT == 1)  
/* bti c */  
      hint        #34   
#endif  
```  
### Files  
```  
src/asm/jump_arm64_aapcs_elf_gas.S  
src/asm/make_arm64_aapcs_elf_gas.S  
src/asm/ontop_arm64_aapcs_elf_gas.S  
```  
Adds one 4-byte NOP-class instruction **only** when BTI is requested.  
  
**Environment**  
- Boost _develop_  
- AmazonLinux2023  
- Clang15, Clang 18  
- Hardware: AWSGraviton4

---

## Comment 1

> Username: ozbenh  
> Created at: 2025-07-23 00:45:16 UTC  
> Updated at: 2025-07-23 00:45:50 UTC  
> Url: https://github.com/boostorg/context/issues/308#issuecomment-3105259331  

You also need to add the `.note.gnu.property` section to indicate BTI support in every one of those .S files. Afaik, without that, the linker will not put it in the final binary (it should require it to be present in all the .o's) and thus the boost .so will not be mapped with PROT_BTI.  
  
In your example, you "work around" it by using force-bti, but that shouldn't be necessary if you added the section.

---

## Comment 2

> Username: zaydr-al  
> Created at: 2025-07-23 01:28:06 UTC  
> Url: https://github.com/boostorg/context/issues/308#issuecomment-3105325029  

Thanks, that makes sense. I encountered the warning (`all inputs do not have BTI in note section`) because I was forcing BTI at link time. Emitting the `.note.gnu.property` at the object level in each trampoline should indeed address this.

---

## Comment 3

> Username: olk  
> Created at: 2025-07-23 05:44:43 UTC  
> Url: https://github.com/boostorg/context/issues/308#issuecomment-3105802564  

Please provide an PR
