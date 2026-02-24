# #171 Fix/strong type checking for cs template parameter [Merged]

> Username: mkaravel  
> Created at: 2014-10-29 11:52:04 UTC  
> Updated at: 2014-10-31 13:54:22 UTC  
> Merged at: 2014-10-31 13:21:38 UTC  
> Closed at: 2014-10-31 13:21:38 UTC  
> Url: https://github.com/boostorg/geometry/pull/171  

Implement a strict type checking mechanism for the template parameter of the non-Cartesian coordinate systems. The goal is to allow the instantiation of `bg::cs::spherical<T>`, `bg::cs::geographic<T>`, etc., only when `T` is either `bg::degree` or `bg::radian`.  
  
Note that, even with the stronger type checking mechanism mentioned above, since the coordinate system is never really created when a `bg::model::point<>` is created, it is possible to write something like:  
  
```  
bg::model::point<double, 2, bg::cs::geographic<double> > p;  
```  
  
By making `bg::model::point<>` derive privately from the coordinate system, the above syntax is no longer accepted (see second commit).

---

## Comment 1

> Username: barendgehrels  
> Created_at: 2014-10-29 12:16:57 UTC  
> Updated_at: 2014-10-31 07:54:12 UTC  
> Url: https://github.com/boostorg/geometry/pull/171#discussion_r19533432  

It is not a dispatch (dispatched-by-tag), it is a specialization, there are no tags involved

---

## Comment 2

> Username: barendgehrels  
> Created_at: 2014-10-29 12:18:57 UTC  
> Updated_at: 2014-10-31 07:54:12 UTC  
> Url: https://github.com/boostorg/geometry/pull/171#discussion_r19533511  

This will work but I'm not in favor of deriving the class for the sake of testing only. If we extend this we get multiple inheritance, etc, while inheritance is effectively not used at all here.  
  
It should be possible to check in another way too, e.g. defining a private class-type doing this check

---

## Comment 3

> Username: barendgehrels  
> Created_at: 2014-10-29 12:20:15 UTC  
> Updated_at: 2014-10-31 07:54:12 UTC  
> Url: https://github.com/boostorg/geometry/pull/171#discussion_r19533558  

Same here - but even more influence on the whole library, I would not derive the point from another class...  
Is the checking here necessary? Because the coordinate system as a parameter should already be checked.

---

## Comment 4

> Username: barendgehrels  
> Created_at: 2014-10-29 12:21:45 UTC  
> Url: https://github.com/boostorg/geometry/pull/171#issuecomment-60914205  

Hi Menelaos,  
Thanks for the PR - can you look at the comments, and add a unit test too which should fail if the wrong type is passed and succeeds for degree/radian?

---

## Comment 5

> Username: mkaravel  
> Created_at: 2014-10-29 13:08:52 UTC  
> Updated_at: 2014-10-31 07:54:12 UTC  
> Url: https://github.com/boostorg/geometry/pull/171#discussion_r19535651  

You are right. Very good point. Would `core_detail` be okay then?

---

## Comment 6

> Username: mkaravel  
> Created_at: 2014-10-29 14:25:30 UTC  
> Updated_at: 2014-10-31 07:54:12 UTC  
> Url: https://github.com/boostorg/geometry/pull/171#discussion_r19540559  

Okay, understood. I will do this another way.

---

## Comment 7

> Username: mkaravel  
> Created_at: 2014-10-29 14:31:15 UTC  
> Updated_at: 2014-10-31 07:54:12 UTC  
> Url: https://github.com/boostorg/geometry/pull/171#discussion_r19541031  

It depends on the level of type checking you want to do.  
If you want to disallow BG points with wrongly defined coordinate systems to be even defined then something has to be done within BG point.  
One alternative is to write the constructor as follows:  
  
```  
inline point()  
{  
  CoordinateSystem cs;  
  boost::ignore_unused(cs);  
}  
```  
  
and similarly for the other constructor. This will have the same checking effect. Probably not a good idea though.  
  
If you consider this as too much, then we can simply leave point as it is and rely on other places in the library to trigger the check. If we leave point as it is (without derivation), it would be possible to write what I wrote in my initial comment above. But then once you use this point in an algorithm, you almost certainly get a compile time error.  
  
Again, it really depends on the level of checking we want to do. I would be okay with leaving the point class as it is.

---

## Comment 8

> Username: mkaravel  
> Created_at: 2014-10-29 14:32:21 UTC  
> Url: https://github.com/boostorg/geometry/pull/171#issuecomment-60933529  

