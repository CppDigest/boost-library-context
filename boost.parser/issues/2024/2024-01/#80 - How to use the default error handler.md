# #80 - How to use the default error handler? [Closed]

> Username: akrzemi1  
> Created at: 2024-01-27 10:11:29 UTC  
> Updated at: 2024-01-29 03:00:52 UTC  
> Closed at: 2024-01-29 03:00:51 UTC  
> Url: https://github.com/boostorg/parser/issues/80  

The docs say that by default I get the `default_error_handler` which produces a nice GCC-like error message and prints it to `std::cerr`. This is not confirmed by any of my examples. E.g., https://godbolt.org/z/a5vb8E87o  
  
I just get a `false` return value, and nothing is printed.  
  
Apart from this discrepancy between docs and the reality, how do I trigger this nice error messages from my parser?

---

## Comment 1

> Username: akrzemi1  
> Created at: 2024-01-27 11:25:59 UTC  
> Url: https://github.com/boostorg/parser/issues/80#issuecomment-1913124024  

Ok, now I understand better. By "errors" you mean only the failed expectations of the expectation operator. Any other syntax mismatches are apparently not logged as errors.  
  
I understand that for the latter kind it is more difficult to indicate one specific place where the syntax error is. But I remember the Boost.Spirit did return the place where it first disliked th input. Couldn't you use this as approximation?  
  
Anyway, the docs should reflect the current behaviour as it is not intuitive: when I read "error handler" and I know that I have a syntax error in my input, I naturally expected it to be handled.

---

## Comment 2

> Username: akrzemi1  
> Created at: 2024-01-27 11:26:42 UTC  
> Url: https://github.com/boostorg/parser/issues/80#issuecomment-1913124236  

Actually, they are error *reporters*, not *handlers*. The problem still needs to be handled by someone else.

---

## Comment 3

> Username: tzlaine  
> Created at: 2024-01-27 22:03:21 UTC  
> Updated at: 2024-01-28 23:21:40 UTC  
> Url: https://github.com/boostorg/parser/issues/80#issuecomment-1913348098  

> Ok, now I understand better. By "errors" you mean only the failed expectations of the expectation operator. Any other syntax mismatches are apparently not logged as errors.  
>   
> I understand that for the latter kind it is more difficult to indicate one specific place where the syntax error is. But I remember the Boost.Spirit did return the place where it first disliked th input. Couldn't you use this as approximation?  
  
No.  Remember, all errors happen either 1) at failed expectation points, or 2) at the end of input, or 3) at some place where the next thing does not match some part of the current parse.  
  
EDIT: Ignore the next two paragraphs; see the next comment below.  
1) and 2) are easily reported.  I should add 2) as a case that gets reported by the standard error handler.  3) can't be reported accurately.  Whenever I reach the end of the match of the input with the current parser P, and P is not complete, I know P has failed.  That's fine, backtrack and resume the parse.  If there is another alternative left to try, try it; otherwise, the parse fails.  At the point that I realize there's no alternative left to try, I've backtracked, and the error point that I would report is the place that P (and maybe its peer alternatives) first started trying to match, not at the last successful point within the parse of P.  It's going to be pretty misleading, depending on the state of the parse.  Also, P is not special; it's just one alternative.  What if P matched only a couple of characters into the input, but an earlier parser Prev matched 30 characters in.  Should I report that the expected next thing was P or Prev, or maybe even some other alternative?  
  
What I *can* do though is report the last successful point in the parsse, without any indication of what it expected next.  I should probably do that; it's a lot better than nothing.  
  
> Anyway, the docs should reflect the current behaviour as it is not intuitive: when I read "error handler" and I know that I have a syntax error in my input, I naturally expected it to be handled.  
  
Will do.  I'll include the comment I made above; it's definitely not clear enough.

---

## Comment 4

> Username: tzlaine  
> Created at: 2024-01-28 23:44:15 UTC  
> Url: https://github.com/boostorg/parser/issues/80#issuecomment-1913763052  

Ok, I opened another issue (#86) which I've now abandoned.  I now feel that this is purely a documentation issue.  Let me explain.  
  
In order to error handling to happen other than at expectation points, we have to know that there is no further processing that might take place.  This is true because we have `P1 | P2 | ... | Pn` parsers ("`or_parser`").  If any one of these `Pi` parsers fails, either because it does not match, or even because it does not match due to seeing the end of input, it is not allowed to fail the parse -- the next one (`Pi+1`) might match.  If we get to the end of the alternatives of the `or_parser` and `Pn` fails, we still cannot fail the top-level parse, because the `or_parser` might be a subparser within a parent `or_parser`.  Etc., etc.  This feels suspiciously like the halting problem.  
  
Ok, so what might we do?  I mentioned in a previous post that we could at least indicate when we ran into end of input.  But we cannot, for exactly the same reason already stated.  For any parser `P`, reaching EOI is a failure for *`P`*, but not necessarily for the whole parse.  
  
Perhaps we could record that farthest point ever reached during the parse, and report that at the top level, if the top level parser fails.  That would be little help without knowing which parser was active when we reached that point.  This would require some sort of repeated memory allocation, since currently the progress point of the parser is stored exclusively on the stack -- by the time we fail the top-level parse, all those far-reaching stack frames are long gone.  Not the best.  
  
Worse still, knowing how far you got in the parse and which parser was active is not very useful.  Consider this.  
  
```c++  
auto a_b = char_('a') >> char_('b');  
auto c_b = char_('c') >> char_('b');  
  
auto result = parse("acb", a_b | c_b);  
```  
  
If we reported the farthest-reaching parser and it's position, it would be the `a_b` parser, at position `"bc"`.  Is this really enlightening?  Was the input mistake putting the `'a'` at the beginning or putting the `'c'` in the middle?  If you point the user at `a_b` as the parser that failed, and never mention `c_b`, are you just steering them in the wrong direction?  
  
All error messages must come from failed expectation points.  Think about parsing JSON.  If you open a list with `'['`, you will get an error that the list is ill-formed if it's not a proper list.  If you open an object with `'{'`, the same thing is possible -- when missing the matching `'}'`, you can tell the user "that's not an object", and this is useful feedback.  The same thing with a partially parsed number, etc.  If the JSON parser does not build in expectations like `'}'` must eventually follow `'{'`, how can the parser know that the missing `'}'` is really a problem, and that no later parser will not match the input even without the `'}'`?  
  
This needs to be documented of course.  It can't be automated though.

---

## Comment 5

> Username: tzlaine  
> Created at: 2024-01-28 23:45:19 UTC  
> Url: https://github.com/boostorg/parser/issues/80#issuecomment-1913763374  

> Actually, they are error _reporters_, not _handlers_. The problem still needs to be handled by someone else.  
  
No, they're handlers.  You can make one that does whatever.  One of the built-in ones does zero reporting.
