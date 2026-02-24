# #14 Fix to Ticket #11001 [Merged]

> Username: brunocodutra  
> Created at: 2015-02-09 02:05:33 UTC  
> Updated at: 2015-04-28 22:48:52 UTC  
> Merged at: 2015-04-28 20:06:42 UTC  
> Closed at: 2015-04-28 20:06:42 UTC  
> Url: https://github.com/boostorg/mpl/pull/14  

According to reference, insert_range should work for any Extensible  
sequence or Extensible Associative sequence, but the default  
implementation of insert_range_impl assumes a front_inserter is defined  
for the given sequence, but neither Extensible nor every Extensible  
Associative sequences are required to also be a Front Extensible  
sequence. This fix rely only on insert, which is defined for every  
Extensible sequence.

---

## Comment 1

> Username: brunocodutra  
> Created_at: 2015-02-09 11:12:38 UTC  
> Updated_at: 2015-02-09 11:14:55 UTC  
> Url: https://github.com/boostorg/mpl/pull/14#issuecomment-73492410  

Taking a closer look at the documentation, I came to realize that order does naturally matter for a given generic Extensible sequence and indeed inserting elements in the end of a list has linear time complexity, while it is constant when inserting at the beginning.  
  
I thus believe it would be better to implement insert_range by means of reverse_fold, hence always inserting elements at the beginning.  
  
Later this evening I believe I could take a look at this and update the pull request.

---

## Comment 2

> Username: brunocodutra  
> Created_at: 2015-02-21 21:54:14 UTC  
> Url: https://github.com/boostorg/mpl/pull/14#issuecomment-75395030  

Just like for insert, the second parameter of insert_range, Pos, doesn't really mean anything for associative sequences and is essentially ignored. To my understanding, the code and documentation should be updated to reflect this fact. I volunteer to do the job, but I don't think these changes fit the scope of this pull request, since in fact it consists in a new feature, so I plan to open a new request just for that.

---

## Comment 3

> Username: brunocodutra  
> Created_at: 2015-03-22 18:40:25 UTC  
> Url: https://github.com/boostorg/mpl/pull/14#issuecomment-84672266  

All mpl tests were verified to pass for clang 3.6 and gcc 4.9.2 on linux and for msvc 2010 on windows xp

---

## Comment 4

> Username: eldiener  
> Created_at: 2015-04-28 20:06:22 UTC  
> Url: https://github.com/boostorg/mpl/pull/14#issuecomment-97191845  

This looks good and has been locally tested with all tests passing, so I am going to merge to develop. Please check the 'develop' regression tests to make sure there is no errors being caused by this fix.

---

## Comment 5

> Username: brunocodutra  
> Created_at: 2015-04-28 22:47:33 UTC  
> Url: https://github.com/boostorg/mpl/pull/14#issuecomment-97247870  

Thank you Edward, I'll watch the test reports for anything suspicious.

---
