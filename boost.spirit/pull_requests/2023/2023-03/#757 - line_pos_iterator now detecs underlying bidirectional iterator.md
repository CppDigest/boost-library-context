# #757 line_pos_iterator now detecs underlying bidirectional iterator [Merged]

> Username: tobias-loew  
> Created at: 2023-03-09 14:23:48 UTC  
> Updated at: 2023-03-13 08:10:09 UTC  
> Merged at: 2023-03-12 21:03:10 UTC  
> Closed at: 2023-03-12 21:03:10 UTC  
> Url: https://github.com/boostorg/spirit/pull/757  

line_pos_iterator now detecs underlying bidirectional iterator and uses iterator-tag dispatching to select appropriate implementations of get_line_start (for bidirectional-iterators: linear-complexity in line-length).  
Breaking change: all functions now return underlying iterators instead of line_pos_iterators. But, since they are only used to get the bounds for the current line, returning a full line_pos_iterator seems unnecessary.

---

## Comment 1

> Username: Kojoley  
> Created_at: 2023-03-09 15:01:27 UTC  
> Url: https://github.com/boostorg/spirit/pull/757#issuecomment-1462208230  

Could it be done without breaking public API maybe?

---

## Comment 2

> Username: tobias-loew  
> Created_at: 2023-03-10 08:29:59 UTC  
> Url: https://github.com/boostorg/spirit/pull/757#issuecomment-1463448160  

There are pros and cons wrt. to the API break:  
  
