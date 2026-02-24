# #233 - Crash in regex_match [Open]

> Username: krishnacx  
> Created at: 2024-11-24 16:08:15 UTC  
> Updated at: 2024-11-26 14:10:45 UTC  
> Url: https://github.com/boostorg/regex/issues/233  

Boost version being used: 1.83.0.4  
  
```  
inline bool regex_match(const std::basic_string<charT, ST, SA>& s,   
                 const basic_regex<charT, traits>& e,   
                 match_flag_type flags = match_default)  
  
```  
My application is using the above function for a regex_match and it is crashing  
  
```  
0 core.so!boost::re_detail_500::perl_matcher<std::__1::__wrap_iter, std::__1::allocator<boost::sub_match<std::__1::__wrap_iter > >, boost::regex_traits > >::match_all_states() + 0x1ff  
  
rax = 0x000055f29fe1ad01 rdx = 0x00007f234021a6b0  
  
rcx = 0x00007f234021a6b0 rbx = 0x00007f232db9bc20  
  
rsi = 0x0000000000000003 rdi = 0x00007f232db9bc20  
  
rbp = 0x0000000000000000 rsp = 0x00007f232db9bae0  
  
r8 = 0x00007f232db9c61c r9 = 0x0000000000000000  
  
r10 = 0x00007f232db9c61c r11 = 0xffffffffffffffff  
  
r12 = 0x00007f232db9c608 r13 = 0x00007f23400f9380  
  
r14 = 0x00007f235931a5c0 r15 = 0x00007f235931a2e0  
  
rip = 0x00007f2356ffe8df  
  
Found by: given as instruction pointer in context  
  
1 core.so!boost::re_detail_500::perl_matcher<std::__1::__wrap_iter, std::__1::allocator<boost::sub_match<std::__1::__wrap_iter > >, boost::regex_traits > >::match_prefix() + 0xb4  
  
rbx = 0x0000000000000004 rbp = 0x0000000000000000  
  
rsp = 0x00007f232db9bb00 r12 = 0x00007f232db9c608  
  
r13 = 0x00007f23400f9380 r14 = 0x00007f232db9bc20  
  
r15 = 0x00007f232db9bc20 rip = 0x00007f2356ffe424  
  
Found by: call frame info  
  
2 core.so!boost::re_detail_500::perl_matcher<std::__1::__wrap_iter, std::__1::allocator<boost::sub_match<std::__1::__wrap_iter > >, boost::regex_traits > >::match_imp() + 0x226  
  
rbx = 0x00007f2340098950 rbp = 0x0000000000000000  
  
rsp = 0x00007f232db9bb20 r12 = 0x00007f232db9c608  
  
r13 = 0x00007f23400f9380 r14 = 0x00007f232db9bc20  
  
r15 = 0x00007f232db9bc20 rip = 0x00007f2357c3be36  
  
Found by: call frame info  
```  
  
What are the potential causes of crash ?

---

## Comment 1

> Username: jzmaddock  
> Created at: 2024-11-25 13:24:03 UTC  
> Url: https://github.com/boostorg/regex/issues/233#issuecomment-2498015661  

No idea, it shouldn't happen.  Do you have a test case we can look at?

---

## Comment 2

> Username: jzmaddock  
> Created at: 2024-11-25 13:24:19 UTC  
> Url: https://github.com/boostorg/regex/issues/233#issuecomment-2498016298  

Also forgot to ask what the Boost version is?

---

## Comment 3

> Username: krishnacx  
> Created at: 2024-11-26 04:09:18 UTC  
> Url: https://github.com/boostorg/regex/issues/233#issuecomment-2499614880  

Boost version being used: 1.83.0.4

---

## Comment 4

> Username: krishnacx  
> Created at: 2024-11-26 05:12:04 UTC  
> Updated at: 2024-11-26 05:12:56 UTC  
> Url: https://github.com/boostorg/regex/issues/233#issuecomment-2499675887  

**Boost version being used: 1.83.0.4**  
  
```  
std::string input_path = "/opt/"  
for (auto& path: paths)  
            {  
                if (boost::regex_match(input_path, std::get<boost::regex>(path)))  
                {  
                    return true;  
                }  
            }  
```  
  
I am invoking(regex_match) iteratively on a predefined set of paths until it matches.  
  
- Is there any harm in invoking regex_match back to back ?  
- Should there be any size limit of set ?

---

## Comment 5

> Username: jzmaddock  
> Created at: 2024-11-26 08:51:32 UTC  
> Url: https://github.com/boostorg/regex/issues/233#issuecomment-2500024004  

That doesn't help: I need a self contained test case with the actual inputs that cause the issue in order to reproduce here.

---

## Comment 6

> Username: krishnacx  
> Created at: 2024-11-26 10:40:05 UTC  
> Url: https://github.com/boostorg/regex/issues/233#issuecomment-2500264026  

Even I am unsure of the input path that has been passed since the crash is shared from a customer. Only thing that we know of the list of paths that input path is matched with.  
Are there any exceptions wrt string used as input path that shouln't be passed ?

---

## Comment 7

> Username: jzmaddock  
> Created at: 2024-11-26 14:10:44 UTC  
> Url: https://github.com/boostorg/regex/issues/233#issuecomment-2500904828  

It shouldn't crash whatever.  The only things I can think of are either:  
  
* Binary incompatibility with a mismatched libboost_regex.  
* Stack overflow if there was too little stack available when regex is invoked.  
  
Based on what I can see it's falling at the first hurdle - ie it's never really getting started at all.
