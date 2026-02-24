# #706 Tweaked exception text in case problem is with invalid whitespace [Closed]

> Username: dkondor  
> Created at: 2020-04-28 14:39:05 UTC  
> Updated at: 2023-04-13 17:22:05 UTC  
> Closed at: 2023-04-13 17:22:04 UTC  
> Url: https://github.com/boostorg/geometry/pull/706  

Clarify exception text in case the parsing error might be the result of the string including tab ('\t') or newline ('\r' or '\n') character. Suggestion for #703   
  
In my opinion, current exception text can be hard to interpret in this case. Since looking at the output, all whitespace looks similar, it might not be obvious which character is problematic. E.g.  
```  
Too many tokens at '  
' in 'LINESTRING(1.0 2.0, 2.0 1.0)  
'  
```  
  
or especially:  
```  
bad lexical cast: source type value could not be interpreted as target at '  
' in 'LINESTRING(1.0 2.0,   
 2.0 1.0)'  
```  
since this looks like a regular line break in my terminal.

---

## Review 1 [Commented]

> Username: awulkiew  
> Created_at: 2020-04-28 16:47:05 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/706#pullrequestreview-402025000  

📁 include/boost/geometry/io/wkt/read.hpp

```diff
  88 |+         {
  89 |+ 			complete += "\n(note: newline and tab characters are not allowed in WKT)";
  90 |+ 		}
```

> Username: awulkiew  
> Created_at: 2020-04-28 16:47:04 UTC  
> Updated_at: 2020-04-28 16:47:05 UTC  
> Url: https://github.com/boostorg/geometry/pull/706#discussion_r416766723  

We don't use tabs. :)

> Username: mloskot  
> Created_at: 2020-04-28 16:48:17 UTC  
> Updated_at: 2020-04-28 16:48:18 UTC  
> Url: https://github.com/boostorg/geometry/pull/706#discussion_r416767485  

https://github.com/boostorg/geometry/wiki/Guidelines-for-Developers


---

## Review 2 [Commented]

> Username: awulkiew  
> Created_at: 2020-04-28 16:54:29 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/706#pullrequestreview-402031044  

📁 include/boost/geometry/io/wkt/read.hpp

```diff
  87 |+         if (it->find_first_of("\t\r\n") != std::string::npos)
  88 |+         {
  89 |+ 			complete += "\n(note: newline and tab characters are not allowed in WKT)";
```

> Username: awulkiew  
> Created_at: 2020-04-28 16:54:29 UTC  
> Updated_at: 2020-05-06 14:05:10 UTC  
> Url: https://github.com/boostorg/geometry/pull/706#discussion_r416771909  

Should we add line break here instead of simply appending the note to the message? We don't know exactly how this message is going to be used by the user. It not necessarily is going to be displayed with `cout`/`cerr`.


---

## Review 3 [Commented]

> Username: awulkiew  
> Created_at: 2020-04-28 17:06:42 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/706#pullrequestreview-402040506  

📁 include/boost/geometry/io/wkt/read.hpp

```diff
  85 |+         // add extra diagnostic message if the problematic token included
  86 |+         // common invalid whitespace characters to help users
  87 |+         if (it->find_first_of("\t\r\n") != std::string::npos)
```

> Username: awulkiew  
> Created_at: 2020-04-28 17:06:41 UTC  
> Updated_at: 2020-04-28 17:06:42 UTC  
> Url: https://github.com/boostorg/geometry/pull/706#discussion_r416779756  

I'm ok with this approach but I'd like to present different ones and get your feedback. What do you think about the following alternatives:  
  
1. Check wider range of invalid characters because this might potentially help other users, not only the ones dealing with whitespaces and add this specific character to the additional info note below.  
  
2. Escape special characters in `wkt.substr(0, 100)` in the message instead of adding the note, e.g. put tab as `\t`.


---

## Comment 4

> Username: barendgehrels  
> Created_at: 2023-04-13 17:22:04 UTC  
> Url: https://github.com/boostorg/geometry/pull/706#issuecomment-1507336671  

It's now supported.  
https://github.com/boostorg/geometry/commit/f92671b933e585ecba3c1b02c830f6e90e813da9  
Closing.

---
