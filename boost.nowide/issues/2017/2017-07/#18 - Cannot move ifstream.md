# #18 - Cannot move ifstream [Closed]

> Username: egorpugin  
> Created at: 2017-07-12 20:57:27 UTC  
> Updated at: 2020-03-03 14:52:20 UTC  
> Closed at: 2020-03-03 14:52:20 UTC  
> Url: https://github.com/boostorg/nowide/issues/18  

Hi!  
  
I'm playing with nowide and tried to replace `std::ifstream` in my code with nowide one.  
But move operator is deleted. (VS2017 - v15.2 - the latest).  
```  
d->ifile = std::move(boost::nowide::ifstream(f.string(), std::ifstream::binary));  
```  
  
```  
error C2280: 'boost::nowide::basic_ifstream<char,std::char_traits<char>> &boost::nowide::basic_ifstream<char,std::char_traits<char>>::operator =(const boost::nowide::basic_ifstream<char,std::char_traits<char>> &)': attempting to reference a deleted function  
```  
  
VS points me to `fstream.hpp:109`:  
```  
private:  
        boost::scoped_ptr<internal_buffer_type> buf_;  
```  
  
I workarounded it with `.open()`, but still...  
Is this actually a bug or a feature?

---

## Comment 1

> Username: artyom-beilis  
> Created at: 2017-07-12 21:16:52 UTC  
> Url: https://github.com/boostorg/nowide/issues/18#issuecomment-314899659  

Currently it does not support C++11 move constructor, will be addressed upon inclusion to boost

---

## Comment 2

> Username: egorpugin  
> Created at: 2017-07-13 23:56:25 UTC  
> Updated at: 2017-07-13 23:56:45 UTC  
> Url: https://github.com/boostorg/nowide/issues/18#issuecomment-315232979  

Do you plan to add wide streams (e.g. `boost::nowide::wofstream`) also?  
Because now nowide don't have it and when I write  
```  
typedef boost::nowide::basic_ofstream<wchar_t> wofstream;  
wofstream ofile(p.string(), std::ios::out | std::ios::binary);  
```  
compiler says there's no `boost::nowide::basic_filebuf<CharType,Traits>` with wchar_t specialization.  
Indeed there's only `char` one.

---

## Comment 3

> Username: artyom-beilis  
> Created at: 2017-07-14 06:00:33 UTC  
> Url: https://github.com/boostorg/nowide/issues/18#issuecomment-315277730  

No I don't plan to add wfstream , since the library is nowide .
