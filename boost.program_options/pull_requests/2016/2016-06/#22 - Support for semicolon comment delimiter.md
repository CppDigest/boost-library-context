# #22 Support for semicolon comment delimiter [Open]

> Username: teeks99  
> Created at: 2016-06-04 13:25:57 UTC  
> Updated at: 2021-08-29 16:58:05 UTC  
> Url: https://github.com/boostorg/program_options/pull/22  

INI style configuration files should support the semicolon ';' character, when  
it is the first character on the line only, as a comment delimiter. Because  
the '#' style comments are not allowed in strict MS INI files, this gives an  
opening for files to be compatible in both and still have comments.

---

## Comment 1

> Username: teeks99  
> Created_at: 2016-06-04 13:33:26 UTC  
> Url: https://github.com/boostorg/program_options/pull/22#issuecomment-223755927  

This could, in theory, cause errors in existing files...if there are valid lines/values that start with semi-colons. I suspect there are very close to zero instances where this is the case, because that would be a weird thing to do. More realistically, there are people who have files with what they think are comments, but the lines are actually being processed.  
  
Either way, if we don't want to change the file format in this minor way, we can add another defaulted parameter (defaulting it off) to the parse_config_file interface.

---

## Comment 2

> Username: jeking3  
> Created_at: 2018-03-06 14:22:26 UTC  
> Url: https://github.com/boostorg/program_options/pull/22#issuecomment-370796578  

This seems reasonable to me.

---

## Review 3 [Changes requested]

> Username: eyalroz  
> Created_at: 2018-05-14 15:28:34 UTC  
> State: CHANGES_REQUESTED  
> Url: https://github.com/boostorg/program_options/pull/22#pullrequestreview-119886617  

1. Why check for a semicolon before, rather than after, trimming whitespace? If we support `#` comments (which are not integral to the INI format AFAICR), and if we trim whitespace before really processing a line, let's also consider `   ;blah blah blah` to be a comment.  
2. The original code uses `*s.begin()` instead of `*s.at(0)`, I suggest maintaining uniformity in this respect.  
3. Given the current choice of when to check for the semicolon, why go through whitespace trimming, instead of continuing to the next loop iteration immediately?  
  
The code I'd put in here would be:  
```  
s = trim_ws(s);  
if (!s.empty()) {  
    if (*s.begin() == ';') {   
        continue; // an INI-style comment line  
    }  
    /* etc. etc. */  
```

---

## Comment 4

> Username: jjELT  
> Created_at: 2021-03-24 08:21:26 UTC  
> Url: https://github.com/boostorg/program_options/pull/22#issuecomment-805601621  

This has been fixed 4 years ago. Why is this not yet merged into the latest Boost release?

---

## Comment 5

> Username: vprus  
> Created_at: 2021-03-24 11:54:17 UTC  
> Url: https://github.com/boostorg/program_options/pull/22#issuecomment-805760739  

There are outstanding questions above, as you can see.

---

## Comment 6

> Username: eyalroz  
> Created_at: 2021-03-24 12:34:55 UTC  
> Url: https://github.com/boostorg/program_options/pull/22#issuecomment-805784510  

@jjELT : You could "adopt" this PR by answering the questions, and possibly creating a new PR after they've been resolved.  
  
But just to clarify - I don't hold any authority here, it's @vprus' repository... I just commented since I was more interested in PRs here back in 2018.

---

## Comment 7

> Username: jjELT  
> Created_at: 2021-03-24 14:16:12 UTC  
> Url: https://github.com/boostorg/program_options/pull/22#issuecomment-805858128  

Ok, let me see if I understood correctly the open issues.  
  
1.  > This could, in theory, cause errors in existing files...if there are valid lines/values that start with semi-colons. [...]  
2.  > [...] if we don't want to change the file format in this minor way, we can add another defaulted parameter (defaulting it off) to the parse_config_file interface.  
  
