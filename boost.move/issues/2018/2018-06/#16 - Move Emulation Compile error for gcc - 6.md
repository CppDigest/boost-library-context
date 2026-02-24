# #16 - Move Emulation Compile error for gcc < 6 [Closed]

> Username: bc-lee  
> Created at: 2018-06-07 04:08:39 UTC  
> Updated at: 2018-09-18 12:35:22 UTC  
> Closed at: 2018-09-18 12:35:22 UTC  
> Url: https://github.com/boostorg/move/issues/16  

Consider this code: https://wandbox.org/permlink/jLbuodseGjnw6bIb  
  
boost version: 1.67  
CXXFLAGS: -std=c++98  
  
if gcc >= 6 or clang, Move emulation works as expected and run as expected.  
  
However, with gcc < 6 (ex. 5.5), this code fails to compile with these messages:  
```  
prog.cc:51:1: error: prototype for 'Handle::Handle(boost::rv<Handle>&)' does not match any in class 'Handle'  
 Handle::Handle(BOOST_RV_REF(Handle) other) BOOST_NOEXCEPT {  
```  
  
After I read an answer from stackoverflow ( https://stackoverflow.com/a/44038078/3627572  ),  if I redefine `BOOST_MAY_ALIAS` macro (In my code, `#define MAY_ALIAS_MONKEY_PATCH 1`), it works as expected, but I don't think it is a good solution for this issue.

---

## Comment 1

> Username: jeking3  
> Created at: 2018-08-01 13:46:51 UTC  
> Url: https://github.com/boostorg/move/issues/16#issuecomment-409580248  

Could you submit a pull request to the project adding a new test based on your example (as simplified as possible)?  This will allow for a code review of the test and then a follow-up fix using the test to prove the fix.

---

## Comment 2

> Username: igaztanaga  
> Created at: 2018-09-18 12:35:22 UTC  
> Url: https://github.com/boostorg/move/issues/16#issuecomment-422374079  

It seems that this is a GCC bug. I've found that MAY_ALIAS is not necessary for GCC compilers older than GCC 4.5 so I applied this patch, which at least helps GCC 4.4-4.8 and GCC 5 users:  
  
https://github.com/boostorg/move/commit/5e2e0f944e6bd7d16fbf49aef03c9c90f937ae52
