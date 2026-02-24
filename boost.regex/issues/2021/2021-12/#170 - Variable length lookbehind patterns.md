# #170 - Variable length lookbehind patterns [Closed]

> Username: bikky9  
> Created at: 2021-12-01 08:35:28 UTC  
> Updated at: 2022-10-03 16:12:44 UTC  
> Closed at: 2022-10-03 16:12:44 UTC  
> Url: https://github.com/boostorg/regex/issues/170  

I am consuming boost library for regex matching. Recently we have upgraded boost from version 1.37 to 1.65 and then suddenly the regexes having patterns like (?<=^|\s|_), (?<!\d{3}|\d{4}) are failing to compile. Most of the failing regexes are having variable length lookbehind patterns  
  
I see from documentation that lookbehind regexes should have fixed length pattern and that should be why the above regexes are failing to compile. I am wondering why they used to work in 1.37 and not now? Is there any optimization that pushed to remove variable length regex patterns?

---

## Comment 1

> Username: redactedscribe  
> Created at: 2022-01-10 15:51:40 UTC  
> Url: https://github.com/boostorg/regex/issues/170#issuecomment-1009015641  

I'm attempting to use `(?<=^|\s)` in [Mp3tag](https://www.mp3tag.de/en/) (which [credits boost::regex](https://help.mp3tag.de/misc_credits.html)) but Mp3tag is returning "Invalid lookbehind assertion encountered in the regular expression.".  
  
The workaround I've been using (which I believe is equivalent in result but not efficiency) is `((?<=^)|(?<=\s))`.  
  
Testing the former expression via [regex101](https://regex101.com/)'s PCRE2 (PHP >=7.3) flavour shows that it is valid, which is why I think the issue is within Boost. Both regex101 and Mp3tag accept the latter just fine.  
  
\- Boost 1.75.0? (this is the version of the Boost docs that Mp3tag's credits link to, but the actual version in use may differ)  
\- Mp3tag 3.11  
  
Thanks.

---

## Comment 2

> Username: jzmaddock  
> Created at: 2022-01-11 19:19:29 UTC  
> Url: https://github.com/boostorg/regex/issues/170#issuecomment-1010283952  

We have never supported variable-length-lookbehind, but I'll keep it in mind for future enhancements.

---

## Comment 3

> Username: JohnAlt2  
> Created at: 2022-09-28 14:30:34 UTC  
> Url: https://github.com/boostorg/regex/issues/170#issuecomment-1261002815  

I would imagine that more efficient than "**((?<=^)|(?<=\s))**"would be "**(^|(?<=\s))**" or even better supress the capture and use "**(?:^|(?<=\s))**".  
As "^" operator does not refer to a previous character you do not need to look behind, it is in effect querying a quality of the current parse location.

---

## Comment 4

> Username: JohnAlt2  
> Created at: 2022-10-03 09:00:39 UTC  
> Url: https://github.com/boostorg/regex/issues/170#issuecomment-1265140574  

Suggest CLOSE TICKET as all questions here appear to be answered.

---

## Comment 5

> Username: jzmaddock  
> Created at: 2022-10-03 16:12:43 UTC  
> Url: https://github.com/boostorg/regex/issues/170#issuecomment-1265698371  

Agreed, it's not easy to implement, and I don't have time at present...
