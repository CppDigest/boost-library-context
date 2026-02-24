# #225 - [Feature Request] Boost.regex - allow OR operator in look-behind [Open]

> Username: Palloxin  
> Created at: 2024-09-12 11:04:45 UTC  
> Updated at: 2025-11-26 23:30:02 UTC  
> Url: https://github.com/boostorg/regex/issues/225  

### Description  
  
As the title says.  
  
The lib doesnt accept OR operator in lookbehind.  
Example:  
```js  
/\w+(?<=Sublime|Bob)/  
```  
doesnt work.  
  
BUT it accepts this:  
```js  
/\w+(?<=Sublime)(?<=Bob)/  
```  
  
I would like the lib to support the OR operation for the regex look-behind  
### Other Details  
These don't work too:  
```js  
/\w+(?<=(?:Sublime|Bob))/  
```  
```js  
/\w+(?<=\s?Sublime)/  
```

---

## Comment 1

> Username: JohnAlt2  
> Created at: 2024-10-30 20:26:29 UTC  
> Updated at: 2024-11-02 23:38:22 UTC  
> Url: https://github.com/boostorg/regex/issues/225#issuecomment-2448304107  

ORs are accepted in lookbehinds so long as each branch  is of the same length e.g.  
```perl  
/\w+(?<=Dave|John|.Bob)/  
```   
  
look-behinds needing to be fixed length is a known feature as documented e.g.: https://www.boost.org/doc/libs/1_86_0/libs/regex/doc/html/boost_regex/syntax/perl_syntax.html  
  
_[Lookbehind](https://www.boost.org/doc/libs/1_86_0/libs/regex/doc/html/boost_regex/syntax/perl_syntax.html#boost_regex.syntax.perl_syntax.lookbehind)  
(?<=pattern) consumes zero characters, only if pattern could be matched against the characters preceding the current position (pattern must be of fixed length).  
  
(?<!pattern) consumes zero characters, only if pattern could not be matched against the characters preceding the current position (pattern must be of fixed length)._  
  
The desired functionality can often instead be created by using an OR of two separate look-behinds:  
```perl  
/\w+(?:(?<=Sublime)|(?<=Bob))/  
```   
Or adapt your branches to make them the same length such as:  
```perl  
/\w+(?<=Sublime|....Bob)/  
```

---

## Comment 2

> Username: JohnAlt2  
> Created at: 2024-12-05 17:44:23 UTC  
> Url: https://github.com/boostorg/regex/issues/225#issuecomment-2521036595  

@Palloxin , did the previous guidance resolve your issue?

---

## Comment 3

> Username: Palloxin  
> Created at: 2024-12-08 11:13:31 UTC  
> Updated at: 2024-12-08 11:14:26 UTC  
> Url: https://github.com/boostorg/regex/issues/225#issuecomment-2525652828  

Hmm. What i asked is clear enough. I didnt ask for support or an alternative solution. If you are an admin and cannot implement it, just close the issue.

---

## Comment 4

> Username: Stormur  
> Created at: 2025-11-26 17:04:45 UTC  
> Url: https://github.com/boostorg/regex/issues/225#issuecomment-3582321606  

I would also very much like this factual bug to be resolved.

---

## Comment 5

> Username: JohnAlt2  
> Created at: 2025-11-26 23:30:02 UTC  
> Url: https://github.com/boostorg/regex/issues/225#issuecomment-3583536554  

For us, variable length look-behind would be a good improvement. My quite possibly mistaken understanding/presumption is this limitation is in place as a compromise to increase performance? @jzmaddock   
   
Therefore, IF fixing this has a measurable performance reduction on the library in general, or this has any substantial performance degradation on fixed-length look-behinds, then that would be concerning for our Boost RegEx based product's performance. Presumably, such an overhead would be also concerning for some other Boost integrators as well.  
  
Not arguing against fixing this. Just saying we need to be wary whether there is a cost to the change.   
   
FYI: I am not a Boost project admin. I am from a very heavy usage integrator of the RegEx engine.
