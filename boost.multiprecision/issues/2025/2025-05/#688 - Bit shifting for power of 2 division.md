# #688 - Bit shifting for power of 2 division [Open]

> Username: eddiewastaken  
> Created at: 2025-05-15 13:18:56 UTC  
> Updated at: 2025-05-15 21:04:00 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/688  

From some quick and rudimentary testing, a `>>` operation on several `boost::multiprecision` types is significantly more performant than the existing division operand, when the divisor is a power of 2. I'm expecting there to be a reason this check and shortcut isn't implemented, hence an issue and not a PR :)

---

## Comment 1

> Username: jzmaddock  
> Created at: 2025-05-15 17:29:06 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/688#issuecomment-2884568407  

Good question.  I guess the dilemma is how do we tell that a random divisor is a power of two without unduly hampering performance for the very much more common case that it is not?  We have the tools (lsb and msb) to determine "power of 2-ness", but I confess I haven't looked into this to see if it is a real concern.  Have you looked to see if gmp performs this optimisation?

---

## Comment 2

> Username: eddiewastaken  
> Created at: 2025-05-15 21:00:16 UTC  
> Updated at: 2025-05-15 21:03:59 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/688#issuecomment-2885042510  

> How do we tell that a random divisor is a power of two without unduly hampering performance for the very much more common case that it is not?  
  
I tried! Using your LSB suggestion (labelled `w/ check`) and shifting in that case, here's a bit more of a comparison, using `boost::multiprecision::cpp_int` and `limb_bits: 64`:  
  
