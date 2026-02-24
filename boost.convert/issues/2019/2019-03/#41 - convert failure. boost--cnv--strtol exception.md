# #41 - convert failure. boost::cnv::strtol  exception [Closed]

> Username: liujingliangPacs  
> Created at: 2019-03-19 07:27:18 UTC  
> Updated at: 2020-09-12 21:03:23 UTC  
> Closed at: 2020-09-12 21:03:23 UTC  
> Url: https://github.com/boostorg/convert/issues/41  

boost::cnv::strtol cnv;  
std::string str = "0";  
auto intValue = boost::convert<uint32_t>(  
    str,  
    cnv  
    (boost::cnv::parameter::skipws = true)  
    (boost::cnv::parameter::base = boost::cnv::base::hex)  
    ).value();  
this sentence will trigger exception.

---

## Comment 1

> Username: yet-another-user  
> Created at: 2019-03-19 10:47:05 UTC  
> Url: https://github.com/boostorg/convert/issues/41#issuecomment-474302440  

Thank you for reporting. I'll look into it ASAP.

---

## Comment 2

> Username: yet-another-user  
> Created at: 2019-03-19 22:02:33 UTC  
> Url: https://github.com/boostorg/convert/issues/41#issuecomment-474602166  

I can't reproduce it with gcc 7.3.0. What do you use? Would you mind trying with char const* str = "0"; please? Thanks much.

---

## Comment 3

> Username: yet-another-user  
> Created at: 2020-09-12 21:03:23 UTC  
> Url: https://github.com/boostorg/convert/issues/41#issuecomment-691547916  

I can't see any progress with the issue. So, I guess, it is not an issue anymore. Closing.
