# #29 - Is QVM meant to work in C++03 compilation mode [Closed]

> Username: eldiener  
> Created at: 2020-12-16 08:39:46 UTC  
> Updated at: 2020-12-17 13:48:24 UTC  
> Closed at: 2020-12-16 14:42:37 UTC  
> Url: https://github.com/boostorg/qvm/issues/29  

I thought I had previously found in the documentation that qvm requires the C++ standard compilation level to be C++11, but I can no longer find it. I know if I compile qvm at the C++03 level with the latest release of gcc-10.2 I get a number of errors. Is qvm supposed to require C++11 or not ?

---

## Comment 1

> Username: zajo  
> Created at: 2020-12-16 20:07:02 UTC  
> Url: https://github.com/boostorg/qvm/issues/29#issuecomment-746933389  

Yeah I should add a note to the documentation, QVM works on C++03.

---

## Comment 2

> Username: eldiener  
> Created at: 2020-12-17 13:48:23 UTC  
> Url: https://github.com/boostorg/qvm/issues/29#issuecomment-747449094  

It does compile at the C++03 level without errors. It was my previous mistake to say that it did not.
