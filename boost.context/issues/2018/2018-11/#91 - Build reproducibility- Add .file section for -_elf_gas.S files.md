# #91 - Build reproducibility: Add .file section for *_elf_gas.S files [Closed]

> Username: DouglasRoyds  
> Created at: 2018-11-21 21:10:53 UTC  
> Updated at: 2018-11-24 18:41:38 UTC  
> Closed at: 2018-11-24 18:41:38 UTC  
> Url: https://github.com/boostorg/context/issues/91  

Add a .file directive to all *_elf_gas.S files to prevent the linker adding a host build-system path as a FILE symbol to the object file. Without this, the gnu linker adds a symbol like this:  
  
    0000000000000000 0 FILE LOCAL DEFAULT ABS /path/to/my/build/.../asm/make_x86_64_sysv_elf_gas.o  
  
The attached file is a .patch file, but GitHub wouldn't upload it with the .patch extension:  
[0001-Reproducibility-Add-file-section-for-elf-gas-S-files.patch.txt](https://github.com/boostorg/context/files/2605796/0001-Reproducibility-Add-file-section-for-elf-gas-S-files.patch.txt)

---

## Comment 1

> Username: olk  
> Created at: 2018-11-24 18:41:38 UTC  
> Url: https://github.com/boostorg/context/issues/91#issuecomment-441387487  

ty
