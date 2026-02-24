# #282 [extensions][wkb] Fix GCC compilation errors and warnings. [Merged]

> Username: awulkiew  
> Created at: 2015-04-23 21:29:38 UTC  
> Updated at: 2015-04-26 11:58:41 UTC  
> Merged at: 2015-04-24 13:12:57 UTC  
> Closed at: 2015-04-24 13:12:57 UTC  
> Url: https://github.com/boostorg/geometry/pull/282  



---

## Comment 1

> Username: mkaravel  
> Created_at: 2015-04-24 05:55:06 UTC  
> Updated_at: 2015-04-24 11:28:33 UTC  
> Url: https://github.com/boostorg/geometry/pull/282#discussion_r29025240  

`const` should go after `L`.

---

## Comment 2

> Username: mkaravel  
> Created_at: 2015-04-24 05:58:25 UTC  
> Updated_at: 2015-04-24 11:28:33 UTC  
> Url: https://github.com/boostorg/geometry/pull/282#discussion_r29025328  

Same here: `const` should go after `ring_type`.

---

## Comment 3

> Username: mkaravel  
> Created_at: 2015-04-24 05:58:46 UTC  
> Updated_at: 2015-04-24 11:28:33 UTC  
> Url: https://github.com/boostorg/geometry/pull/282#discussion_r29025340  

Same issue here.

---

## Comment 4

> Username: mkaravel  
> Created_at: 2015-04-24 05:58:56 UTC  
> Updated_at: 2015-04-24 11:28:33 UTC  
> Url: https://github.com/boostorg/geometry/pull/282#discussion_r29025344  

And here.

---

## Comment 5

> Username: mkaravel  
> Created_at: 2015-04-24 06:01:14 UTC  
> Updated_at: 2015-04-24 11:28:33 UTC  
> Url: https://github.com/boostorg/geometry/pull/282#discussion_r29025401  

It seems here that interior rings are copied.  
Is that a good idea? I think we should avoid the copying. We have already faced similar issues in the library and there is a sort-of standard way for addressing this.

---

## Comment 6

> Username: mkaravel  
> Created_at: 2015-04-24 06:03:54 UTC  
> Updated_at: 2015-04-24 11:28:33 UTC  
> Url: https://github.com/boostorg/geometry/pull/282#discussion_r29025479  

Here and below.  
Why the `OutputIterator` is passed by value rather then by reference as in the original code?  
I prefer the reference approach, as with the value approach it is not possible to recover the iterator value after the call to this method.

---

## Comment 7

> Username: mkaravel  
> Created_at: 2015-04-24 06:13:45 UTC  
> Updated_at: 2015-04-24 11:28:33 UTC  
> Url: https://github.com/boostorg/geometry/pull/282#discussion_r29025771  

The general paradigm we have used for handling this is as follows.  
  
```  
struct my_struct  
{  
    template <typename InteriorRingIterator>  
    static void do_the_work(InteriorRingIterator first, InteriorRingIterator last)  
    {  
        for (InteriorRingIterator it = first; it != last; ++it)  
        {  
            // do what is needed  
        }  
    }  
  
    template <typename InteriorRings>  
    static void do_the_work(InteriorRings const& interior_rings)  
    {  
        do_the_work(boost::begin(interior_rings), boost::end(interior_rings));  
    }  
  
    static void apply(...)  
    {  
        // this is the initial caller  
        do_the_work(geometry::interior_rings(polygon));  
    }  
};  
```

---

## Comment 8

> Username: mkaravel  
> Created_at: 2015-04-24 06:14:15 UTC  
> Updated_at: 2015-04-24 11:28:33 UTC  
> Url: https://github.com/boostorg/geometry/pull/282#discussion_r29025787  

Line 197 is too long and should be wrapped.

---

## Comment 9

> Username: awulkiew  
> Created_at: 2015-04-24 11:42:37 UTC  
> Url: https://github.com/boostorg/geometry/pull/282#discussion_r29042037  

It's because otherwise it wouldn't work on a standard-conformant compiler for a r-value type passed as the iterator. In the tests `std::back_insert_iterator` returned by `std::back_inserter()` is passed into this function which doesn't compile e.g. on GCC. This is why the iterators in the STL algorithms are always passed by value and if needed returned from the function.  
  
