# #245 Update timezone data file to IANA 2025b [Open]

> Username: jeking3  
> Created at: 2025-07-07 11:24:16 UTC  
> Updated at: 2025-07-07 11:24:33 UTC  
> Url: https://github.com/boostorg/date_time/pull/245  

Ingested tooling from https://github.com/fmidev/smartmet-timezones (which uses the unlicence licence) and updated timezone data README so we have a means to regenerate the file that is documented.  
  
Compared to IANA 2016c, the timezone information in 2025c no longer has a distinction between short and long names.  For example, in 2016c America/Denver used MST for short, Mountain Standard Time for long but America/Boise used MST for both.  This discrepancy was only present in a few time zones.  With 2025c they both use MST for short and long, which also means that the built-in "Coordinated Universal Time" long form is now displayed as "UTC" like the short form.  Now this could be considered a breaking change, so if there are concerns that we should maintain what was there before with manual fixups, let's discuss.  If we merge this for a release it should come with a release note indicating long form time zone names now match short form in all cases.  
  
While this does not fully resolve #67, it does help us maintain the time zone data file better.  It would be much better to have the library use the system timezone data through standard library APIs than for us to provide one.

---
