# #74 Fix time_input_facet to be strict when matching input [Closed]

> Username: jeking3  
> Created at: 2018-01-19 23:04:19 UTC  
> Updated at: 2018-01-22 23:37:04 UTC  
> Closed at: 2018-01-22 23:37:04 UTC  
> Url: https://github.com/boostorg/date_time/pull/74  

Can be disabled by defining BOOST_DATE_TIME_NO_STRICT_PARSE.  I added this ability to disable because of the widespread use of this code and the fact it has been broken for so long in the field, folks may need the previous, more permissive parsing behavior.  
  
This fixes #53

---

## Comment 1

> Username: pdimov  
> Created_at: 2018-01-22 17:22:42 UTC  
> Url: https://github.com/boostorg/date_time/pull/74#issuecomment-359499061  

What is the motivation for this change?

---

## Comment 2

> Username: jeking3  
> Created_at: 2018-01-22 20:47:52 UTC  
> Url: https://github.com/boostorg/date_time/pull/74#issuecomment-359559393  

I ran across this issue when I was fixing other trac-identified issues from the backlog, so I added a github issue for it and decided to fix it.  I made it defeatable in case folks have been parsing strings incorrectly for the last 15 years.  We could just as easily change the documentation to say that any character in the input string that is not part of a format flag will be treated as a wildcard (because that's what the library has done).  
  
This brings it more in line with the strptime(3) behavior:  
  
```   
       The format argument is a character string that consists of field  
       descriptors and text characters, reminiscent of scanf(3).  Each field  
       descriptor consists of a % character followed by another character  
       that specifies the replacement for the field descriptor.  All other  
       characters in the format string must have a matching character in the  
       input string, except for whitespace, which matches zero or more  
       whitespace characters in the input string.  There should be white‐  
       space or other alphanumeric characters between any two field descrip‐  
       tors.  
```

---

## Comment 3

> Username: pdimov  
> Created_at: 2018-01-22 21:24:55 UTC  
> Url: https://github.com/boostorg/date_time/pull/74#issuecomment-359569891  

My question was more about whether this change has been requested by any actual users of the library.

---

## Comment 4

> Username: jeking3  
> Created_at: 2018-01-22 21:46:13 UTC  
> Url: https://github.com/boostorg/date_time/pull/74#issuecomment-359576535  

No, it has not.  It was just something I found along the way.  I won't merge it for now, if you think it's a step backwards... we could just as easily document the rules around processing the format string that format flags are processed and must be separated by at least one whitespace character; any other character matches as a wildcard.  That would be safer, but I don't think it matches the intention of having a format string to process...

---

## Comment 5

> Username: pdimov  
> Created_at: 2018-01-22 22:03:59 UTC  
> Url: https://github.com/boostorg/date_time/pull/74#issuecomment-359583352  

If nobody has complained for 15 years it's a safe bet that people find the current behavior either harmless or useful, and many may well have come to rely on it. Nobody likes having to change 15 year old working code. It's more effective to address actual pain points.  
  
A `#define` is not an adequate mechanism to control the strictness as DateTime is not header-only and is installed as part of Linux distros. You have to provide an actual runtime argument. (Which seems more user-friendly anyway.)

---

## Comment 6

> Username: jeking3  
> Created_at: 2018-01-22 23:37:02 UTC  
> Url: https://github.com/boostorg/date_time/pull/74#issuecomment-359611668  

I'm okay tossing this out; having to add an #ifdef was a pretty good sign.

---
