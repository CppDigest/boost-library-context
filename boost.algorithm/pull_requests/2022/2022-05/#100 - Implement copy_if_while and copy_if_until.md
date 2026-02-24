# #100 Implement copy_if_while and copy_if_until [Merged]

> Username: jgopel  
> Created at: 2022-05-11 03:57:40 UTC  
> Updated at: 2022-06-29 00:46:46 UTC  
> Merged at: 2022-06-29 00:46:46 UTC  
> Closed at: 2022-06-29 00:46:46 UTC  
> Url: https://github.com/boostorg/algorithm/pull/100  

Problem:  
- There is no way to signal that a copy should proceed, selecting  
  elements by a predicate until some condition is met. This is useful  
  for patterns along the lines of "copy selected elements until there  
  are n total elements in the output".  
  
Solution:  
- Introduce `copy_if_while()` and `copy_if_until()`.

---

## Review 1 [Commented]

> Username: tzlaine  
> Created_at: 2022-05-27 22:03:33 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/algorithm/pull/100#pullrequestreview-988183230  

@jgopel  This needs documentation.  The inline Doxygen comments are important, but you're missing the tutorial-level docs, like these: https://www.boost.org/doc/libs/1_79_0/libs/algorithm/doc/html/algorithm/Misc.html .  See one of my PRs for how to add to the docs.

---

## Review 2 [Commented]

> Username: tzlaine  
> Created_at: 2022-05-27 22:07:41 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/algorithm/pull/100#pullrequestreview-988187576  

📁 include/boost/algorithm/cxx11/copy_if.hpp

```diff
 138 |+ /// \param term_pred A predicate for testing whether to end the copy operation
 139 |+ template<typename InputIterator, typename OutputIterator, typename CopyPredicate, typename TerminatePred>
 140 |+ BOOST_CXX14_CONSTEXPR std::pair<InputIterator, OutputIterator>
```

> Username: tzlaine  
> Created_at: 2022-05-27 22:07:41 UTC  
> Url: https://github.com/boostorg/algorithm/pull/100#discussion_r884006026  

I think the return type here and elsewhere should be `in_out_result`.  Users should expect to use `.in` and `.out` rather than `.first` and `.second`, as of C++20.  See https://en.cppreference.com/w/cpp/algorithm/ranges/return_types/in_out_result .  A version will have to be added to the `boost::algorithm` namespace.

> Username: mclow  
> Created_at: 2022-05-28 15:52:23 UTC  
> Url: https://github.com/boostorg/algorithm/pull/100#discussion_r884150099  

I disagree; I think that having `copy_while` return one type and having `copy_if_while` having a *different* return type (with the same information) will confuse users.  
  
P.S I'm not really interested in changing the return type of `copy_while`, either.


---

## Comment 3

> Username: jgopel  
> Created_at: 2022-05-29 21:32:19 UTC  
> Url: https://github.com/boostorg/algorithm/pull/100#issuecomment-1140528315  

@tzlaine Thanks for the feedback Zach - I've added documentation using the existing documentation for `copy_while` and `copy_until` as a template. Please let me know if there's anything that I can do to improve that 🙂   
  
The documentation that I have is certainly not as thorough as your documentation is on #47 or on #99. I viewed this as a tradeoff between consistency and thoroughness. In my experience, consistency in documentation is very important, so I decided to prioritize that over thoroughness. I could have had both consistency and thoroughness by updating the existing documentation to be more thorough, but that felt out of scope for this PR. If that's something that would be a welcome addition, I would be more than happy to address that in another PR!

---

## Comment 4

> Username: tzlaine  
> Created_at: 2022-05-31 19:37:34 UTC  
> Url: https://github.com/boostorg/algorithm/pull/100#issuecomment-1142569315  

Docs LGTM.  Marshall gets the final say, of course.

---

## Review 5 [Commented]

> Username: mclow  
> Created_at: 2022-06-24 04:17:46 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/algorithm/pull/100#pullrequestreview-1017964120  

📁 include/boost/algorithm/cxx11/copy_if.hpp

```diff
 128 | 
 129 |+ /// \fn copy_if_while ( InputIterator first, InputIterator last, OutputIterator result, CopyPredicate copy_pred, TerminatePred term_pred )
 130 |+ /// \brief Copies all the elements from the input range that satisfy the
```

> Username: mclow  
> Created_at: 2022-06-24 04:17:46 UTC  
> Url: https://github.com/boostorg/algorithm/pull/100#discussion_r905702992  

You missed something here; shouldn't this be:  
Copies all the elements from the input range that satisfy the predicate while the termination predicate is satisfied.  
  
also, is `TerminatePred` the correct term to use here?

> Username: jgopel  
> Created_at: 2022-06-24 16:49:53 UTC  
> Url: https://github.com/boostorg/algorithm/pull/100#discussion_r906236816  

