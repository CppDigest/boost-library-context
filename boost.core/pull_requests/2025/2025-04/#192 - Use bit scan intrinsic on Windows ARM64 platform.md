# #192 Use bit scan intrinsic on Windows ARM64 platform [Merged]

> Username: mborland  
> Created at: 2025-04-10 12:46:50 UTC  
> Updated at: 2025-04-16 12:13:43 UTC  
> Merged at: 2025-04-16 12:13:43 UTC  
> Closed at: 2025-04-16 12:13:43 UTC  
> Url: https://github.com/boostorg/core/pull/192  

On [MSVC ARM64](https://learn.microsoft.com/en-us/cpp/intrinsics/arm64-intrinsics?view=msvc-170) both `_BitScanForward64` and `_BitScanReverse64` are available. ARM runners [are not yet available](https://github.com/github/roadmap/issues/1098) with Github actions, but I have run this (and the entire core test suite) locally on an ARM64 Windows Machine:  
  
<img width="1772" alt="Screenshot 2025-04-10 at 8 41 35 AM" src="https://github.com/user-attachments/assets/57eaf98b-5942-40f8-bb15-d2356c63fb9b" />

---
