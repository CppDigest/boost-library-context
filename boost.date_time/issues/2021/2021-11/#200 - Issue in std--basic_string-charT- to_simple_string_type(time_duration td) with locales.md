# #200 - Issue in std::basic_string<charT> to_simple_string_type(time_duration td) with locales [Open]

> Username: jalegido  
> Created at: 2021-11-29 07:52:59 UTC  
> Updated at: 2021-11-29 16:03:09 UTC  
> Url: https://github.com/boostorg/date_time/issues/200  

There is a problem std::basic_string<charT> to_simple_string_type(time_duration td) if application doesn't use the locale("C").  
  
If you use spanish locale the result of this function is like this one:  
2020-11-03T09:26:04.966.395  
where the second 'dot' is wrong because is the miles signal in spanish locale.  
  
I propose to correct it, use next code:  
```  
std::basic_ostringstream<charT> ss;  
ss.imbue(std::locale::classic());  
  
```   
  
Best regards

---

## Comment 1

> Username: JeffGarland  
> Created at: 2021-11-29 12:57:10 UTC  
> Url: https://github.com/boostorg/date_time/issues/200#issuecomment-981609292  

to_simple_string ignores locales, so it's not really the right tool for the job.  Have a look at the following:  
  
https://www.boost.org/doc/libs/1_77_0/doc/html/date_time/date_time_io.html#date_time.time_input_facet  
  
https://github.com/boostorg/date_time/blob/develop/test/posix_time/testtime_facet.cpp  
https://github.com/boostorg/date_time/blob/develop/example/tutorial/io_tutorial.cpp

---

## Comment 2

> Username: jalegido  
> Created at: 2021-11-29 13:09:52 UTC  
> Url: https://github.com/boostorg/date_time/issues/200#issuecomment-981619602  

Yes, I know the others options to do this job. But, as boost::posix_time::to_iso_extended_wstring exits and is publicity accesible; I would like to use it.  
But to_iso_extended_wstring has this locale problem, when no classic locale is set.

---

## Comment 3

> Username: JeffGarland  
> Created at: 2021-11-29 14:17:57 UTC  
> Url: https://github.com/boostorg/date_time/issues/200#issuecomment-981676729  

From the initial problem report it seemed like it wasn't just wstring with the problem. And for sure the code here has no dependency on locales -- in particular the global one -- which is a property I'd like to keep.  If we wanted a locale based solution it should be an overload.

---

## Comment 4

> Username: jalegido  
> Created at: 2021-11-29 14:31:11 UTC  
> Url: https://github.com/boostorg/date_time/issues/200#issuecomment-981688482  

I think these public functions should not dependencies on locales. But as ISO is always in classic locale, I think it should be good to set imbue(std::locale::classic()) in the function to_simple_string_type to solve future problems with parse ISO string in other applications or languages.

---

## Comment 5

> Username: JeffGarland  
> Created at: 2021-11-29 15:04:07 UTC  
> Url: https://github.com/boostorg/date_time/issues/200#issuecomment-981718623  

The classic locale doesn't have anything to do with ISO time representations -- as far as I know, you'll never get ISO out of time_put/get without forcing the format. The only thing that could be used there are the punctuation chars, which is the original issue you're having.  So maybe if there was a tweak we could make it would be that the separators be a defaulted parameter to the function.    
  
https://en.cppreference.com/w/cpp/locale/numpunct

---

## Comment 6

> Username: jalegido  
> Created at: 2021-11-29 15:22:44 UTC  
> Url: https://github.com/boostorg/date_time/issues/200#issuecomment-981737944  

Ok, that should be enough.

---

## Comment 7

> Username: jalegido  
> Created at: 2021-11-29 16:03:09 UTC  
> Url: https://github.com/boostorg/date_time/issues/200#issuecomment-981775292  

I would like that next code will generate correct output:  
```  
#include <iostream>  
#include <boost/date_time.hpp>  
  
int main()  
{  
    std::locale::global(std::locale("es_ES"));  
    boost::posix_time::ptime dt(boost::gregorian::date(2021, 11, 29), boost::posix_time::time_duration(10, 11, 12, 123456));  
    std::wcout << boost::posix_time::to_iso_extended_wstring(dt) << std::endl;  
  
    std::locale::global(std::locale("ar_EG"));  
    std::wcout << boost::posix_time::to_iso_extended_wstring(dt) << std::endl;  
}  
```  
Current output MSVC 16.11.7  
2021-11-29T10:11:12.123.456  
2021-11-29T10:11:12.123,456  
  
Expected output:  
2021-11-29T10:11:12.123456  
2021-11-29T10:11:12.123456