Should this unit test be in `test/geometries`? How should I handle the fact that it would fail at compile time (from the point of view of boost testing)?

---

## Comment 9

> Username: barendgehrels  
> Created_at: 2014-10-29 15:16:09 UTC  
> Updated_at: 2014-10-31 07:54:12 UTC  
> Url: https://github.com/boostorg/geometry/pull/171#discussion_r19544654  

Sure, core_detail is fine

---

## Comment 10

> Username: barendgehrels  
> Created_at: 2014-10-29 15:20:51 UTC  
> Url: https://github.com/boostorg/geometry/pull/171#issuecomment-60942869  

unit test: see for example https://github.com/boostorg/geometry/blob/develop/test/point_concept/point_with_incorrect_dimension.cpp  
or basically most tests in the point_concept folder, the Jamfile contains  compile-fail for a failing testfile (it is file-based, so you have to provide two separate files)

---

## Comment 11

> Username: barendgehrels  
> Created_at: 2014-10-29 15:25:54 UTC  
> Updated_at: 2014-10-31 07:54:12 UTC  
> Url: https://github.com/boostorg/geometry/pull/171#discussion_r19545663  

OK I see your point, indeed it is possible to declare a point and do (probably nothing) with it.  
I prefer your version with the constructor. Because it is a declaration only, it is zero-time.  
You can probably also declare a non-used member variable in the class itself (edit: if it is zero-sized, otherwise the one in the constructor is better) (edit: or a typedef?)  
  
Another way (more work) is that we define a Concept for all spherical-based coordinate systems, and one of the rules is that the units are degree/radian. We can then perform a concept check. I don't know if we should really do this, maybe it is going too far. So let's not do that now but we can keep it in mind.

---

## Comment 12

> Username: mkaravel  
> Created_at: 2014-10-29 16:23:57 UTC  
> Updated_at: 2014-10-31 07:54:12 UTC  
> Url: https://github.com/boostorg/geometry/pull/171#discussion_r19550385  

I do not want to add a member variable; it will take space. This was one reason why I initially chose to derive, having in mind empty base class optimization.  
The typedef seems like a good alternative. If I can make it to work, I will prefer this.  
  
I agree with the concept check idea. Let's, indeed, leave this for later.

---

## Comment 13

> Username: mkaravel  
> Created_at: 2014-10-29 16:28:46 UTC  
> Updated_at: 2014-10-31 07:54:12 UTC  
> Url: https://github.com/boostorg/geometry/pull/171#discussion_r19550801  

A few more thoughts: doing this type checking in BG point does not mean that other user-defined points will behave the same. So we basically enforce strong type checking in our own point, but cannot do it in user-defined points.  
  
This is one more reason why should go towards a concept check approach. We will then be able to check all point types passed to the algorithms for the correct definition of the coordinate system.

---

## Comment 14

> Username: barendgehrels  
> Created_at: 2014-10-29 18:56:12 UTC  
> Updated_at: 2014-10-31 07:54:12 UTC  
> Url: https://github.com/boostorg/geometry/pull/171#discussion_r19563127  

Agreed. If we check the coordinate system in the PointConcept check, we're there. All algorithms (at least: that is the intention) check geometry concepts. So the check is then OK for our point-type and user-defined types.  
  
I think you can therefore leave the point class as it is now, no check necessary (though a typedef would not do any harm)

---

## Comment 15

> Username: mkaravel  
> Created_at: 2014-10-30 12:57:53 UTC  
> Url: https://github.com/boostorg/geometry/pull/171#issuecomment-61086897  

**Update**  
- The `units` type of non-Cartesian CS's is now defined through a typedef, so there is no inheritance involved.  
- Type checking wrt to the CS units for `bg::point<>` is now done through a private typedef; so no inheritance from the coordinate system type and no construction of objects in the constructors.  
- The point concept (both const and non-const) has been modified: there is a new typedef added for the coordinate system units, which enables concept checking for the units of the coordinate system.  
- Compile-success/compile-failure unit tests have been added for checking the units of the point defined.

---

## Comment 16

> Username: awulkiew  
> Created_at: 2014-10-30 13:34:11 UTC  
> Updated_at: 2014-10-31 07:54:12 UTC  
> Url: https://github.com/boostorg/geometry/pull/171#discussion_r19604328  

Actually the condition in the `MPL_ASSERT` isn't needed since this version will be instantiated for types different than `degree` or `radian`. A simple `false` could be explicitly used there, or does this condition have some other purpose?

---

## Comment 17