I fixed the missing documentation here.

> Username: jgopel  
> Created_at: 2022-06-24 16:50:54 UTC  
> Url: https://github.com/boostorg/algorithm/pull/100#discussion_r906237515  

I'm not sure if `TerminatePred` is correct - I definitely struggled with naming of the predicates to indicate what they were and how they were different - these were the best that I could come up with. If you have a better name, I am more than happy to make changes!


---

## Review 6 [Commented]

> Username: mclow  
> Created_at: 2022-06-24 04:18:33 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/algorithm/pull/100#pullrequestreview-1017964446  

📁 include/boost/algorithm/cxx11/copy_if.hpp

```diff
 150 |+ 
 151 |+ /// \fn copy_if_while ( const Range& r, OutputIterator result, CopyPredicate copy_pred, TerminatePred term_pred )
 152 |+ /// \brief Copies all the elements from the input range that satisfy the
```

> Username: mclow  
> Created_at: 2022-06-24 04:18:33 UTC  
> Url: https://github.com/boostorg/algorithm/pull/100#discussion_r905703250  

Same as above; there's some missing text here.

> Username: jgopel  
> Created_at: 2022-06-24 16:49:40 UTC  
> Url: https://github.com/boostorg/algorithm/pull/100#discussion_r906236666  

Fixed.


---

## Review 7 [Commented]

> Username: mclow  
> Created_at: 2022-06-24 04:23:51 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/algorithm/pull/100#pullrequestreview-1017964537  

This looks really good to me; just a couple bits of doxygen copy-pasta.  
  
Sorry this took so long; my hand is healing very slowly

📁 include/boost/algorithm/cxx11/copy_if.hpp

```diff
 166 |+ 
 167 |+ /// \fn copy_if_until ( InputIterator first, InputIterator last, OutputIterator result, CopyPredicate copy_pred, TerminatePred term_pred )
 168 |+ /// \brief Copies all the elements from the input range that satisfy the
```

> Username: mclow  
> Created_at: 2022-06-24 04:18:46 UTC  
> Updated_at: 2022-06-24 04:23:52 UTC  
> Url: https://github.com/boostorg/algorithm/pull/100#discussion_r905703307  

Once again.

> Username: jgopel  
> Created_at: 2022-06-24 16:49:34 UTC  
> Url: https://github.com/boostorg/algorithm/pull/100#discussion_r906236576  

Fixed.

---

📁 include/boost/algorithm/cxx11/copy_if.hpp

```diff
 179 |+ copy_if_until ( InputIterator first, InputIterator last, OutputIterator result, CopyPredicate copy_pred, TerminatePred term_pred)
 180 |+ {
 181 |+     for ( ; first != last and not term_pred(*first); ++first ) {
```

> Username: mclow  
> Created_at: 2022-06-24 04:20:36 UTC  
> Updated_at: 2022-06-24 04:23:52 UTC  
> Url: https://github.com/boostorg/algorithm/pull/100#discussion_r905703963  

I'm not a big fan of the `not` keyword; because older compilers didn't support it.  But - I'm not going to reject this PR because of that.

> Username: jgopel  
> Created_at: 2022-06-24 16:49:29 UTC  
> Url: https://github.com/boostorg/algorithm/pull/100#discussion_r906236522  

Oops - habits die hard. I've updated these to match the style of the surrounding file.

---

📁 include/boost/algorithm/cxx11/copy_if.hpp

```diff
 189 |+ /// \fn copy_if_until ( const Range& r, OutputIterator result, CopyPredicate copy_pred, TerminatePred term_pred )
 190 |+ /// \brief Copies all the elements from the input range that satisfy the
 191 |+ /// predicate to the output range.
```

> Username: mclow  
> Created_at: 2022-06-24 04:20:49 UTC  
> Updated_at: 2022-06-24 04:23:52 UTC  
> Url: https://github.com/boostorg/algorithm/pull/100#discussion_r905704028  

Again.

> Username: jgopel  
> Created_at: 2022-06-24 16:49:03 UTC  
> Url: https://github.com/boostorg/algorithm/pull/100#discussion_r906236209  

Fixed.


---

## Comment 8

> Username: jgopel  
> Created_at: 2022-06-24 16:52:10 UTC  
> Url: https://github.com/boostorg/algorithm/pull/100#issuecomment-1165755989  

> This looks really good to me; just a couple bits of doxygen copy-pasta.  
>   
> Sorry this took so long; my hand is healing very slowly  
  
Thanks for the feedback! I'm sorry to hear your hand is healing slowly 😞 No rush on responding to the one comment that I had - if you need to wait out the hand that's totally fine, I'll be here and be motivated when you're ready!

---