pros:  
1. get_line_start and get_current_line return underlying iterators which usually fit into registers  
2. get_line_start for forward-only iterators no longer needs to track the line-number, which is an overhead in the loop (though I haven't measured it)  
3. users get build errors and have to reconsider their code, which in the light of b0237057c804fa6a54871704f329a8b16bb97c42 should be done anyway  
  
cons:  
1. user code gets broken  
2. get_line_start and get_current_line return less information than before and user have to write their own code, if they needed that information  
  
IMHO pros 1. and 2. are desirable, and prohibiting the cons would be also.   
The results of get_line_start and get_current_line are usualy only used to mark the begin and end in the input. So, creating line_pos_iterators, when usually not needed violates the zero-abstraction principle.  
  
So, one possible ways could be:  
- creating new names for get_line_start and get_current_line that only return the underlying iterator, e.g. get_line_start_base and get_current_line_base  
- leaving line_pos_iterator like it is and make a new class-template, e.g. line_pos_iterator_ex  
- an intermediate way could be: additionally adding new names for versions of get_line_start and get_current_line that return line_pos_iterators. This would resolve cons 2.

---

## Comment 3

> Username: Kojoley  
> Created_at: 2023-03-10 16:17:39 UTC  
> Url: https://github.com/boostorg/spirit/pull/757#issuecomment-1464044262  

> 1. get_line_start and get_current_line return underlying iterators which usually fit into registers  
> 2. get_line_start for forward-only iterators no longer needs to track the line-number, which is an overhead in the loop (though I haven't measured it)  
  
If you want to work with underlying iterators and avoid overhead of line counting - you can obtain them with `.base()` method and pass them to get_line_start/get_current_line.  
  
> 3. users get build errors and have to reconsider their code, which in the light of [b023705](https://github.com/boostorg/spirit/commit/b0237057c804fa6a54871704f329a8b16bb97c42) should be done anyway  
  
That commit fixed a bug, I don't understand what you meant.

---

## Review 4 [Commented]

> Username: Kojoley  
> Created_at: 2023-03-10 16:19:38 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/spirit/pull/757#pullrequestreview-1335164552  

📁 include/boost/spirit/home/support/iterators/line_pos_iterator.hpp

```diff
  68 |+         line_pos_iterator::iterator_adaptor_(base)
  69 |+         , line(line_data.line)
  70 |+         , prev_n(), prev_r()
```

> Username: Kojoley  
> Created_at: 2023-03-10 16:19:37 UTC  
> Updated_at: 2023-03-10 16:19:38 UTC  
> Url: https://github.com/boostorg/spirit/pull/757#discussion_r1132581536  

This is a hella confusing constructor. Why not `(Iterator base, std::size_t base_line)`?

> Username: tobias-loew  
> Created_at: 2023-03-11 10:12:12 UTC  
> Url: https://github.com/boostorg/spirit/pull/757#discussion_r1133063972  

Agree, I made another commit. Now, allowing an optional start-line for the from-base constructor.


---

## Comment 5

> Username: tobias-loew  
> Created_at: 2023-03-11 10:02:16 UTC  
> Url: https://github.com/boostorg/spirit/pull/757#issuecomment-1464876065  

> > 1. get_line_start and get_current_line return underlying iterators which usually fit into registers  
> > 2. get_line_start for forward-only iterators no longer needs to track the line-number, which is an overhead in the loop (though I haven't measured it)  
>   
> If you want to work with underlying iterators and avoid overhead of line counting - you can obtain them with `.base()` method and pass them to get_line_start/get_current_line.  
>  
Yes, but that would require the user to know, that get_line_start/get_current_line is suboptimal. I think it's better to either invoke the optimal version or give at least an advice, that there is a faster way to do it.  
   
> > 3. users get build errors and have to reconsider their code, which in the light of [b023705](https://github.com/boostorg/spirit/commit/b0237057c804fa6a54871704f329a8b16bb97c42) should be done anyway  
>   
> That commit fixed a bug, I don't understand what you meant.  
  
To make it clear: the fix is of course correct and necessary. But, the code was broken for CRLF input since the beginning, and nobody complained (or at least nobody fixed it) before. So, either users didn't check the outcome or they wrote their own fix. Nevertheless, a change of the (apparently wrong) semantics after so many years should be made visible to the user.

---

## Comment 6

> Username: Kojoley  
> Created_at: 2023-03-11 17:42:02 UTC  
> Url: https://github.com/boostorg/spirit/pull/757#issuecomment-1464961140  

> Yes, but that would require the user to know, that get_line_start/get_current_line is suboptimal. I think it's better to either invoke the optimal version or give at least an advice, that there is a faster way to do it.  
  
What about get_column? It would be still suboptimal even after this PR.  
  
I think the code would be much clearer if you did:  
```cpp  
// optimized get_line_start for bidirectional iterators  
// optimized get_column for bidirectional iterators  
  
line_pos_iterator<Iterator> get_line_start(line_pos_iterator<Iterator> lower_bound,  
                                           line_pos_iterator<Iterator> current)  
{  
    // No need in line number counting because it will be the same  
    Iterator it = get_line_start(lower_bound.base(), current.base());  
    return line_pos_iterator<Iterator>(it, current.position());  
}  
  
line_pos_iterator<Iterator> get_line_end(line_pos_iterator<Iterator> current,  
                                         line_pos_iterator<Iterator> upper_bound)  
{  
    // No need in line number counting because it will be the same  
    Iterator it = get_line_end(current.base(), upper_bound.base());  
    return line_pos_iterator<Iterator>(it, current.position());  
}  
  
std::size_t get_column(line_pos_iterator<Iterator> lower_bound,  
                       line_pos_iterator<Iterator> current,  
                       std::size_t tabs)  
{  
    // No need in line number counting because it will be the same  
    Iterator it = get_column(lower_bound.base(), current.base(), tabs);  
    return line_pos_iterator<Iterator>(it, current.position());  
}  
```

---

## Comment 7

> Username: tobias-loew  
> Created_at: 2023-03-12 09:49:18 UTC  
> Url: https://github.com/boostorg/spirit/pull/757#issuecomment-1465142743  

Compiling with VS 2022 x64-release (default options) the generated code for   
```  
            boost::iterator_range<pos_iterator_t> const range = get_current_line(input_begin, position, input_end);  
            std::string const current(range.begin(), range.end());  
```  
and   
```  
            boost::iterator_range<std::string::const_iterator> const range = get_current_line(input_begin.base(), position.base(), input_end.base());  
            std::string const current(range.begin(), range.end());  
```  
are almost identical. The optimizer removes the line_pos_iterator creation/destruction in the upper code.

---

## Comment 8

> Username: tobias-loew  
> Created_at: 2023-03-13 08:10:09 UTC  
> Url: https://github.com/boostorg/spirit/pull/757#issuecomment-1465684011  

Thanks

---
