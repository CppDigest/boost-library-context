# #110 - to_iso_extended_string documentation wrong about fraction print [Open]

> Username: Superlokkus  
> Created at: 2019-10-10 12:44:23 UTC  
> Updated at: 2022-08-07 02:49:30 UTC  
> Url: https://github.com/boostorg/date_time/issues/110  

I am using `std::string to_iso_extended_string(ptime)` and have reports that on some system it prints without the second fractions. Consulting the documentation and the header of boost 1.71 I was confronted with inconsistent documentation:  
  
[The Docs are saying it prints with fractions of a second:](https://www.boost.org/doc/libs/1_71_0/doc/html/date_time/posix_time.html)  
  
> std::string to_iso_extended_string(ptime)  
> Convert to form YYYY-MM-DDTHH:MM:SS,fffffffff where T is the date-time separator  
> 2002-01-31T10:00:01,123456789  
  
[While the accoding header says:](https://github.com/boostorg/date_time/blob/boost-1.71.0/include/boost/date_time/posix_time/time_formatters.hpp#L240)  
  
> //! Convert to form YYYY-MM-DDTHH:MM:SS where T is the date-time separator  
>   /*!\ingroup time_format  
>    */  
>   inline std::string to_iso_extended_string(ptime t){  
  
So YYYY-MM-DDTHH:MM:SS,fffffffff  or YYYY-MM-DDTHH:MM:SS, who is right?

---

## Comment 1

> Username: JeffGarland  
> Created at: 2019-10-10 12:57:47 UTC  
> Url: https://github.com/boostorg/date_time/issues/110#issuecomment-540564143  

The docs are ‘right’, but incomplete. If the fractional seconds are 0 then it is omitted - that’s probably the report your receiving. I have a version if the function around somewhere that consistently prints the fractional second even when zero.  
  
> On Oct 10, 2019, at 5:44 AM, Markus Klemm <notifications@github.com> wrote:  
>   
> I am using std::string to_iso_extended_string(ptime) and have reports that on some system it prints without the second fractions. Consulting the documentation and the header of boost 1.71 I was confronted with inconsistent documentation:  
>   
> The Docs are saying it prints with fractions of a second:  
>   
> std::string to_iso_extended_string(ptime)  
> Convert to form YYYY-MM-DDTHH:MM:SS,fffffffff where T is the date-time separator  
> 2002-01-31T10:00:01,123456789  
>   
> While the accoding header says:  
>   
> //! Convert to form YYYY-MM-DDTHH:MM:SS where T is the date-time separator  
> /*!\ingroup time_format  
> */  
> inline std::string to_iso_extended_string(ptime t){  
>   
> So YYYY-MM-DDTHH:MM:SS,fffffffff or YYYY-MM-DDTHH:MM:SS, who is right?  
>   
> —  
> You are receiving this because you are subscribed to this thread.  
> Reply to this email directly, view it on GitHub, or unsubscribe.

---

## Comment 2

> Username: Superlokkus  
> Created at: 2019-10-10 13:15:48 UTC  
> Url: https://github.com/boostorg/date_time/issues/110#issuecomment-540574246  

I just looked into the code, it seems except for some obscure workaround for `_MSC_VER < 1300` the format really is either with fractions when non zero or without fractions otherwise.  
  
However this should be documentation in the header AND the HTML documentation, since its neither "Always without fractions" nor "Always with fractions" but "With fraction if fraction is not zero, otherwise without", which booth should be corrected.

---

## Comment 3

> Username: Superlokkus  
> Created at: 2019-10-10 14:00:08 UTC  
> Url: https://github.com/boostorg/date_time/issues/110#issuecomment-540599512  

> I have a version if the function around somewhere that consistently prints the fractional second even when zero.  
  
Don't need that, since it really implies non existent precision. The undocumented format behavior has its cause in the field because of some platforms not supplying microsecond precision:   
  
[ptime microsec_clock::universal_time()](https://www.boost.org/doc/libs/1_71_0/doc/html/date_time/posix_time.html) :  
  
> Win32 systems often do not achieve microsecond resolution via this API. If higher resolution is critical to your application test your platform to see the achieved resolution.  
  
One might refer to that when documenting this fraction second format switch.

---

## Comment 4

> Username: lukem  
> Created at: 2022-08-07 02:46:14 UTC  
> Url: https://github.com/boostorg/date_time/issues/110#issuecomment-1207319425  

I've fixed the documentation issue along with other ISO 8601-related documentation fixes, and created a pull request.

---

## Comment 5

> Username: lukem  
> Created at: 2022-08-07 02:49:30 UTC  
> Url: https://github.com/boostorg/date_time/issues/110#issuecomment-1207319687  

>  I have a version if the function around somewhere that consistently prints the fractional second even when zero.  
  
I think this function would be useful.  
  
I've rolled my own because I had a requirement to always provide the fractional seconds even if they were 0 (in my case, to microsecond resolution, which matched the time source). This isn't adding "non existent precision" if the micro-second resolution time source happens to provide 0 microseconds when appropriate.
