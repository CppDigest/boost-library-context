# #102 - Inconsistent '%f' behaviour between boost::posix_time::time_facet and boost::posix_time::time_input_facet [Open]

> Username: jpo38  
> Created at: 2019-05-02 06:31:15 UTC  
> Updated at: 2019-05-03 16:40:47 UTC  
> Url: https://github.com/boostorg/date_time/issues/102  

I'm trying to convert a boost::posix_time::ptime to a specific string format (extended ISO) and then back to a boost::posix_time::ptime.  
  
Surprisingly, it looks like for boost::posix_time::time_facet %f means 000000 to 999999 (with no decimal separator). But for boost::posix_time::time_input_facet, it means .000000 to .999999 (with decimal separator).  
  
    int main( int argc, char** argv )  
    {  
        auto now = boost::posix_time::second_clock::local_time();  
  
        std::stringstream outStr;  
        {  
            boost::posix_time::time_facet* facet = new boost::posix_time::time_facet();  
            facet->format("%Y-%m-%dT%H:%M:%S.%f");  
            outStr.imbue(std::locale(std::locale::classic(), facet));  
            outStr << now;  
        }  
        std::cout << outStr.str() << std::endl;  
  
        {  
            static const std::string format = "%Y-%m-%dT%H:%M:%S.%f";  
            const std::locale loc = std::locale(std::locale::classic(), new boost::posix_time::time_input_facet(format));  
            std::istringstream is(outStr.str());  
            is.imbue(loc);  
            boost::posix_time::ptime converted;  
            is >> converted;  
            std::cout << converted << std::endl;  
        }  
  
        {  
            static const std::string format = "%Y-%m-%dT%H:%M:%S%f";  
            const std::locale loc = std::locale(std::locale::classic(), new boost::posix_time::time_input_facet(format));  
            std::istringstream is(outStr.str());  
            is.imbue(loc);  
            boost::posix_time::ptime converted;  
            is >> converted;  
            std::cout << converted << std::endl;  
        }  
  
        return 0;  
    }  
  
This outputs:  
  
2019-04-30T12:23:29.000000  
not-a-date-time  
2019-Apr-30 12:23:29  
  
While I would expect:  
  
2019-04-30T12:23:29.000000  
2019-Apr-30 12:23:29  
not-a-date-time  
  
Note that using "%Y-%m-%dT%H:%M:%S%F" works just fine (but has different behaviour when time is not a decimal value)

---

## Comment 1

> Username: jeking3  
> Created at: 2019-05-02 16:13:10 UTC  
> Url: https://github.com/boostorg/date_time/issues/102#issuecomment-488735204  

Thanks for reporting it with an example!

---

## Comment 2

> Username: JeffGarland  
> Created at: 2019-05-02 16:23:45 UTC  
> Url: https://github.com/boostorg/date_time/issues/102#issuecomment-488738720  

yes, that’s not good. I’m thinking though that the to_iso_extended functions are going to be more efficient...  
  
> On May 2, 2019, at 9:13 AM, James E. King III <notifications@github.com> wrote:  
>   
> Thanks for reporting it with an example!  
>   
> —  
> You are receiving this because you are subscribed to this thread.  
> Reply to this email directly, view it on GitHub <https://github.com/boostorg/date_time/issues/102#issuecomment-488735204>, or mute the thread <https://github.com/notifications/unsubscribe-auth/ABBY5DVYASRASW5QBOGNOCTPTMHJNANCNFSM4HJ3WSTA>.  
>

---

## Comment 3

> Username: jpo38  
> Created at: 2019-05-02 19:49:02 UTC  
> Url: https://github.com/boostorg/date_time/issues/102#issuecomment-488807678  

@JeffGarland: Sure, but as there is no from_iso_extended one must create the pattern itself....

---

## Comment 4

> Username: JeffGarland  
> Created at: 2019-05-02 21:37:07 UTC  
> Url: https://github.com/boostorg/date_time/issues/102#issuecomment-488841391  

@jpo38  
  
Actually it was added awhile ago — but I see the docs never caught up :( In date_time/posix_time/time_parsers.hpp you’ll find :  
  
 inline ptime from_iso_extended_string(const std::string& s) {  
    return date_time::parse_delimited_time<ptime>(s, 'T');  
  }  
  
I didn’t confirm by looking at test cases that it’s handling all these fractional cases correctly.  In my codebase I have a different version that I use because I have iso times that have space delimiters between date and time.  If the one above doesn’t work I can drop my other one…  
  
Jeff  
  
> On May 2, 2019, at 12:49 PM, jpo38 <notifications@github.com> wrote:  
>   
> @JeffGarland <https://github.com/JeffGarland>: Sure, but as there is no from_iso_extended one must create the pattern itself....  
>   
> —  
> You are receiving this because you were mentioned.  
> Reply to this email directly, view it on GitHub <https://github.com/boostorg/date_time/issues/102#issuecomment-488807678>, or mute the thread <https://github.com/notifications/unsubscribe-auth/ABBY5DTSJB3RWR4QQQI3A7TPTNAS7ANCNFSM4HJ3WSTA>.  
>

---

## Comment 5

> Username: jpo38  
> Created at: 2019-05-03 15:44:00 UTC  
> Url: https://github.com/boostorg/date_time/issues/102#issuecomment-489141205  

You are right, from_iso_extended_string works like a charm! Would be better if it was part of the doc ;-)  
  
Anyway, %f is still inconsistent between time_facet and time_input_facet!  
  
Thanks for the from_iso_extended_string tip, it makes things easier for me!  
  
Jean

---

## Comment 6

> Username: JeffGarland  
> Created at: 2019-05-03 15:59:47 UTC  
> Url: https://github.com/boostorg/date_time/issues/102#issuecomment-489148413  

jpo38 > You are right, from_iso_extended_string works like a charm! Would be better if it was part of the doc ;-)  
  
We never turn down a pull request :) Or at a minimum we should make an issue so we don’t forget to fix — not helping that its just in my brain...  
  
Jeff

---

## Comment 7

> Username: jeking3  
> Created at: 2019-05-03 16:30:16 UTC  
> Url: https://github.com/boostorg/date_time/issues/102#issuecomment-489158212  

This is the issue thread. :)

---

## Comment 8

> Username: JeffGarland  
> Created at: 2019-05-03 16:40:47 UTC  
> Url: https://github.com/boostorg/date_time/issues/102#issuecomment-489161284  

Perhaps — I guess it’s a question of if we’re going to do something with the original problem in this issue.  The docs seem only peripherally related ‘discovery’...  
  
> On May 3, 2019, at 9:30 AM, James E. King III <notifications@github.com> wrote:  
>   
> This is the issue thread. :)  
>   
> —  
> You are receiving this because you were mentioned.  
> Reply to this email directly, view it on GitHub <https://github.com/boostorg/date_time/issues/102#issuecomment-489158212>, or mute the thread <https://github.com/notifications/unsubscribe-auth/ABBY5DSBMHV3HDVE4FGPQ33PTRSBTANCNFSM4HJ3WSTA>.  
>