```  
--- Small Dividend (256-bit) ---  
# Divisors - Powers of 2: (2^k)  
256-bits / 2^1         (no check)  |  Avg: 165.77 ns/op  
256-bits / 2^1         (w/ check)  |  Avg: 159.42 ns/op  
256-bits / 2^64        (no check)  |  Avg: 354.34 ns/op  
256-bits / 2^64        (w/ check)  |  Avg: 154.89 ns/op  
256-bits / 2^128       (no check)  |  Avg: 271.93 ns/op  
256-bits / 2^128       (w/ check)  |  Avg: 163.52 ns/op  
256-bits / 2^254       (no check)  |  Avg: 204.65 ns/op  
256-bits / 2^254       (w/ check)  |  Avg: 163.41 ns/op  
----------------  
Avg Speedup: +41.94%  
  
# Divisors - Not powers of 2: (2^k)-1  
256-bits / (2^1)-1     (no check)  |  Avg: 160.55 ns/op  
256-bits / (2^1)-1     (w/ check)  |  Avg: 125.95 ns/op  
256-bits / (2^64)-1    (no check)  |  Avg: 164.59 ns/op  
256-bits / (2^64)-1    (w/ check)  |  Avg: 230.39 ns/op  
256-bits / (2^128)-1   (no check)  |  Avg: 280.21 ns/op  
256-bits / (2^128)-1   (w/ check)  |  Avg: 328.46 ns/op  
256-bits / (2^254)-1   (no check)  |  Avg: 208.13 ns/op  
256-bits / (2^254)-1   (w/ check)  |  Avg: 263.61 ns/op  
----------------  
Avg Speedup: -17.34%  
  
--- Medium Dividend (512-bit) ---  
# Divisors - Powers of 2: (2^k)  
512-bits / 2^1         (no check)  |  Avg: 187.65 ns/op  
512-bits / 2^1         (w/ check)  |  Avg: 159.33 ns/op  
512-bits / 2^128       (no check)  |  Avg: 696.54 ns/op  
512-bits / 2^128       (w/ check)  |  Avg: 161.34 ns/op  
512-bits / 2^256       (no check)  |  Avg: 518.89 ns/op  
512-bits / 2^256       (w/ check)  |  Avg: 168.12 ns/op  
512-bits / 2^510       (no check)  |  Avg: 226.11 ns/op  
512-bits / 2^510       (w/ check)  |  Avg: 172.29 ns/op  
----------------  
Avg Speedup: +231.24%  
  
# Divisors - Not powers of 2: (2^k)-1  
512-bits / (2^1)-1     (no check)  |  Avg: 185.08 ns/op  
512-bits / (2^1)-1     (w/ check)  |  Avg: 128.79 ns/op  
512-bits / (2^128)-1   (no check)  |  Avg: 949.31 ns/op  
512-bits / (2^128)-1   (w/ check)  |  Avg: 1031.14 ns/op  
512-bits / (2^256)-1   (no check)  |  Avg: 703.18 ns/op  
512-bits / (2^256)-1   (w/ check)  |  Avg: 752.06 ns/op  
512-bits / (2^510)-1   (no check)  |  Avg: 239.50 ns/op  
512-bits / (2^510)-1   (w/ check)  |  Avg: 285.37 ns/op  
----------------  
Avg Speedup: -7.97%  
  
--- Large Dividend (1024-bit) ---  
# Divisors - Powers of 2: (2^k)  
1024-bits / 2^1        (no check)  |  Avg: 236.31 ns/op  
1024-bits / 2^1        (w/ check)  |  Avg: 171.76 ns/op  
1024-bits / 2^256      (no check)  |  Avg: 1317.38 ns/op  
1024-bits / 2^256      (w/ check)  |  Avg: 162.12 ns/op  
1024-bits / 2^512      (no check)  |  Avg: 985.86 ns/op  
1024-bits / 2^512      (w/ check)  |  Avg: 171.43 ns/op  
1024-bits / 2^1022     (no check)  |  Avg: 257.66 ns/op  
1024-bits / 2^1022     (w/ check)  |  Avg: 188.11 ns/op  
----------------  
Avg Speedup: +395.94%  
  
# Divisors - Not powers of 2: (2^k)-1  
1024-bits / (2^1)-1    (no check)  |  Avg: 232.57 ns/op  
1024-bits / (2^1)-1    (w/ check)  |  Avg: 123.00 ns/op  
1024-bits / (2^256)-1  (no check)  |  Avg: 1842.38 ns/op  
1024-bits / (2^256)-1  (w/ check)  |  Avg: 1750.58 ns/op  
1024-bits / (2^512)-1  (no check)  |  Avg: 1531.23 ns/op  
1024-bits / (2^512)-1  (w/ check)  |  Avg: 1410.04 ns/op  
1024-bits / (2^1022)-1 (no check)  |  Avg: 263.81 ns/op  
1024-bits / (2^1022)-1 (w/ check)  |  Avg: 318.71 ns/op  
----------------  
Avg Speedup: -1.96%  
  
--- Extra Large Dividend (4096-bit) ---  
# Divisors - Powers of 2: (2^k)  
4096-bits / 2^1        (no check)  |  Avg: 625.60 ns/op  
4096-bits / 2^1        (w/ check)  |  Avg: 243.26 ns/op  
4096-bits / 2^1024     (no check)  |  Avg: 10507.60 ns/op  
4096-bits / 2^1024     (w/ check)  |  Avg: 195.26 ns/op  
4096-bits / 2^2048     (no check)  |  Avg: 8328.20 ns/op  
4096-bits / 2^2048     (w/ check)  |  Avg: 245.70 ns/op  
4096-bits / 2^4094     (no check)  |  Avg: 484.04 ns/op  
4096-bits / 2^4094     (w/ check)  |  Avg: 306.56 ns/op  
----------------  
Avg Speedup: +2703.02%  
  
# Divisors - Not powers of 2: (2^k)-1  
4096-bits / (2^1)-1    (no check)  |  Avg: 515.42 ns/op  
4096-bits / (2^1)-1    (w/ check)  |  Avg: 130.22 ns/op  
4096-bits / (2^1024)-1 (no check)  |  Avg: 14803.92 ns/op  
4096-bits / (2^1024)-1 (w/ check)  |  Avg: 14909.20 ns/op  
4096-bits / (2^2048)-1 (no check)  |  Avg: 11176.70 ns/op  
4096-bits / (2^2048)-1 (w/ check)  |  Avg: 11229.82 ns/op  
4096-bits / (2^4094)-1 (no check)  |  Avg: 492.18 ns/op  
4096-bits / (2^4094)-1 (w/ check)  |  Avg: 541.12 ns/op  
----------------  
Avg Speedup: -1.94%  
```  
  
It seems as the dividend grows, the slowdown is reduced for non powers of 2, and the gains are highly amplified and pretty impressive. Overall though, the impact on the non powers of 2 appears to not be worth the trade-off like you said! :)