According to [Wiki:](https://en.wikipedia.org/wiki/INI_file#Comments)   
> Semicolons (;) at the beginning of the line indicate a comment.  
  
So it would actually be wrong to make sure a line starting with a semicolon would be interpreted as a valid setting.  
It is therefore not necessary to add an option, to enable semicolons to be interpreted as a comment. It **must** be interpreted as a comment.  
  
If you read on in the Wiki article, you notice it would make much more sense to optionally interpret the number sign as a comment instead, since by default it should only be interpreted as a _pseudo line comment character_, hiding a setting by changing it's name.  
Since the effect is the same, and `boost_options` would throw an `Unknown setting '#my-hidden-setting' ` (or similar, unless unkown settings are allowed of course), I would also leave the number sign to be interpreted as a comment.  
  
On [stackoverflow.com there is a related thread concerning this topic.](https://stackoverflow.com/questions/1378219/do-standard-windows-ini-files-allow-comments)  
  
The current implementation  
```  
// strip '#' comments and whitespace  
if ((n = s.find('#')) != string::npos)  
	s = s.substr(0, n);  
// if the first character is a ';' line is a comment  
if (!s.empty() && ';' == s.at(0))  
	s = "";  
s = trim_ws(s);  
```  
should be just fine.  
  
Am I missing something?

---

## Comment 8

> Username: eyalroz  
> Created_at: 2021-03-24 18:54:56 UTC  
> Url: https://github.com/boostorg/program_options/pull/22#issuecomment-806074949  

> Am I missing something?  
  
My questions.

---

## Comment 9

> Username: teeks99  
> Created_at: 2021-03-25 00:01:22 UTC  
> Url: https://github.com/boostorg/program_options/pull/22#issuecomment-806259984  

The documentation I've found says something along the line of "semicolon at the **start** of the line". I feel like trimming white space before it could potentially cause issues. Though the only use case I can think of is where someone wants a single semicolon as the contents of a value....not super useful, but I want to be careful as people might be depending on weird side effects of semicolons not working as a comment other places in the line.  
  
Point taken on `s.begin()` instead of `s.at(0)`.  
  
If we don't change it to remove the white space before checking for a comment line, there's no harm in doing it after, as the empty string won't have any.

---

## Comment 10

> Username: eyalroz  
> Created_at: 2021-03-26 10:28:55 UTC  
> Url: https://github.com/boostorg/program_options/pull/22#issuecomment-808103736  

Well,  emphasizing again that it's Vladimir's call, not mine. But:  
  
> The documentation I've found says something along the line of "semicolon at the start of the line"  
  
Well, there's no official spec, so ok I guess.  
  
> If we don't change it to remove the white space before checking for a comment line, there's no harm in doing it after, as the empty string won't have any.  
  
... but it's useless work.

---

## Comment 11

> Username: teeks99  
> Created_at: 2021-03-27 14:39:55 UTC  
> Url: https://github.com/boostorg/program_options/pull/22#issuecomment-808743192  

@eyalroz I've updated, does that look good to you?

---

## Review 12 [Commented]

> Username: eyalroz  
> Created_at: 2021-03-28 18:07:48 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/program_options/pull/22#pullrequestreview-622768976  

📁 src/config_file.cpp

```diff
  93 |+             // if the first character is a ';' line is a comment
  94 |+             if (!s.empty() && ';' == *s.begin()) {
  95 |+                 s = "";
```

> Username: eyalroz  
> Created_at: 2021-03-28 18:07:48 UTC  
> Updated_at: 2021-03-31 03:06:26 UTC  
> Url: https://github.com/boostorg/program_options/pull/22#discussion_r602911328  

Can't you just do `continue;` here?


---

## Review 13 [Commented]

> Username: eyalroz  
> Created_at: 2021-03-28 18:10:43 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/program_options/pull/22#pullrequestreview-622769185  

📁 test/parsers_test.cpp

```diff
 268 |     const char content1[] =
 269 |     " gv1 = 0#asd\n"
 270 |+     "; semi comment\n"
```

> Username: eyalroz  
> Created_at: 2021-03-28 18:10:43 UTC  
> Updated_at: 2021-03-31 03:06:26 UTC  
> Url: https://github.com/boostorg/program_options/pull/22#discussion_r602911685  

A also want to see a commented-out line which has an assignment, and a check to ensure the value is not assigned.


---

## Comment 14

> Username: eyalroz  
> Created_at: 2021-03-28 18:15:09 UTC  
> Url: https://github.com/boostorg/program_options/pull/22#issuecomment-808935729  

> @eyalroz I've updated, does that look good to you?  
  
Other than a couple of nitpicks, yes. But again - you're asking the wrong person :-)

---

## Comment 15

> Username: jjELT  
> Created_at: 2021-04-26 13:19:53 UTC  
> Url: https://github.com/boostorg/program_options/pull/22#issuecomment-826829520  

I am happy to see there is some more activity in this thread.   
  
Unfortunately `continuous-integration/appveyor/pr — AppVeyor build failed`. Since all other tests passed I am wondering whether AppVeyor really fails because of the changes of this pull request, or is it simply configured wrong?!  
  
@vprus: Do you have any more doubts and suggestions or is this code ready to be merged?

---

## Comment 16

> Username: teeks99  
> Created_at: 2021-08-29 14:14:52 UTC  
> Url: https://github.com/boostorg/program_options/pull/22#issuecomment-907798674  

@eyalroz @vprus I just wanted to ping this thread and see if there were any outstanding issues preventing it from merging?   
  
The appveyor failure above is also present on develop, not introduced in this pull request.

---

## Comment 17

> Username: eyalroz  
> Created_at: 2021-08-29 16:58:05 UTC  
> Url: https://github.com/boostorg/program_options/pull/22#issuecomment-907827348  

@teeks99 : Again, I have no say here, it's @vprus 's repo.

---
