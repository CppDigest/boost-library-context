# #166 - Multiprecision Construction from double produces random less-significant digits [Closed]

> Username: pabristow  
> Created at: 2019-11-14 15:52:50 UTC  
> Updated at: 2019-11-14 17:51:38 UTC  
> Closed at: 2019-11-14 17:51:38 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/166  

Multiprecision Construction from double works as expected, losing precision after about digit 17.  
  
This is inevitable because there is no information provided about the rest of the least significant bits.  
  
But this leads to  displays like this which will surprise some users  
  
`cpp_bin_float_quad y(0.1);    = 0.1000000000000000055511151231257827021`  
  
It is less easy to detect for a case like this  
  
`cpp_bin_float_quad pil(3.1415926535897932384626433832795028841L);   // OK construction from long double (suffix L).  
    std::cout << "cpp_bin_float_quad pil();    = " << pil << std::endl;  
    // cpp_bin_float_quad pil();    =3.141592653589793238512808959406186204  
    //                                              3.141592653589793238462643383279502884  
    //                                                            ^  differs at 20th decimal digit.  
`  
  
This is difficult to spot, whereas all zeros would be a big clue that something is not quite right.  
  
Would it be possible and/or desirable to change so that 'unknown' bits are zeros rather than random?  
  
  
PS Of course, the 'right thing' to do outputs this:  
  
cpp_bin_float_quad z("0.1");   = 0.1000000000000000000000000000000000048  
  
but one more often wants to construct from an existing double value.  
  
Or would it be possible get a warning that loss may/has occurred?  
  
It is also curious that values like integers, 0.5, 0.25 ... that can be exactly represented as double are correct displayed with no random decimal-digits.

---

## Comment 1

> Username: jzmaddock  
> Created at: 2019-11-14 17:51:38 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/166#issuecomment-554002398  

>Would it be possible and/or desirable to change so that 'unknown' bits are zeros rather than random?  
  
In short no.  To clarify:  
  
1) There is no loss of precision - construction from double copies all the bits exactly.  
2) There are no random bits - all the additional bits *are* zero.  Anything else would be a very serious bug.  
3) There are binary values which have no exact decimal representation (and vice versa), so as you up the precision, yes you will see "apparently" random decimal digits printed out on and on to infinity even though the binary representation is exact and only uses the first few bits (with the rest zero).  
  
>It is also curious that values like integers, 0.5, 0.25 ... that can be exactly represented as double are correct displayed with no random decimal-digits.  
  
Because they have exact binary->decimal and decimal->binary conversions, that's not true in general.
