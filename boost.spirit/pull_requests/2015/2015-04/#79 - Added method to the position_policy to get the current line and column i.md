# #79 Added method to the position_policy to get the current line and column i... [Merged]

> Username: truebden  
> Created at: 2015-04-15 20:40:46 UTC  
> Updated at: 2015-04-16 09:56:50 UTC  
> Merged at: 2015-04-16 09:56:50 UTC  
> Closed at: 2015-04-16 09:56:50 UTC  
> Url: https://github.com/boostorg/spirit/pull/79  

...n the output.  
  
By forwarding these methodes from the position_sink, someone can access the current line and column of the output_iterator when creating a new terminal.  
This is implemented as announce in the mailing list by @daminetreg and myself.

---

## Comment 1

> Username: djowel  
> Created_at: 2015-04-15 22:44:59 UTC  
> Url: https://github.com/boostorg/spirit/pull/79#issuecomment-93589683  

Why get_out_line and get_out_column instead of simply get_line and get_column?

---

## Comment 2

> Username: truebden  
> Created_at: 2015-04-16 06:01:59 UTC  
> Url: https://github.com/boostorg/spirit/pull/79#issuecomment-93651214  

The get_count function is forwared by get_out_count. I named these to function in the same way. Maybe the idea was, that when calling it on the sink inside the generate function of a terminal is to know, it is for the output and there is no overlapping to functions of a normal output iterator.  
But I can change it to simply get_line and get_column. What do you mean?

---

## Comment 3

> Username: djowel  
> Created_at: 2015-04-16 06:13:28 UTC  
> Url: https://github.com/boostorg/spirit/pull/79#issuecomment-93652824  

Yes, there is no "in" anyway in an "output" iterator.

---

## Comment 4

> Username: truebden  
> Created_at: 2015-04-16 07:07:52 UTC  
> Url: https://github.com/boostorg/spirit/pull/79#issuecomment-93661442  

Well, I removed "out" from the function names.  
Thank you for your input

---
