# #108 - Would docker-based builds for MIPS, s390x, and ppc64 in CI be useful? [Closed]

> Username: jeking3  
> Created at: 2019-07-05 00:22:15 UTC  
> Updated at: 2019-12-26 20:46:51 UTC  
> Closed at: 2019-12-26 20:46:51 UTC  
> Url: https://github.com/boostorg/context/issues/108  

I recently added a ppc64 multiarch build job to boostorg/uuid to prove out some endian issues.  I see a few architecture related issues open and resolved here; wondering if building in simulated architectures and running all the tests would be useful?  Thise multiarch docker images run 10-20 times slower so there could be some issues with builds timing out on GitHub, but I found it very useful in making sure things were working on those architectures.

---

## Comment 1

> Username: olk  
> Created at: 2019-07-05 05:51:08 UTC  
> Url: https://github.com/boostorg/context/issues/108#issuecomment-508638119  

in general it's a good idea - s390x is under development, but MIPS, PPC32/64 and RISC-V should work.
