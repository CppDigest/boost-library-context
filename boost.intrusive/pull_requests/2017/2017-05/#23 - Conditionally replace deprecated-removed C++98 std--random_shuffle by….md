# #23 Conditionally replace deprecated/removed C++98 std::random_shuffle by… [Closed]

> Username: DanielaE  
> Created at: 2017-05-02 13:01:48 UTC  
> Updated at: 2017-06-08 12:05:01 UTC  
> Closed at: 2017-06-08 12:05:01 UTC  
> Url: https://github.com/boostorg/intrusive/pull/23  

… a drop-in implementation.  
  
Signed-off-by: Daniela Engert <dani@ngrt.de>

---

## Comment 1

> Username: DanielaE  
> Created_at: 2017-05-02 13:07:12 UTC  
> Url: https://github.com/boostorg/intrusive/pull/23#issuecomment-298630018  

Tested with msvc 9.0, 10.0, 12.0, 14.0 and 14.1 . The 14.x compilers run in C++17 mode which defines BOOST_NO_CXX98_RANDOM_SHUFFLE and triggers the replacement code path.

---

## Comment 2

> Username: igaztanaga  
> Created_at: 2017-06-08 12:05:00 UTC  
> Url: https://github.com/boostorg/intrusive/pull/23#issuecomment-307083532  

Commit:  
  
https://github.com/boostorg/intrusive/commit/db14782f0f7aa72b502b6296f0543ac0457d497e  
  
uses a different approach implementing random_shuffle and uses it for all standard versions.  
  
Many thanks for the report and the patch.

---
