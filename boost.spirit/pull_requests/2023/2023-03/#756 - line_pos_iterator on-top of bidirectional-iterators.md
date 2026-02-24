# #756 line_pos_iterator on-top of bidirectional-iterators [Closed]

> Username: tobias-loew  
> Created at: 2023-03-08 12:02:29 UTC  
> Updated at: 2023-03-09 14:24:21 UTC  
> Closed at: 2023-03-09 14:24:20 UTC  
> Url: https://github.com/boostorg/spirit/pull/756  

I came over line_pos_iterator and really liked the example from https://stackoverflow.com/questions/19612657/boostspirit-access-position-iterator-from-semantic-actions. The only problem, finding the line-start is linear in the input, which is a burden for large inputs. But when the iterator is bidirectional, finding the line-start can be implemented linear to the average line-length.

---

## Comment 1

> Username: djowel  
> Created_at: 2023-03-08 12:14:01 UTC  
> Url: https://github.com/boostorg/spirit/pull/756#issuecomment-1460066472  

Nice!

---

## Comment 2

> Username: djowel  
> Created_at: 2023-03-08 12:25:22 UTC  
> Url: https://github.com/boostorg/spirit/pull/756#issuecomment-1460078730  

Wouldn't it be nice to have this incorporated into the original line_pos_iterator and just switch to the code depending on iterator type? That's kinda what I was expecting.

---

## Comment 3

> Username: tobias-loew  
> Created_at: 2023-03-08 16:37:47 UTC  
> Url: https://github.com/boostorg/spirit/pull/756#issuecomment-1460472318  

I'm on it.

---

## Comment 4

> Username: tobias-loew  
> Created_at: 2023-03-09 14:24:20 UTC  
> Url: https://github.com/boostorg/spirit/pull/756#issuecomment-1462151152  

Almost three years no change on this file and then two within a day. Wow!  
I closed this PR and opened a new one https://github.com/boostorg/spirit/pull/757

---
