# #60 - Enhancement: format (u)int8 types correctly to number, if formatted with %d, &i, %u [Open]

> Username: s-martin  
> Created at: 2018-10-05 15:25:18 UTC  
> Updated at: 2022-06-15 15:49:37 UTC  
> Url: https://github.com/boostorg/format/issues/60  

If I try to format a (u)int8 number, Boost.Format considers this as (unsigned) char, even if I use `%d`, `%i` or `%u`.  
  
[This is a known and rather old issue](http://boost.2283326.n4.nabble.com/format-show-uint8-t-as-a-number-not-a-char-td2563278.html).  
  
The workaround is to cast the number to (u)int32, before formatting.  
  
Nevertheless the current behavior leads to very subtle bugs, because the developer has to check the type of the number to be printed very carefully.  
  
Plain `printf` handles (u)int8 numbers in combination with `%d`, `%i` or `%u` correctly.  
  
  
Boost version: 1.68 (32bit)  
Compiler: MSVC 15.8

---

## Comment 1

> Username: k15tfu  
> Created at: 2019-12-03 20:58:23 UTC  
> Updated at: 2019-12-03 20:58:57 UTC  
> Url: https://github.com/boostorg/format/issues/60#issuecomment-561354014  

Just faced with the same issue trying to format GUID to its string representation. I use `%02X` to format the last bytes, but unexpectedly it was shown as non-US-ASCII character =(.  
  
It there any plans to fix this? cc @jeking3

---

## Comment 2

> Username: maddanio  
> Created at: 2022-06-15 15:49:36 UTC  
> Url: https://github.com/boostorg/format/issues/60#issuecomment-1156642566  

I don't think this is an enhancement. This just bit me and imho it's a plain bug: i specifically ask for some kind of numeric formatting and it simply doesnt. If I specify '%x' and the argument is a string it should just throw.
