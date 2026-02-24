# #116 - boost::posix_time doesn't have an extended iso string constructor [Closed]

> Username: lpatire  
> Created at: 2019-11-05 13:59:17 UTC  
> Updated at: 2020-07-09 15:10:22 UTC  
> Closed at: 2020-07-09 15:10:22 UTC  
> Url: https://github.com/boostorg/date_time/issues/116  

There is an output method:   
  
`std::string to_iso_extended_string(ptime)`  
  
but no compatible constructor from the extended ISO format.  
This could be a easy and simple idea to guarantee consistency between outwards and inwards methods.

---

## Comment 1

> Username: JeffGarland  
> Created at: 2019-11-21 16:23:56 UTC  
> Url: https://github.com/boostorg/date_time/issues/116#issuecomment-557161511  

This function already exists in later versions of boost past 1.54.  I believe the issue is that the documentation was not updated to match.  
  
// posix_time/time_parsers.hpp  
  inline ptime from_iso_extended_string(const std::string& s) {  
    return date_time::parse_delimited_time<ptime>(s, 'T');  
  }

---

## Comment 2

> Username: JeffGarland  
> Created at: 2020-04-05 18:22:42 UTC  
> Url: https://github.com/boostorg/date_time/issues/116#issuecomment-609460077  

This will be fixed in the 1.73 docs

---

## Comment 3

> Username: JeffGarland  
> Created at: 2020-07-09 15:10:22 UTC  
> Url: https://github.com/boostorg/date_time/issues/116#issuecomment-656185576  

closing -- doc update shipped with 1.73
