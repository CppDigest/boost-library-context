# #230 - Add a way to create a time zone that is set on the system [Open]

> Username: Lastique  
> Created at: 2023-06-07 16:59:11 UTC  
> Updated at: 2023-07-02 11:26:55 UTC  
> Url: https://github.com/boostorg/date_time/issues/230  

Please, provide a way to create a time zone object (presumably, `posix_time_zone`) that corresponds to whatever time zone is set on the current system. Essentially, I need a way to produce formatted date/time strings that include the current time zone information.  
  
This has been requested [here](https://github.com/boostorg/log/issues/210).

---

## Comment 1

> Username: Lastique  
> Created at: 2023-07-02 11:26:55 UTC  
> Url: https://github.com/boostorg/date_time/issues/230#issuecomment-1616610578  

One possible way to do this is, I think, to use `strftime` with %z or %Z to obtain zone information and then parse it back into Boost.DateTime's `posix_time_zone`. This doesn't provide DST information, though.