In this particular case I guess the iterator could be passed by reference into the `dispatch::write_wkb()` but still it must be passed by value into the `geometry::write_wkb()`. I passed it by value also in the internals the same way how it's done in the STL.

---

## Comment 10

> Username: awulkiew  
> Created_at: 2015-04-24 11:46:05 UTC  
> Url: https://github.com/boostorg/geometry/pull/282#discussion_r29042193  

Good catch!  
I just used the correct `..._return_type` for them to not modify too much.

---

## Comment 11

> Username: mkaravel  
> Created_at: 2015-04-24 12:56:14 UTC  
> Url: https://github.com/boostorg/geometry/pull/282#discussion_r29045644  

Great! Thanks for making the change.  
`interior_return_type` is indeed better and should avoid the problem I had in mind.

---

## Comment 12

> Username: mkaravel  
> Created_at: 2015-04-24 13:07:14 UTC  
> Url: https://github.com/boostorg/geometry/pull/282#issuecomment-95928000  

@awulkiew Thanks for the fixes.  
  
I understand your line of thought about the output iterator.  
Most probably the corresponding methods would need to be re-designed and the output iterator returned somehow (either as is and the boolean passed as an argument by reference, or returning an `std::pair<OutputIterator, bool>`, or something else). I fear this is going too far for this right now, and most probably it is not really required as I do not see how the output iterator returned would be used in this case anyway. Let's leave it as it is for now, and if the need comes up we can do the re-design.  
  
I am okay with merging.

---

## Comment 13

> Username: awulkiew  
> Created_at: 2015-04-24 13:12:54 UTC  
> Url: https://github.com/boostorg/geometry/pull/282#issuecomment-95929535  

Ok, thanks!

---

## Comment 14

> Username: barendgehrels  
> Created_at: 2015-04-24 18:08:09 UTC  
> Url: https://github.com/boostorg/geometry/pull/282#discussion_r29071545  

enum_t is not introduced here, but it does not follow any of our conventions

---

## Comment 15

> Username: barendgehrels  
> Created_at: 2015-04-24 18:09:45 UTC  
> Url: https://github.com/boostorg/geometry/pull/282#discussion_r29071681  

Indentation is changed. If so, please follow conventions then, angle-bracket new line, last one too

---

## Comment 16

> Username: barendgehrels  
> Created_at: 2015-04-24 18:10:11 UTC  
> Updated_at: 2015-04-24 18:11:07 UTC  
> Url: https://github.com/boostorg/geometry/pull/282#discussion_r29071729  

Looks good

---

## Comment 17

> Username: barendgehrels  
> Created_at: 2015-04-24 18:12:20 UTC  
> Url: https://github.com/boostorg/geometry/pull/282#discussion_r29071925  

Instead, we usually leave parameter names out here

---

## Comment 18

> Username: barendgehrels  
> Created_at: 2015-04-24 18:12:37 UTC  
> Url: https://github.com/boostorg/geometry/pull/282#discussion_r29071952  

Linestring

---

## Comment 19

> Username: awulkiew  
> Created_at: 2015-04-25 14:44:07 UTC  
> Url: https://github.com/boostorg/geometry/pull/282#discussion_r29100978  

What do you mean? `enum_t` is a name of a type defined in `geometry_type_ogc`. Do you think that the name of a type should be changed to something else? Or should it be defined in a global scope? Is this is a question to the original author?

---

## Comment 20

> Username: barendgehrels  
> Created_at: 2015-04-25 15:31:20 UTC  
> Url: https://github.com/boostorg/geometry/pull/282#discussion_r29101303  

I know it is not part of this review (therefore I wrote "not introduced here"). So yes, it is a remark to the original author (probably Mateusz or Mats).  
OK, inspecting the code it is probably part of the scoped enumerations from Boost. You can ignore this remark.

---

## Comment 21

> Username: awulkiew  
> Created_at: 2015-04-25 22:53:43 UTC  
> Url: https://github.com/boostorg/geometry/pull/282#discussion_r29104897  

Ok, I addressed all of your suggestions (and more) in this commit: https://github.com/boostorg/geometry/commit/a48df2a15338cc4e38c30496f1678435c2c6faca

---

## Comment 22

> Username: barendgehrels  
> Created_at: 2015-04-26 11:58:41 UTC  
> Url: https://github.com/boostorg/geometry/pull/282#discussion_r29109106  

Thanks!

---
