# #64 - [IntegralConstant] Add support for all integer literals with the _c suffix [Closed]

> Username: ldionne  
> Created at: 2015-05-04 16:46:00 UTC  
> Updated at: 2015-11-20 14:26:40 UTC  
> Closed at: 2015-11-20 14:26:40 UTC  
> Url: https://github.com/boostorg/hana/issues/64  

Writing `0x1234_c` should be equivalent to `llong<0x1234>`. Similarly, `0b10101` should be equivalent to `llong<0b10101>`. Same for octal.

---

## Comment 1

> Username: badair  
> Created at: 2015-11-19 06:58:39 UTC  
> Updated at: 2015-11-19 08:09:54 UTC  
> Url: https://github.com/boostorg/hana/issues/64#issuecomment-157970676  

Feature added in pull request #210.

---

## Comment 2

> Username: ldionne  
> Created at: 2015-11-20 14:26:40 UTC  
> Url: https://github.com/boostorg/hana/issues/64#issuecomment-158414749  

Closed by 87f82e9
