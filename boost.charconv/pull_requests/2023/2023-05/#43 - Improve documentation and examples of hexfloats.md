# #43 Improve documentation and examples of hexfloats [Merged]

> Username: mborland  
> Created at: 2023-05-18 11:35:38 UTC  
> Updated at: 2023-06-02 07:34:12 UTC  
> Merged at: 2023-06-02 07:34:08 UTC  
> Closed at: 2023-06-02 07:34:08 UTC  
> Url: https://github.com/boostorg/charconv/pull/43  

Closes: #33

---

## Comment 1

> Username: mborland  
> Created_at: 2023-05-18 11:40:10 UTC  
> Url: https://github.com/boostorg/charconv/pull/43#issuecomment-1552931592  

@NAThompson On the ML someone asked to improve the documentation and examples for hexfloats. Is there anything else you think should be covered?

---

## Comment 2

> Username: cppalliance-bot  
> Created_at: 2023-05-18 11:44:59 UTC  
> Url: https://github.com/boostorg/charconv/pull/43#issuecomment-1552936157  

An automated preview of the documentation is available at [https://43.charconv.prtest.cppalliance.org/libs/charconv/doc/html/index.html](https://43.charconv.prtest.cppalliance.org/libs/charconv/doc/html/index.html)

---

## Comment 3

> Username: NAThompson  
> Created_at: 2023-05-18 15:08:22 UTC  
> Url: https://github.com/boostorg/charconv/pull/43#issuecomment-1553207325  

@mborland : Nice writeup. One thing I have gotten stung by in the past (foolishly, of course) is that despite every float having a unique representation as a hexfloat, not every hexfloat corresponds to a unique float. This is because the number of bits in the significant of float32 and float64 is not divisible by 4.

---

## Comment 4

> Username: mborland  
> Created_at: 2023-05-18 15:22:20 UTC  
> Url: https://github.com/boostorg/charconv/pull/43#issuecomment-1553225675  

> ... is that despite every float having a unique representation as a hexfloat, not every hexfloat corresponds to a unique float. This is because the number of bits in the significant of float32 and float64 is not divisible by 4.  
  
I added that as a note because you could theoretically try to roundtrip binary to hex and back. Thanks.

---

## Comment 5

> Username: NAThompson  
> Created_at: 2023-05-18 15:24:03 UTC  
> Url: https://github.com/boostorg/charconv/pull/43#issuecomment-1553227936  

> I added that as a note because you could theoretically try to roundtrip binary to hex and back.  
  
Yeah . . . that's what I was trying when I learned about it!

---

## Comment 6

> Username: cppalliance-bot  
> Created_at: 2023-05-18 15:30:06 UTC  
> Url: https://github.com/boostorg/charconv/pull/43#issuecomment-1553236003  

An automated preview of the documentation is available at [https://43.charconv.prtest.cppalliance.org/libs/charconv/doc/html/index.html](https://43.charconv.prtest.cppalliance.org/libs/charconv/doc/html/index.html)

---
