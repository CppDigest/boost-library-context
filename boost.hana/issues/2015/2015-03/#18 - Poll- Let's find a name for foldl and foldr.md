# #18 - Poll: Let's find a name for foldl and foldr [Closed]

> Username: ldionne  
> Created at: 2015-03-05 18:56:14 UTC  
> Updated at: 2015-03-31 12:28:53 UTC  
> Closed at: 2015-03-31 12:28:53 UTC  
> Url: https://github.com/boostorg/hana/issues/18  

It was suggested on the [Boost mailing list](http://article.gmane.org/gmane.comp.lib.boost.devel/258028) that `foldl` and `foldr` were ugly. Let's settle this with a poll. Leave a comment to vote for an option or to propose a new option.  
  
Since there are also `foldl1` and `foldr1`, which are equivalent to `foldl` and `foldr` but do not take an initial state, it seems legitimate to ask that a valid naming convention also accounts for them. Here are the current propositions:  
1. `foldl`/`foldr`/`foldl1`/`foldr1` (as is)  
2. `fold`/`reverse_fold`/??/??  
3. `fold_left`/`fold_right`/??/??  
4. `fold.left`/`fold.right`/??/?? (optionally, we could have `fold == fold.left`)

---

## Comment 1

> Username: ericniebler  
> Created at: 2015-03-05 21:58:46 UTC  
> Url: https://github.com/boostorg/hana/issues/18#issuecomment-77461127  

(2) `fold`/`reverse_fold`

---

## Comment 2

> Username: pfultz2  
> Created at: 2015-03-05 22:11:03 UTC  
> Url: https://github.com/boostorg/hana/issues/18#issuecomment-77463255  

I vote for `fold`/`reverse_fold`. This is what fusion uses.   
  
Also, they could be overloaded so they don't take initial state rather than have two different named versions.

---

## Comment 3

> Username: ldionne  
> Created at: 2015-03-05 22:16:51 UTC  
> Updated at: 2015-03-05 22:20:33 UTC  
> Url: https://github.com/boostorg/hana/issues/18#issuecomment-77464190  

I like the idea of overloading them. I would then document how the no-state version is tag-dispatched, a bit like is done for the different types of zips. However, I really dislike `reverse_fold`, so I'm going to vote for `fold.left`/`fold.right`, with `fold` which defaults to `fold.left`, and overloaded versions for providing no state.

---

## Comment 4

> Username: ahundt  
> Created at: 2015-03-05 22:17:23 UTC  
> Url: https://github.com/boostorg/hana/issues/18#issuecomment-77464295  

foldl are foldr are well known names for anyone who has done functional programming.

---

## Comment 5

> Username: ericniebler  
> Created at: 2015-03-05 22:37:58 UTC  
> Updated at: 2015-03-05 22:43:28 UTC  
> Url: https://github.com/boostorg/hana/issues/18#issuecomment-77467737  

`fold.right`  
`reverse_fold`  
  
@ldionne You save 2 characters. W00t. ;-) The problem I've always had with "left" fold vs "right" fold is that I never can remember what the "left" and the "right" refer to. Does the fold start on the left? Or does it proceed left (from the right)? Or (like you suggest on the list) does the left and right refer to the associativity of the operation? I find `reverse_fold` much more descriptive.

---

## Comment 6

> Username: ldionne  
> Created at: 2015-03-05 22:49:54 UTC  
> Url: https://github.com/boostorg/hana/issues/18#issuecomment-77469592  

@ericniebler I observed the difference of characters and concluded it is not a valid justification :-). I just dislike `reverse_fold`. My mnemonic for `fold_left` and `fold_right` is this:  
  
`fold_left` associates to the left, i.e.:  
  
```  
(((((state + x1) + x2) + x3) + x4) + x5)  
```  
  
See how the leftmost element is processed first, and how the parentheses are to the left? Now, just replace `+` by any binary function written in infix application, and you've got it. To the contrary, `fold_right` associates to the right, i.e.:  
  
```  
(x1 + (x2 + (x3 + (x4 + (x5 + state)))))  
```  
  
Again, the rightmost element is processed first, and the parentheses are to the right. When you see the folds this way, the order of the arguments required by the binary function also becomes obvious. For fold left, it should be   
  
```  
state + element == +(state, element)  
                == f(state, element)  
```  
  
whereas for fold right, it should be  
  
```  
element + state == +(element, state)  
                == f(element, state)  
```

---

## Comment 7

> Username: ericniebler  
> Created at: 2015-03-05 22:51:57 UTC  
> Url: https://github.com/boostorg/hana/issues/18#issuecomment-77469948  

