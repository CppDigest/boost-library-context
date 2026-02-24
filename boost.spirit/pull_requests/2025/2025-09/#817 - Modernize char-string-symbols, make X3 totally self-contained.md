# #817 [X3] Modernize char/string/symbols, make X3 totally self-contained [Merged]

> Username: saki7  
> Created at: 2025-09-08 01:41:10 UTC  
> Updated at: 2025-09-10 09:07:17 UTC  
> Merged at: 2025-09-10 09:06:31 UTC  
> Closed at: 2025-09-10 09:06:31 UTC  
> Url: https://github.com/boostorg/spirit/pull/817  

Part of boostorg/spirit_x4#4  
Fixes boostorg/spirit_x4#14   
  
The diff is very large because I copied the `home/support/char_encoding/` components to X3's subdirectory.   
Modernization changes are about 2,000 additions and 1,800 deletions only.  
  
## Summary  
  
- `char_encoding` and `char_set` related components are copied from the `spirit/home/support/` directory to the `spirit/home/x3/` directory, and then modernized.  
  - This would make X3 self-contained (finally!!!!!) and safely allows upgrading the codebase to C++23-style.  
  - Note that other spirit components only support C++11, therefore adding C++23 support for X3 is impossible without this change. Macros are useless because there's no guarantee that the modernization can be expressed only by macros, and ODR violations would be engaged.  
  - Unicode tables are copied as-is, with minor modernization patches.  
- `x3::lit` and `x3::string` now uses concepts for overload resolution; provides significantly improved compilation speed and better errors.  
- Mixture of incompatible character types (e.g. `char` and `wchar_t`) is now officially banned. Such wrong usage would invoke the deleted functions, safely alerting the end users. This would prevent issues like https://github.com/boostorg/spirit/issues/678#issuecomment-2376313379, which involves incorrect understanding of character encodings. Such misuse would introduce serious security issues and should be immediately banned.  
- Add `constexpr` to almost everywhere.  
- Rename `x3::symbols` to `x3::shared_symbols`.  
  - Bonus: the new implementation has full allocator support, including `std::allocator_traits<allocator_type>::propagate_on_container_move_assignment`, etc.  
  - Make `x3::symbols` the alias of `x3::shared_symbols`, with `[[deprecated]]` warning.  
- `x3::unique_symbols`: Added; this is the `std::unique_ptr` counterpart of `x3::shared_symbols`.  
  
## Deprecation of `ascii` and `iso8859_1`  
  
Hard-coding ASCII or Latin-1 assumptions is almost always wrong in modern applications and **can introduce security vulnerabilities.**  
  
Unless the input is provably ASCII-only (e.g., fixed-format data from the 1980s), the only correct interpretation uses the data's *original* encoding. Users should prefer transcoding to Unicode or to the implementation-defined execution character set before passing text to general-purpose parsers. For more information, refer to authoritative sources on C++ character encodings and Unicode fundamentals.   
  
## We should recognize the fact that the global `spirit/home/support/` directory had been a huge contribution barrier.  
  
As a CJK (Japanese) speaker, I always wanted to contribute to the unicode components of X3. I even had tons of `constexpr` support even before this PR. However, **I hesitated to contribute for at least 5 years.** Why? Because the `char_encoding` functionalities were inside the global `spirit/home/support/` directory.   
  
That made me fear that **I would break four Boost Libraries**, because a single mistake in the `support/` directory would result in total breakage.  
  
**The destination folder I chose is `spirit/home/x3/char_encoding/`, NOT `spirit/home/x3/support/char_encoding/`.** The subdirectory "support" is very poorly named, and even I always become confused by thinking what components are actually inside "support" and what are not. To be honest, EVERY "supportive" components can virtually fit inside the "support" subdirectory. We should stop adding new features to X3's "support" directory right now.  
  
`char_set` components are NOT copied to the `x3/char_set/` directory. All components are actually implementation-detail char-related ones, which naturally fits inside the `x3/char/detail/` directory.

---

## Comment 1

> Username: saki7  
> Created_at: 2025-09-08 02:22:48 UTC  
> Url: https://github.com/boostorg/spirit/pull/817#issuecomment-3264357453  

@djowel I'd like to hear your opinion, do you have any objections on the decisions I noted above?

---

## Comment 2

> Username: djowel  
> Created_at: 2025-09-10 05:12:41 UTC  
> Url: https://github.com/boostorg/spirit/pull/817#issuecomment-3273344325  

> @djowel I'd like to hear your opinion, do you have any objections on the decisions I noted above?  
  
I read through this the other day. I have no objections. Making X3 self-contained is a good goal.

---

## Comment 3

> Username: saki7  
> Created_at: 2025-09-10 09:02:55 UTC  
> Url: https://github.com/boostorg/spirit/pull/817#issuecomment-3274010663  

> I read through this the other day. I have no objections. Making X3 self-contained is a good goal.  
  
Thanks for your understanding! I'll do my best to make the world better.

---
