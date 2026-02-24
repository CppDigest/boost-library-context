# #141 Qualify "newline at EOF not required" with actually being there [Merged]

> Username: jefftrull  
> Created at: 2022-01-02 20:02:12 UTC  
> Updated at: 2022-01-02 22:24:40 UTC  
> Merged at: 2022-01-02 22:24:39 UTC  
> Closed at: 2022-01-02 22:24:40 UTC  
> Url: https://github.com/boostorg/wave/pull/141  

In C++11 and later modes Wave by default does not require newline prior to EOF. This works fine but  
there is one place where the test for this feature was used where Wave is not necessarily looking at the end of the file: `pp_is_last_on_line`. One observable (and surprising) result is that unknown  
directives are not getting flagged - but there may be others. The unit tests did not discover this  
problem because they generally do not set c++11 mode.  
  
This PR adds the necessary qualifiers and adds a unit test (the same as an existing one but with  
C++11 enabled).  
  
If merged this will resolve #137

---

## Comment 1

> Username: jefftrull  
> Created_at: 2022-01-02 20:26:47 UTC  
> Url: https://github.com/boostorg/wave/pull/141#issuecomment-1003770414  

btw @hkaiser Wave also has the language option `single_line` which is documented as:  
  
> If the `support_option_single_line` flag is set, the Wave library will now report an  
>  error if the last line of the processed input is not terminated by a new line...  
  
`no_newline_at_end_of_file` is unfortunately not documented in [the same place](https://github.com/boostorg/wave/blob/develop/doc/class_reference_context.html) - what's the difference? should we add an explanation?

---

## Comment 2

> Username: jefftrull  
> Created_at: 2022-01-02 21:27:50 UTC  
> Url: https://github.com/boostorg/wave/pull/141#issuecomment-1003778052  

I'm thinking in particular of [this old trac report](https://svn.boost.org/trac10/ticket/6759) which I believe is referencing [this logic](https://github.com/boostorg/wave/blob/f2957045b5668d544525492b37d071c4f4ddbcc2/include/boost/wave/util/cpp_iterator.hpp#L667)

---

## Comment 3

> Username: hkaiser  
> Created_at: 2022-01-02 21:53:50 UTC  
> Url: https://github.com/boostorg/wave/pull/141#issuecomment-1003781183  

> btw @hkaiser Wave also has the language option `single_line` which is documented as:  
>   
> > If the `support_option_single_line` flag is set, the Wave library will now report an  
> > error if the last line of the processed input is not terminated by a new line...  
>   
> `no_newline_at_end_of_file` is unfortunately not documented in [the same place](https://github.com/boostorg/wave/blob/develop/doc/class_reference_context.html) - what's the difference? should we add an explanation?  
  
@jefftrull I don't have any recollection of the difference, sorry :/ My best guess would be that the former is applied in single-line mode, the latter in normal mode.

---

## Review 4 [Commented]

> Username: hkaiser  
> Created_at: 2022-01-02 21:55:11 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/wave/pull/141#pullrequestreview-842446882  

📁 test/testwave/testfiles/t_2_031.cpp

```diff
   3 |+     http://www.boost.org/
   4 |+ 
   5 |+     Copyright (c) 2001-2012 Hartmut Kaiser. Distributed under the Boost
```

> Username: hkaiser  
> Created_at: 2022-01-02 21:55:11 UTC  
> Url: https://github.com/boostorg/wave/pull/141#discussion_r777257618  

@jefftrull you should use your copyright for new files (at least)


---

## Review 5 [Approved]

> Username: hkaiser  
> Created_at: 2022-01-02 21:55:20 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/wave/pull/141#pullrequestreview-842446888  

LGTM, thanks!

---

## Comment 6

> Username: jefftrull  
> Created_at: 2022-01-02 22:10:44 UTC  
> Updated_at: 2022-01-02 22:11:05 UTC  
> Url: https://github.com/boostorg/wave/pull/141#issuecomment-1003783247  

> > btw @hkaiser Wave also has the language option `single_line` which is documented as:  
> > > If the `support_option_single_line` flag is set, the Wave library will now report an  
> > > error if the last line of the processed input is not terminated by a new line...  
> >   
> >   
> > `no_newline_at_end_of_file` is unfortunately not documented in [the same place](https://github.com/boostorg/wave/blob/develop/doc/class_reference_context.html) - what's the difference? should we add an explanation?  
>   
> @jefftrull I don't have any recollection of the difference, sorry :/ My best guess would be that the former is applied in single-line mode, the latter in normal mode.  
  
I think I get it now, perhaps... maybe the documentation should say "... the Wave library will _not_ report an error..."? That would explain that TRAC report. Actually I might rewrite it to explain that it does more than that. I'll send another PR.

---