> Username: awulkiew  
> Created_at: 2014-10-30 13:48:12 UTC  
> Updated_at: 2014-10-31 07:54:12 UTC  
> Url: https://github.com/boostorg/geometry/pull/171#discussion_r19605094  

Why is this helper needed? Wouldn't it be sufficient to define a CS in the Point Concept and `model::point<>`? Shouldn't it itself just fail to compile?

---

## Comment 18

> Username: mkaravel  
> Created_at: 2014-10-30 14:26:47 UTC  
> Updated_at: 2014-10-31 07:54:12 UTC  
> Url: https://github.com/boostorg/geometry/pull/171#discussion_r19607551  

Done

---

## Comment 19

> Username: mkaravel  
> Created_at: 2014-10-30 14:27:36 UTC  
> Updated_at: 2014-10-31 07:54:12 UTC  
> Url: https://github.com/boostorg/geometry/pull/171#discussion_r19607607  

What do you mean "define"? With a typedef?

---

## Comment 20

> Username: mkaravel  
> Created_at: 2014-10-30 14:28:25 UTC  
> Updated_at: 2014-10-31 07:54:12 UTC  
> Url: https://github.com/boostorg/geometry/pull/171#discussion_r19607667  

I am trying to access the `units` type of the CS. `bg::cartesian` does not define such a type, hence the helper....

---

## Comment 21

> Username: mkaravel  
> Created_at: 2014-10-30 14:31:17 UTC  
> Updated_at: 2014-10-31 07:54:12 UTC  
> Url: https://github.com/boostorg/geometry/pull/171#discussion_r19607855  

BTW, the point concept already defines through a typedef the coordinate system, and this is not enough.  
So I am asking the question again: what do you have in mind when writing "define a CS in the Point Concept"?

---

## Comment 22

> Username: awulkiew  
> Created_at: 2014-10-30 14:54:58 UTC  
> Updated_at: 2014-10-31 07:54:12 UTC  
> Url: https://github.com/boostorg/geometry/pull/171#discussion_r19609654  

Exactly, just like it was done in the Point Concept.  
  
```  
typedef typename coordinate_system<Geometry>::type csystem;  
```  
  
and in `model::point<>`:  
  
```  
typedef CoordinateSystem csystem;  
```  
  
Isn't that enough? In case if it wasn't, must a member of CS be accessed (`unit`)? Couldn't CS be passed to some already existing template as parameter? E.g. something like:  
  
```  
typedef typename boost::mpl::identity  
    <  
        typename coordinate_system<Geometry>::type  
    >::type csystem;  
```  
  
or  
  
```  
typedef typename boost::mpl::identity<CoordinateSystem>::type csystem;  
```  
  
Then this file wouldn't be required.

---

## Comment 23

> Username: mkaravel  
> Created_at: 2014-10-30 15:08:23 UTC  
> Updated_at: 2014-10-31 07:54:12 UTC  
> Url: https://github.com/boostorg/geometry/pull/171#discussion_r19610600  

Neither one of your two suggestions works.

---

## Comment 24

> Username: awulkiew  
> Created_at: 2014-10-30 21:51:47 UTC  
> Updated_at: 2014-10-31 07:54:12 UTC  
> Url: https://github.com/boostorg/geometry/pull/171#discussion_r19638996  

Ok, I have a solution in pure C++ and working on msvc10, msvc12 and (MinGW) gcc4.9:  
  
```  
enum { cscheck = sizeof(CoordinateSystem) };  
```

---

## Comment 25

> Username: mkaravel  
> Created_at: 2014-10-30 21:58:29 UTC  
> Updated_at: 2014-10-31 07:54:12 UTC  
> Url: https://github.com/boostorg/geometry/pull/171#discussion_r19639352  

I will try it and get back to you.

---

## Comment 26

> Username: mkaravel  
> Created_at: 2014-10-31 07:50:22 UTC  
> Url: https://github.com/boostorg/geometry/pull/171#issuecomment-61228720  

@awulkiew Your latest suggestion worked! Thanks for the hint.  
I have committed the updated version following your suggestion.

---

## Comment 27

> Username: mkaravel  
> Created_at: 2014-10-31 07:56:07 UTC  
> Url: https://github.com/boostorg/geometry/pull/171#issuecomment-61229088  

**Update**  
- In the point concept, the typedef to the coordinate system units has been replaced by an enum storing the size of the coordinate system class. This has the same checking effect with the typedef previously used, but it is much simpler (solution hinted by Adam Wulkiewicz).  
  
I believe that the PR is now ready for merging.

---