Sure, but you have to _remember_ that. With `reverse_fold`, I don't have to remember. I see the name and know.

---

## Comment 8

> Username: pabristow  
> Created at: 2015-03-06 11:45:57 UTC  
> Url: https://github.com/boostorg/hana/issues/18#issuecomment-77547122  

Not foldl or foldr :-(

---

## Comment 9

> Username: viboes  
> Created at: 2015-03-06 19:24:54 UTC  
> Url: https://github.com/boostorg/hana/issues/18#issuecomment-77619472  

I believe it is important to make the distinction between these two results  
  
```  
(x1 + (x2 + (x3 + (x4 + (x5 + state)))))  
```  
  
```  
(((((state + x5) + x4) + x3) + x2) + x1)  
```  
  
I would name the first `fold_right`. and the second `fold_reverse`. The difference is related to the function signature  
  
```  
Fun :  Element State -> State  
```  
  
or   
  
```  
Fun : State  Element -> State  
```  
  
The reason I would name the second one fold_reverse is because  
  
`fold_reverse(f, s0, l)' equivalent to`fold_left(f, s0, reverse(l))'?  
  
+1 for `fold_left`/`fold_right` for the first interpretation  
+1 for `fold`/`reverse_fold` for the second one  
  
fold == fold_left

---

## Comment 10

> Username: tzlaine  
> Created at: 2015-03-09 16:10:22 UTC  
> Url: https://github.com/boostorg/hana/issues/18#issuecomment-77884392  

1.  The community at large will understand this with low-to-no required explanation.

---

## Comment 11

> Username: pfultz2  
> Created at: 2015-03-09 18:11:46 UTC  
> Url: https://github.com/boostorg/hana/issues/18#issuecomment-77909812  

@tzlaine Zach, did you mean number 1 or 2? Because it looks like the markdown parser transformed your 2 into a 1.

---

## Comment 12

> Username: tzlaine  
> Created at: 2015-03-09 19:31:23 UTC  
> Url: https://github.com/boostorg/hana/issues/18#issuecomment-77924975  

What the what?  Yes, I meant 2 ("two").

---

## Comment 13

> Username: ericniebler  
> Created at: 2015-03-09 19:32:06 UTC  
> Url: https://github.com/boostorg/hana/issues/18#issuecomment-77925103  

Markdown FTW

---

## Comment 14

> Username: kris-jusiak  
> Created at: 2015-03-10 20:12:16 UTC  
> Updated at: 2015-03-10 20:12:26 UTC  
> Url: https://github.com/boostorg/hana/issues/18#issuecomment-78137637  

[2] fold/reverse_fold

---

## Comment 15

> Username: jaredgrubb  
> Created at: 2015-03-18 05:29:09 UTC  
> Url: https://github.com/boostorg/hana/issues/18#issuecomment-82749241  

+1 foldl/foldr

---

## Comment 16

> Username: ldionne  
> Created at: 2015-03-22 22:15:16 UTC  
> Url: https://github.com/boostorg/hana/issues/18#issuecomment-84714895  

Ok, so I'm about to close this issue. Here are the results:  
  
```  
fold/reverse_fold:  
    + ericniebler  
    + pfultz2  
    + tzlaine  
    + krzysztof-jusiak  
    + pabristow (I assume)  
    --------------------  
    5  
  
foldl/foldr:  
    + ahundt (I assume)  
    + jaredgrubb  
    --------------------  
    2  
  
fold_reverse/fold_right:  
    + viboes  
    --------------------  
    1  
  
fold.right/fold.left:  
    + ldionne  
    + someone via private email  
    --------------------  
    2  
```  
  
The chosen resolution is that `fold` and `reverse_fold` aliases are provided and their semantic matches exactly that of the MPL and Fusion, except that it's possible to omit the state argument. These aliases are introduced in afa9171.   
  
`foldl` and `foldr` will be kept around and I will maybe replace them for `fold.right` and `fold.left` (with overloads for the no-state case) when I get the time. The reason for probably replacing `foldl` and `foldr` by `fold.left` and `fold.right` is that anyone can understand those, while `foldl` and `foldr` speak arguably less to FP beginners.  
  
Finally, considering monadic folds, we currently have `foldrM` and `foldlM`. No-state variants could also be added, which would give `foldl1M` and `foldr1M`. My current plan is to have  
  
``` c++  
    fold.left.monadic(...)  
    fold.right.monadic(...)  
```  
  
with no-state overloads to replace `foldl1M` and `foldr1M`.  
  
If someone wants to raise an objection to any of this, now's the time.
