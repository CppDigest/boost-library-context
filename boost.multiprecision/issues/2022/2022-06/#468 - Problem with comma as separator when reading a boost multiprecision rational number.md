# #468 - Problem with comma as separator when reading a boost multiprecision rational number [Closed]

> Username: afabri  
> Created at: 2022-06-02 14:29:58 UTC  
> Updated at: 2022-06-05 11:38:18 UTC  
> Closed at: 2022-06-05 11:37:19 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/468  

When reading from a stream a rational number followed by a comma I get an error. This happens for the cpp as well as for the gmp backend.   I've put a gist [here](https://gist.github.com/afabri/b6543cce0a2da29219033bdce7719a43).      
  
When I put a blank before the comma it works (as the blank is then the delimiter".   I think the error is at [this line in number.hpp](https://github.com/boostorg/multiprecision/blob/develop/include/boost/multiprecision/number.hpp#L2322)

---

## Comment 1

> Username: afabri  
> Created at: 2022-06-03 08:53:33 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/468#issuecomment-1145747398  

If I understand the code correctly I would add one more `case` for the rational numbers that is basically the same as for the integer numbers but adding the "/" to the matching character set:  
```  
case boost::multiprecision::number_kind_rational:  
      if (oct_format)  
         s = detail::read_string_while(is, "+-01234567/");  
      else if (hex_format)  
         s = detail::read_string_while(is, "+-xXabcdefABCDEF0123456789/");  
      else  
         s = detail::read_string_while(is, "+-0123456789/");  
      break;  
```  
  
If that seems reasonable, I can make a pull request.

---

## Comment 2

> Username: jzmaddock  
> Created at: 2022-06-03 18:29:00 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/468#issuecomment-1146247759  

Yep, that's the correct fix for that I think, but complex and interval types probably have the same issue, and I also notice that we've only half-heartedly supported hexadecimal and octal IO for rational types so that should be fixed as well...  
  
I'll tackle all that shortly.

---

## Comment 3

> Username: jzmaddock  
> Created at: 2022-06-05 11:38:18 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/468#issuecomment-1146788711  

This should be fixed in develop now.  I'll deal with octal and hex notation rationals separately.
