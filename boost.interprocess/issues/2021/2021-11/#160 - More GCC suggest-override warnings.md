# #160 - More GCC suggest-override warnings [Closed]

> Username: DonOregano  
> Created at: 2021-11-27 15:16:41 UTC  
> Updated at: 2021-12-26 23:27:19 UTC  
> Closed at: 2021-12-26 23:27:00 UTC  
> Url: https://github.com/boostorg/interprocess/issues/160  

I had -Werror=suggest-override set when I did this, so below are errors instead of warnings.  
  
```  
bufferstream.hpp:118:21: error: ‘boost::interprocess::basic_bufferbuf<CharT, CharTraits>::int_type boost::interprocess::basic_bufferbuf<CharT, CharTraits>::underflow() [with CharT = char; CharTraits = std::char_traits<char>; boost::interprocess::basic_bufferbuf<CharT, CharTraits>::int_type = int]’ can be marked override [-Werror=suggest-override]  
  118 |    virtual int_type underflow()  
bufferstream.hpp:125:21: error: ‘boost::interprocess::basic_bufferbuf<CharT, CharTraits>::int_type boost::interprocess::basic_bufferbuf<CharT, CharTraits>::pbackfail(boost::interprocess::basic_bufferbuf<CharT, CharTraits>::int_type) [with CharT = char; CharTraits = std::char_traits<char>; boost::interprocess::basic_bufferbuf<CharT, CharTraits>::int_type = int]’ can be marked override [-Werror=suggest-override]  
  125 |    virtual int_type pbackfail(int_type c = CharTraits::eof())  
bufferstream.hpp:150:21: error: ‘boost::interprocess::basic_bufferbuf<CharT, CharTraits>::int_type boost::interprocess::basic_bufferbuf<CharT, CharTraits>::overflow(boost::interprocess::basic_bufferbuf<CharT, CharTraits>::int_type) [with CharT = char; CharTraits = std::char_traits<char>; boost::interprocess::basic_bufferbuf<CharT, CharTraits>::int_type = int]’ can be marked override [-Werror=suggest-override]  
  150 |    virtual int_type overflow(int_type c = CharTraits::eof())  
bufferstream.hpp:182:21: error: ‘boost::interprocess::basic_bufferbuf<CharT, CharTraits>::pos_type boost::interprocess::basic_bufferbuf<CharT, CharTraits>::seekoff(boost::interprocess::basic_bufferbuf<CharT, CharTraits>::off_type, std::ios_base::seekdir, std::ios_base::openmode) [with CharT = char; CharTraits = std::char_traits<char>; boost::interprocess::basic_bufferbuf<CharT, CharTraits>::pos_type = std::fpos<__mbstate_t>; boost::interprocess::basic_bufferbuf<CharT, CharTraits>::off_type = long int; std::ios_base::seekdir = std::ios_base::seekdir; std::ios_base::openmode = std::ios_base::openmode]’ can be marked override [-Werror=suggest-override]  
  182 |    virtual pos_type seekoff(off_type off, std::ios_base::seekdir dir,  
bufferstream.hpp:248:21: error: ‘boost::interprocess::basic_bufferbuf<CharT, CharTraits>::pos_type boost::interprocess::basic_bufferbuf<CharT, CharTraits>::seekpos(boost::interprocess::basic_bufferbuf<CharT, CharTraits>::pos_type, std::ios_base::openmode) [with CharT = char; CharTraits = std::char_traits<char>; boost::interprocess::basic_bufferbuf<CharT, CharTraits>::pos_type = std::fpos<__mbstate_t>; std::ios_base::openmode = std::ios_base::openmode]’ can be marked override [-Werror=suggest-override]  
  248 |    virtual pos_type seekpos(pos_type pos, std::ios_base::openmode mode  
```

---

## Comment 1

> Username: igaztanaga  
> Created at: 2021-12-26 23:27:18 UTC  
> Url: https://github.com/boostorg/interprocess/issues/160#issuecomment-1001256179  

Thanks for the report!
