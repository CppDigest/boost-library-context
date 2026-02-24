# #150 - Elide the temporary container created when returning from algorithms [Open]

> Username: ldionne  
> Created at: 2015-06-22 19:45:50 UTC  
> Updated at: 2015-11-13 17:49:46 UTC  
> Url: https://github.com/boostorg/hana/issues/150  

When we write something like  
  
``` c++  
auto xs = make_tuple(...);  
auto ys = transform(xs, f);  
```  
  
A temporary tuple is created and then move-assigned to `ys`. This should be elided most of the time, but providing a way to make sure that this is always the case (without relying on the optimizer) would be nice. This can be achieved e.g. by providing mutating algorithms:  
  
``` c++  
auto xs = make_tuple(...);  
Result ys;  
  
length(xs).times.with_index([&](auto i) {  
    ys[i] = f(xs[i]);  
});  
```  
  
There are probably other ways to achieve this too. See the [original discussion](http://thread.gmane.org/gmane.comp.lib.boost.devel/261077/focus=261308) on the Boost.Devel mailing list.

---

## Comment 1

> Username: ldionne  
> Created at: 2015-11-11 12:30:45 UTC  
> Url: https://github.com/boostorg/hana/issues/150#issuecomment-155764821  

I think this issue is unsolvable without either  
1. Introducing mutable versions of all algorithms where it makes sense. I'm not going to do this because these algorithms wouldn't be sufficiently composable, and it would represent too much of a big leap for Hana for the sake of one optimization.  
2. Relying on the fact that in practice, any decent compiler should elide the copy.  
  
Since [P0135R0](http://www.open-std.org/jtc1/sc22/wg21/docs/papers/2015/p0135r0.html) is in the works for C++17, I'd rather rely on this.  
  
@tzlaine: Since you were the original person to ask for this on the mailing list, does relying on P0135R0 seems reasonable to you? One of your comment on the mailing list was   
  
> No, I'm trying to get rid of the temporary altogether.  We all know that copies of temporaries get RVO'd out of code like the above a lot of the time, _but not always_.  I want a guarantee that I don't need to rely on RVO in a particular case, if efficiency is critical.  
  
If RVO became _mandatory_, would it be satisfying?

---

## Comment 2

> Username: tzlaine  
> Created at: 2015-11-11 13:17:06 UTC  
> Url: https://github.com/boostorg/hana/issues/150#issuecomment-155783658  

P0135R0 definitely helps.  However, I don't think the compiler is capable  
of applying RVO to unbounded call depths.  I'm sure I can make calls into  
the Hana interface that result in temporaries, even if P0135R0 becomes  
standard.  
  
Mutating operations are simply necessary in efficient C++ code.  
  
If that's out of scope for Hana, then that's just what we'll have to live  
with.  But it almost certainly will not be optimal in many, many cases.  
  
Zach  
  
On Wed, Nov 11, 2015 at 6:30 AM, Louis Dionne notifications@github.com  
wrote:  
  
> I think this issue is unsolvable without either  
> 1. Introducing mutable versions of all algorithms where it makes  
>    sense. I'm not going to do this because these algorithms wouldn't be  
>    sufficiently composable, and it would represent too much of a big leap for  
>    Hana for the sake of one optimization.  
> 2. Relying on the fact that in practice, any decent compiler should  
>    elide the copy.  
>   
> Since P0135R0  
> http://www.open-std.org/jtc1/sc22/wg21/docs/papers/2015/p0135r0.html is  
> in the works for C++17, I'd rather rely on this.  
>   
> @tzlaine https://github.com/tzlaine: Since you were the original person  
> to ask for this on the mailing list, does relying on P0135R0 seems  
> reasonable to you? One of your comment on the mailing list was  
>   
> No, I'm trying to get rid of the temporary altogether. We all know that  
> copies of temporaries get RVO'd out of code like the above a lot of the  
> time, _but not always_. I want a guarantee that I don't need to rely on  
> RVO in a particular case, if efficiency is critical.  
>   
> If RVO became _mandatory_, would it be satisfying?  
>   
> —  
> Reply to this email directly or view it on GitHub  
> https://github.com/boostorg/hana/issues/150#issuecomment-155764821.

---

## Comment 3

> Username: ldionne  
> Created at: 2015-11-11 16:16:57 UTC  
> Updated at: 2015-11-11 16:17:30 UTC  
> Url: https://github.com/boostorg/hana/issues/150#issuecomment-155832011  

Indeed, I just re-convinced myself that I was wrong. Consider the following example, where `x`,`y` and `z` are expensive to copy and/or move. This is mostly for my future reference so I don't fall back into the trap of thinking that RVO will be enough:  
  
``` c++  
auto tuple = hana::make_tuple(x, y, z);  
  
auto f() {  
    return hana::transform(tuple, some_function1);  
}  
  
auto g() {  
    return hana::transform(f(), some_function2);  
}  
  
auto result = g();  
```  
  
If RVO is applied, then the above code is equivalent to   
  
``` c++  
auto tuple = hana::make_tuple(x, y, z);  
  
auto g() {  
    auto result_of_f = hana::transform(tuple, some_function1); // RVO-ed from f()  
    return hana::transform(std::move(result_of_f), some_function2);  
}  
  
auto result = g();  
```  
  
which in turn is equivalent to   
  
``` c++  
auto tuple = hana::make_tuple(x, y, z);  
auto result_of_f = hana::transform(tuple, some_function1);  
auto result = hana::transform(std::move(result_of_f), some_function2); // RVO-ed from g()  
```  
  
The problem is that we still end up with a move of `result_of_f`, even if RVO is both mandatory and applied transitively. Instead, with a view, what you would get is  
  
``` c++  
auto tuple = hana::make_tuple(x, y, z);  
  
auto f() {  
    return hana::transform_view(tuple, some_function1);  
}  
  
auto g() {  
    return hana::transform_view(f(), some_function2);  
}  
  
auto result = g();  
```  
  
which is RVO-equivalent to   
  
``` c++  
auto tuple = hana::make_tuple(x, y, z);  
auto result_of_f = hana::transform_view(tuple, some_function1);  
auto lazy_result = hana::transform_view(result_of_f, some_function2);  
```  
  
Now, however, when you evaluate the result,  
  
``` c++  
auto result = evaluate(lazy_result);  
```  
  
what you get is equivalent to the following:  
  
``` c++  
auto result = hana::make_tuple(  
    some_function2(some_function1(tuple[0])),  
    some_function2(some_function1(tuple[1]))  
    some_function2(some_function1(tuple[2]))  
);  
```  
  
which indeed does not create any temporary. Of course, there are some downsides to views, but it seems like it would be useful to support them in the future.  
  
@tzlaine Does my analysis of where the temporaries are created make sense to you?

---

## Comment 4

> Username: tzlaine  
> Created at: 2015-11-11 16:51:14 UTC  
> Url: https://github.com/boostorg/hana/issues/150#issuecomment-155843687  

On Wed, Nov 11, 2015 at 10:16 AM, Louis Dionne notifications@github.com  
wrote:  
  
> Indeed, I just re-convinced myself that I was wrong. Consider the  
> following example, where x,y and z are expensive to copy and/or move.  
> This is mostly for my future reference so I don't fall back into the trap  
> of thinking that RVO will be enough:  
>   
> auto tuple = hana::make_tuple(x, y, z);  
> auto f() {  
>     return hana::transform(tuple, some_function1);  
> }  
> auto g() {  
>     return hana::transform(f(), some_function2);  
> }  
> auto result = g();  
>   
> If RVO is applied, then the above code is equivalent to  
>   
> auto tuple = hana::make_tuple(x, y, z);  
> auto g() {  
>     auto result_of_f = hana::transform(tuple, some_function1); // RVO-ed from f()  
>     return hana::transform(std::move(result_of_f), some_function2);  
> }  
> auto result = g();  
>   
> which in turn is equivalent to  
>   
> auto tuple = hana::make_tuple(x, y, z);auto result_of_f = hana::transform(tuple, some_function1);auto result = hana::transform(std::move(result_of_f), some_function2); // RVO-ed from g()  
>   
> The problem is that we still end up with a move of result_of_f, even if  
> RVO is both mandatory and applied transitively. Instead, with a view, what  
> you would get is  
>   
> auto tuple = hana::make_tuple(x, y, z);  
> auto f() {  
>     return hana::transform_view(tuple, some_function1);  
> }  
> auto g() {  
>     return hana::transform_view(f(), some_function2);  
> }  
> auto result = g();  
>   
> which is RVO-equivalent to  
>   
> auto tuple = hana::make_tuple(x, y, z);auto result_of_f = hana::transform_view(tuple, some_function1);auto lazy_result = hana::transform_view(result_of_f, some_function2);  
>   
> Now, however, when you evaluate the result,  
>   
> auto result = evaluate(lazy_result);  
>   
> what you get is equivalent to the following:  
>   
> auto result = hana::make_tuple(  
>     some_function2(some_function1(tuple[0])),  
>     some_function2(some_function1(tuple[1]))  
>     some_function2(some_function1(tuple[2]))  
> );  
>   
> which indeed does not create any temporary. Of course, there are some  
> downsides to views, but it seems like it would be useful to support them in  
> the future.  
  
Right.  Furthermore, views are often a pessimization too.  Consider a  
view-version of STL range algorithms:  
  
MySeq seq = {a, b, c, d, ..., z};  
sort(seq); // mutates in-place  
transform(seq, out_iterator); // access is in-order and cache-friendly  
  
versus:  
  
MySeq seq = {a, b, c, d, ..., z};  
auto sorted = sort_view(seq); // (ignore the cost of this copy; it could  
just as easily have been inlined below)  
transform(sorted, out_iterator); // access is out-of-order and  
cache-unfriendly in the general case  
  
I care a lot about cache coherency.  
  
My perfect Hana interface would have a mutating overload for each  
possibly-mutating algorithm (some of them should not mutate anything).  
  
So if I want functional, I could write:  
  
result1 = sort(seq);  
result2 = transform(seq);  
  
or, if I want mutating:  
  
sort(seq);  
transform(seq, out_seq);  
  
Note that without a bit of work, the sort() overloads above are ambiguous.  
This could be fixed with a "mutating" namespace, by tagging the type of  
"seq" above as being mutable, etc.  The exact mechanism is not that  
important, but I really want to be able to use the moral equivalent of  
std::sort(), std::transform(), etc., on Hana tuples.  
  
Zach

---

## Comment 5

> Username: ldionne  
> Created at: 2015-11-11 17:14:06 UTC  
> Url: https://github.com/boostorg/hana/issues/150#issuecomment-155849961  

> sort(seq);  
  
Sorting in place is simply impossible in the general case, since the type of the sorted sequence may differ from the type of the original sequence. Hence, sorting in-place only makes sense when you have e.g. `hana::tuple<int, int, int>` (all with the same type). But then, you probably should be using a `std::array`.  
  
> transform(seq, out_seq);  
  
Regarding a mutating `transform`, I don't see any difference between  
  
``` c++  
auto tuple = hana::make_tuple(x, y, z);  
hana::tuple<T, U, V> result; // suppose you can predict the type of the resulting tuple  
hana::transform_mutate(tuple, function, result);  
```  
  
and  
  
``` c++  
auto tuple = hana::make_tuple(x, y, z);  
auto result = hana::transform(tuple, function);  
```  
  
assuming RVO applies. Actually, the difference is that in the first case, you have to default-construct `result`, which might even be less efficient.  
  
Don't get me wrong; I'm not trying to argue against you just for the sake of it. What I'm really trying to do is to go through the thought process of making the algorithms mutable, and see if that makes sense.  
  
I'm not sure, but I think that heterogeneous sequences make those mutable operations not-so-well-defined in the general case, and equivalent to `std` algorithms in the special cases.

---

## Comment 6

> Username: tzlaine  
> Created at: 2015-11-11 19:50:32 UTC  
> Url: https://github.com/boostorg/hana/issues/150#issuecomment-155891969  

On Wed, Nov 11, 2015 at 11:14 AM, Louis Dionne notifications@github.com  
wrote:  
  
> sort(seq);  
>   
> Sorting in place is simply impossible in the general case, since the type  
> of the sorted sequence may differ from the type of the original sequence.  
> Hence, sorting in-place only makes sense when you have e.g. hana::tuple<int,  
> int, int> (all with the same type). But then, you probably should be  
> using a std::array.  
>   
> Right. sort() is a bad example.  Pretend I wrote filter() and the issue  
> remains the same.  
>   
> transform(seq, out_seq);  
>   
> Regarding a mutating transform, I don't see any difference between  
>   
> auto tuple = hana::make_tuple(x, y, z);  
> hana::tuple<T, U, V> result; // suppose you can predict the type of the resulting tuplehana::transform_mutate(tuple, function, result);  
>   
> and  
>   
> auto tuple = hana::make_tuple(x, y, z);auto result = hana::transform(tuple, function);  
>   
> assuming RVO applies. Actually, the difference is that in the first case,  
> you have to default-construct result, which might even be less efficient.  
>   
> RVO simply cannot be applied to some functions:  
  
void not_rvo_able (MySeq seq_a, MySeq seq_b)  
{  
    if (some_predicate())  
        return seq_a;  
    else  
        return seq_b;  
}  
  
Other optimizations may make the code above reduce to nearly nothing, but  
not RVO (SSA-style analysis probably kicks in above, but also probably not  
if the args are refs/pointers).  
  
The point is that if I can't mutate, I can't eliminate all  
temporaries/copies that are not strictly necessary.  
  
> Don't get me wrong; I'm not trying to argue against you just for the sake  
> of it. What I'm really trying to do is to go through the thought process of  
> making the algorithms mutable, and see if that makes sense.  
>   
> I get that completely.  I work the same way.  
>   
> I'm not sure, but I think that heterogeneous sequences make those mutable  
> operations not-so-well-defined in the general case, and equivalent to std  
> algorithms in the special cases.  
>   
> I agree.  It's completely case-by-case.  The ones that are isomorphic with  
> STL algorithms are the ones I probably want the most anyway though.  
  
Zach

---

## Comment 7

> Username: ldionne  
> Created at: 2015-11-11 21:25:47 UTC  
> Url: https://github.com/boostorg/hana/issues/150#issuecomment-155914958  

> Right. sort() is a bad example.  Pretend I wrote filter() and the issue remains the same.  
  
`filter` will also not work in-place, because the type of the filtered sequence may change. Consider for example  
  
``` c++  
hana::tuple<int, double, char> tuple{1, 3.4, 'x'};  
hana::tuple<double> result = hana::filter(tuple, hana::traits::is_floating_point);  
```  
  
Actually, I think that none of the algorithms can be in-place in the general case. Going through the important algorithms in the cheatsheet,  
1. `transform`: In the general case, the type of each element may change so transforming in-place would be a special case.  
2. `drop_front`: Can only be in-place when we don't drop anything, since the number of elements in the tuple is encoded in its type.  
3. `filter`: Explained above  
4. `sort`: Explained above  
5. `remove_at`: The type of the resulting sequence will change, since the size of the sequence is encoded in its type.  
6. `reverse`: Again, it is not possible to reverse in-place, since `hana::tuple<int, char, float> != hana::tuple<float, char, int>`.  
  
More generally, we can see that any algorithm that changes the size of the original sequence is bound not to be implementable in-place, because that size is encoded in the type of the sequence. Also, any algorithm that reorders the elements of a sequence can't have an in-place version, unless the reordering is done in such a way that the types of the initial sequence are respected. For example, consider  
  
``` c++  
hana::tuple<int, char, int> tuple{1, 'x', 3};  
auto result = f(tuple); // some reordering of the tuple  
```  
  
The only way `f` can ever be implemented in-place is if `f` is either the identity function, or if it swaps the two `int`s together (and perhaps apply a type-preserving transformation to the elements). If we remove all the algorithms that change the size of the sequence or reorder elements, we're left with the algorithms from `Functor`. Do you agree with me up to this point?  
  
Now, since all those algorithms from `Functor` can be implemented very easily in terms of `transform`, let's just consider `transform`. As I said above, `transform` can only be implemented in-place if the function it maps over the sequence is type-preserving. However, it is very easy to implement such an in-place transformation as:  
  
``` c++  
auto tuple = hana::make_tuple(x, y, z);  
hana::for_each(tuple, [](auto& x) {  
    x = f(x); // x is a reference, so we mutate in-place  
});  
```  
  
Since it only applies to these algorithms from `Functor`, and since they are so easy to implement with `for_each`, I do not think it would be wise to provide then as part of Hana's interface. I would also argue that whatever the name we might pick for such an in-place `transform`, nothing will be as clear as writing the above `for_each`.  
  
> RVO simply cannot be applied to some functions:  
>   
> [...]  
  
I know, but my point was that RVO trivially applies to `hana::transform`. Let's clarify things. For any given algorithm, we have four ways of implementing it:  
1. Non-mutating version, as-if:  
     
   ``` c++  
   template <typename Tuple, typename F>  
   auto transform(Tuple&& tuple, F const& f) {  
       return hana::make_tuple(f(tuple[0]), f(tuple[1]), ..., f(tuple[n]));  
   }  
   ```  
2. Mutating version, as-if:  
     
   ``` c++  
   template <typename Tuple, typename F, typename Result>  
   void transform_mutate(Tuple&& tuple, F const& f, Result& result) {  
       result[0] = f(tuple[0]);  
       result[1] = f(tuple[1]);  
       ...  
       result[n] = f(tuple[n]);  
   }  
   ```  
3. In-place version, as-if:  
     
   ``` c++  
   template <typename Tuple, typename F>  
   void transform_inplace(Tuple& tuple, F const& f) {  
       result[0] = f(result[0]);  
       result[1] = f(result[1]);  
       ...  
       result[n] = f(result[n]);  
   }  
   ```  
4. Lazy version, as-if:  
     
   ``` c++  
   template <typename Tuple, typename F>  
   auto transform_lazy(Tuple& tuple, F const& f) {  
       return some_view_that_does_the_computation_lazily(tuple, f);  
   }  
   ```  
  
First, we have already considered views, and I think we agree that they can be useful. This is on the roadmap for a future version of Hana.  
  
Secondly, I have argued above that in-place algorithms are impossible to implement for heterogeneous sequences in the general case.  
  
Thirdly, there's our discussion about mutating versus non-mutating algorithms. My claim is that both are strictly equivalent, assuming a trivial RVO kicks in. Indeed, if you look at the definition of the non-mutating `transform`, since the body is only a return statement, the compiler should trivially apply RVO there (do you agree?). And if P0135R0 is adopted, then this easy-to-apply RVO becomes mandatory, and we're not just relying on QOI anymore. Hence, assuming that RVO for `transform` is applied, the following code snippets should be equivalent:  
  
``` c++  
auto tuple = hana::make_tuple(x, y, z);  
hana::tuple<T, U, V> result;  
hana::transform_mutate(tuple, function, result);  
```  
  
and  
  
``` c++  
auto tuple = hana::make_tuple(x, y, z);  
auto result = hana::transform(tuple, function);  
```  
  
Do you agree with this third point, or am I making a factual mistake somewhere? If you do agree with the third point, then the only thing left to do would be to confirm that Hana's non-mutating algorithms are indeed implemented in a way that allows RVO to happen. If so, then providing mutating algorithms is not necessary. If not, then we should implement the algorithms in a different way that takes advantage of RVO.

---

## Comment 8

> Username: tzlaine  
> Created at: 2015-11-12 14:57:50 UTC  
> Url: https://github.com/boostorg/hana/issues/150#issuecomment-156128839  

On Wed, Nov 11, 2015 at 3:25 PM, Louis Dionne notifications@github.com  
wrote:  
  
> Right. sort() is a bad example. Pretend I wrote filter() and the issue  
> remains the same.  
>   
> filter will also not work in-place, because the type of the filtered  
> sequence may change. Consider for example  
>   
> hana::tuple<int, double, char> tuple{1, 3.4, 'x'};  
> hana::tuple<double> result = hana::filter(tuple, hana::traits::is_floating_point);  
>   
> Actually, I think that none of the algorithms can be in-place in the  
> general case. Going through the important algorithms in the cheatsheet,  
> 1. transform: In the general case, the type of each element may change  
>    so transforming in-place would be a special case.  
> 2. drop_front: Can only be in-place when we don't drop anything, since  
>    the number of elements in the tuple is encoded in its type.  
> 3. filter: Explained above  
> 4. sort: Explained above  
> 5. remove_at: The type of the resulting sequence will change, since  
>    the size of the sequence is encoded in its type.  
> 6. reverse: Again, it is not possible to reverse in-place, since hana::tuple<int,  
>    char, float> != hana::tuple<float, char, int>.  
>   
> More generally, we can see that any algorithm that changes the size of the  
> original sequence is bound not to be implementable in-place, because that  
> size is encoded in the type of the sequence. Also, any algorithm that  
> reorders the elements of a sequence can't have an in-place version, unless  
> the reordering is done in such a way that the types of the initial sequence  
> are respected. For example, consider  
>   
> hana::tuple<int, char, int> tuple{1, 'x', 3};auto result = f(tuple); // some reordering of the tuple  
>   
> The only way f can ever be implemented in-place is if f is either the  
> identity function, or if it swaps the two ints together (and perhaps  
> apply a type-preserving transformation to the elements). If we remove all  
> the algorithms that change the size of the sequence or reorder elements,  
> we're left with the algorithms from Functor. Do you agree with me up to  
> this point?  
>   
> Yes.  
>   
> Now, since all those algorithms from Functor can be implemented very  
> easily in terms of transform, let's just consider transform. As I said  
> above, transform can only be implemented in-place if the function it maps  
> over the sequence is type-preserving. However, it is very easy to implement  
> such an in-place transformation as:  
>   
> auto tuple = hana::make_tuple(x, y, z);hana::for_each(tuple, [](auto& x) {  
>     x = f(x); // x is a reference, so we mutate in-place  
> });  
>   
> Since it only applies to these algorithms from Functor, and since they  
> are so easy to implement with for_each, I do not think it would be wise  
> to provide then as part of Hana's interface. I would also argue that  
> whatever the name we might pick for such an in-place transform, nothing  
> will be as clear as writing the above for_each.  
>   
> Fair enough, but we're talking about slightly different things, I think.  
> The in-place issue is not my main concern; I was simply trying to show that  
> views jumble the order of access, making them cache-unfriendly.  I picked  
> sort() and filter() badly, but the main idea is that in some cases the  
> optimal thing to do is to copy so that later accesses are in-order (e.g.  
> with a large sequence of cheap-to-copy objects).  Sometimes the optimal  
> thing is to use a view (e.g. with a small sequence of expensive-to-copy,  
> copy-only objects).  Sometimes moves might be more appropriate (e.g. with a  
> small sequence of expensive-to-copy, moveable objects).  
  
For homogeneous sequences, the STL is still useful under all the usage  
conditions above and more, except for its lack of views.  Right now, all  
Hana does is copy.  I really want analogues of std::copy(), std::move(),  
std::copy_backward(), and std::move_backward() in Hana, as well as  
overloads of transform et al with an out-iterator.  
  
RVO simply cannot be applied to some functions:  
  
> [...]  
>   
> I know, but my point was that RVO trivially applies to hana::transform.  
> Let's clarify things. For any given algorithm, we have four ways of  
> implementing it:  
>   
> Wait, you're skipping over my point.  Consider:  
  
template <typename F>  
auto not_rvo_able (MySeq seq_a, MySeq seq_b, F f)  
{  
    if (some_predicate())  
        return transform(seq_a, F);  
    else  
        return transform(seq_b, F);  
}  
  
Ok, so now, why does it matter that transform() trivially gets RVO?  The  
point is that if I use copy-happy algorithms that are all RVO-able  
individually, I end up writing code that does not use RVO.  Even so, for  
many use cases this will be optimal or near-optimal code.  
  
Views help, but only if you can compute the type of the result beforehand:  
  
template <typename F>  
auto not_rvo_able (MySeq seq_a, MySeq seq_b, F f)  
{  
    decltype(transform_view(seq_a, f)) retval;  
    if (some_predicate())  
        retval = transform_view(seq_a, F);  
    else  
        retval = transform_view(seq_b, F);  
    return retval;  
}  
  
This is a bit awkward, but it works.  It is not appropriate if copies  
and/or views themselves are not appropriate, as I outlined above.  For many  
use cases though this will be exactly right.  
  
But for other cases, maybe this is the optimal code:  
  
some_other_t result;  
transform(some_predicate() ? seq_a : seq_b, F, result); // or begin(result)  
for the last arg, you get the idea  
  
Note that the code above is allowed to copy, move, construct something new,  
or even chose which to do on a per-element basis.  
  
The point is, I need the flexibility of choosing the right approach for my  
particular circumstances.  
  
>    1.  
>   
>    Non-mutating version, as-if:  
>   
>    template <typename Tuple, typename F>auto transform(Tuple&& tuple, F const& f) {  
>        return hana::make_tuple(f(tuple[0]), f(tuple[1]), ..., f(tuple[n]));  
>    }  
>   
>    2.  
>   
>    Mutating version, as-if:  
>   
>    template <typename Tuple, typename F, typename Result>void transform_mutate(Tuple&& tuple, F const& f, Result& result) {  
>        result[0] = f(tuple[0]);  
>        result[1] = f(tuple[1]);  
>        ...  
>        result[n] = f(tuple[n]);  
>    }  
>   
>    3.  
>   
>    In-place version, as-if:  
>   
>    template <typename Tuple, typename F>void transform_inplace(Tuple& tuple, F const& f) {  
>        result[0] = f(result[0]);  
>        result[1] = f(result[1]);  
>        ...  
>        result[n] = f(result[n]);  
>    }  
>   
>    4.  
>   
>    Lazy version, as-if:  
>   
>    template <typename Tuple, typename F>auto transform_lazy(Tuple& tuple, F const& f) {  
>        return some_view_that_does_the_computation_lazily(tuple, f);  
>    }  
>   
> First, we have already considered views, and I think we agree that they  
> can be useful. This is on the roadmap for a future version of Hana.  
>   
> Agreed.  
>   
> Secondly, I have argued above that in-place algorithms are impossible to  
> implement for heterogeneous sequences in the general case.  
>   
> Agreed.  
>   
> Thirdly, there's our discussion about mutating versus non-mutating  
> algorithms. My claim is that both are strictly equivalent, assuming a  
> trivial RVO kicks in. Indeed, if you look at the definition of the  
> non-mutating transform, since the body is only a return statement, the  
> compiler should trivially apply RVO there (do you agree?).  
>   
> Yes.  
>   
> And if P0135R0 is adopted, then this easy-to-apply RVO becomes mandatory,  
> and we're not just relying on QOI anymore. Hence, assuming that RVO for  
> transform is applied, the following code snippets should be equivalent:  
>   
> auto tuple = hana::make_tuple(x, y, z);  
> hana::tuple<T, U, V> result;hana::transform_mutate(tuple, function, result);  
>   
> and  
>   
> auto tuple = hana::make_tuple(x, y, z);auto result = hana::transform(tuple, function);  
>   
> Do you agree with this third point, or am I making a factual mistake  
> somewhere? If you do agree with the third point, then the only thing left  
> to do would be to confirm that Hana's non-mutating algorithms are indeed  
> implemented in a way that allows RVO to happen. If so, then providing  
> mutating algorithms is not necessary. If not, then we should implement the  
> algorithms in a different way that takes advantage of RVO.  
>   
> I agree that RVO will be mandatory and that that is a good thing.  It's  
> just not sufficient.  
  
Zach

---

## Comment 9

> Username: ldionne  
> Created at: 2015-11-13 16:05:39 UTC  
> Url: https://github.com/boostorg/hana/issues/150#issuecomment-156474247  

> > [...]  
> >   
> > Now, since all those algorithms from Functor can be implemented very  
> > easily in terms of transform, let's just consider transform. As I said  
> > above, transform can only be implemented in-place if the function it maps  
> > over the sequence is type-preserving. However, it is very easy to implement  
> > such an in-place transformation as:  
> >   
> > auto tuple = hana::make_tuple(x, y, z);  
> > hana::for_each(tuple, [](auto& x) {  
> >   x = f(x); // x is a reference, so we mutate in-place  
> > });  
> >   
> > Since it only applies to these algorithms from Functor, and since they  
> > are so easy to implement with for_each, I do not think it would be wise  
> > to provide then as part of Hana's interface. I would also argue that  
> > whatever the name we might pick for such an in-place transform, nothing  
> > will be as clear as writing the above for_each.  
>   
> Fair enough, but we're talking about slightly different things, I think.  
> The in-place issue is not my main concern; I was simply trying to show that  
> views jumble the order of access, making them cache-unfriendly. I picked  
> sort() and filter() badly, but the main idea is that in some cases the  
> optimal thing to do is to copy so that later accesses are in-order (e.g.  
> with a large sequence of cheap-to-copy objects). Sometimes the optimal  
> thing is to use a view (e.g. with a small sequence of expensive-to-copy,  
> copy-only objects). Sometimes moves might be more appropriate (e.g. with a  
> small sequence of expensive-to-copy, moveable objects).  
  
I understand, and I agree with you about the need to sometimes copy, sometimes move and sometimes be lazy.  
  
> For homogeneous sequences, the STL is still useful under all the usage  
> conditions above and more, except for its lack of views. Right now, all  
> Hana does is copy.  
  
If I understand correctly what you mean, this is incorrect. Hana will move when given a sequence that can be moved from, and it will copy otherwise. Consider the following example:  
  
``` c++  
auto tuple = hana::make_tuple(x, y, z);  
auto result = hana::transform(tuple, function);  
```  
  
Here, since `tuple` is passed to `transform` as an lvalue, it can't be moved-from and `function` will be called as `function(x)`, `function(y)`, and `function(z)`. However, consider the following:  
  
``` c++  
auto tuple = hana::make_tuple(x, y, z);  
auto result = hana::transform(std::move(tuple), function);  
```  
  
Now, `tuple` is passed to `transform` as an rvalue-reference, and `transform` will call `function` as `function(std::move(x))`, `function(std::move(y))`, and `function(std::move(z))`. If `function` takes advantage of move semantics, then you just moved your resources instead of copying them. This is the same for e.g. `sort`. If you write  
  
``` c++  
auto tuple = hana::make_tuple(x, y, z);  
auto result = hana::sort(std::move(tuple));  
```  
  
it will steal the elements from `tuple` and move-construct `result` with them.  
  
Is this what you meant?  
  
[Note that this is actually a lie for `sort` right now, but only because of a QOI issue. I _do_ have a more runtime-efficient implementation of `sort` locally, but it behaves badly at compile-time. I should fix this.]  
  
> I really want analogues of std::copy(), std::move(),  
> std::copy_backward(), and std::move_backward() in Hana, as well as  
> overloads of transform et al with an out-iterator.  
  
For `std::copy` and `std::move`, you can just write  
  
``` c++  
auto tuple = hana::make_tuple(x, y, z);  
auto a = tuple; // std::copy  
auto b = std::move(tuple); // std::move  
```  
  
If you want to do it across sequence types, just use:  
  
``` c++  
auto tuple = hana::make_tuple(x, y, z);  
auto a = hana::to<hana::ext::boost::fusion::vector_tag>(tuple); // std::copy  
auto b = hana::to<hana::ext::boost::fusion::vector_tag>(std::move(tuple)); // std::move  
```  
  
For the `*_backward` variants, I admit that there's no such thing out of the  
box in Hana. You could write it with a `for_each` with appropriate indices,  
but you'd have to write it by hand. Same goes for `transform` with an output  
iterator. I fear that adding iterators to Hana would spoil its functional design, and I am reluctant to do so.  
  
> > RVO simply cannot be applied to some functions:  
> >   
> > [...]  
> >   
> > I know, but my point was that RVO trivially applies to hana::transform.  
> > Let's clarify things. For any given algorithm, we have four ways of  
> > implementing it:  
> >   
> > Wait, you're skipping over my point. Consider:  
>   
> template <typename F>  
> auto not_rvo_able (MySeq seq_a, MySeq seq_b, F f)  
> {  
>   if (some_predicate())  
>       return transform(seq_a, F);  
>   else  
>       return transform(seq_b, F);  
> }  
>   
> Ok, so now, why does it matter that transform() trivially gets RVO? The  
> point is that if I use copy-happy algorithms that are all RVO-able  
> individually, I end up writing code that does not use RVO.  
  
Ah, you're right. I was overlooking the context in which the function is called.  
  
> [...]  
>   
> But for other cases, maybe this is the optimal code:  
>   
> some_other_t result;  
> transform(some_predicate() ? seq_a : seq_b, F, result); // or begin(result)  
> for the last arg, you get the idea  
>   
> Note that the code above is allowed to copy, move, construct something new,  
> or even chose which to do on a per-element basis.  
>   
> The point is, I need the flexibility of choosing the right approach for my  
> particular circumstances.  
  
You're right, your alternative is more flexible. It also comes at a cost, though, which is  
1. reduced expressiveness  
2. you need to default-construct the result  
  
Hmmm this issue is hard. I want the best of both worlds, i.e. performance _and_ expressiveness. I'll try to come up with a minimal implementation of mutating algorithms and see where that leads me. Frankly, I don't think this has its place in Hana as-is, because it would just clash massively with the rest of the library. However, there might be a way to provide these algorithms in another way, perhaps even a different library.

---

## Comment 10

> Username: tzlaine  
> Created at: 2015-11-13 17:49:46 UTC  
> Url: https://github.com/boostorg/hana/issues/150#issuecomment-156499694  

On Fri, Nov 13, 2015 at 10:05 AM, Louis Dionne notifications@github.com  
wrote:  
  
> For homogeneous sequences, the STL is still useful under all the usage  
> conditions above and more, except for its lack of views. Right now, all  
> Hana does is copy.  
>   
> If I understand correctly what you mean, this is incorrect. Hana will move  
> when given a sequence that can be moved from, and it will copy otherwise.  
> Consider the following example:  
>   
> auto tuple = hana::make_tuple(x, y, z);auto result = hana::transform(tuple, function);  
>   
> Here, since tuple is passed to transform as an lvalue, it can't be  
> moved-from and function will be called as function(x), function(y), and  
> function(z). However, consider the following:  
>   
> auto tuple = hana::make_tuple(x, y, z);auto result = hana::transform(std::move(tuple), function);  
>   
> Now, tuple is passed to transform as an rvalue-reference, and transform  
> will call function as function(std::move(x)), function(std::move(y)), and  
> function(std::move(z)). If function takes advantage of move semantics,  
> then you just moved your resources instead of copying them. This is the  
> same for e.g. sort. If you write  
>   
> auto tuple = hana::make_tuple(x, y, z);auto result = hana::sort(std::move(tuple));  
>   
> it will steal the elements from tuple and move-construct result with them.  
>   
> Is this what you meant?  
>   
> Not exactly.  I was saying something much simpler -- just that I can use  
> copy(), move(), mutating transform(), mutating for_each(), etc.  
  
However, it is _very_ cool that Hana does what you show above.  I already  
knew that, I just meant that Hana doesn't support the mutating options  
other than move in its algorithm interfaces.  
  
> [Note that this is actually a lie for sort right now, but only because of  
> a QOI issue. I _do_ have a more runtime-efficient implementation of sort  
> locally, but it behaves badly at compile-time. I should fix this.]  
>   
> I really want analogues of std::copy(), std::move(),  
> std::copy_backward(), and std::move_backward() in Hana, as well as  
> overloads of transform et al with an out-iterator.  
>   
> For std::copy and std::move, you can just write  
>   
> auto tuple = hana::make_tuple(x, y, z);auto a = tuple; // std::copyauto b = std::move(tuple); // std::move  
>   
> Those are great.  
>   
> If you want to do it across sequence types, just use:  
>   
> auto tuple = hana::make_tuple(x, y, z);auto a = hana::tohana::ext::boost::fusion::vector_tag(tuple); // std::copyauto b = hana::tohana::ext::boost::fusion::vector_tag(std::move(tuple)); // std::move  
>   
> Those, not so much.  As a user, I really want to write what's in the  
> comment above, not what's in the code above.  That's a lot of typing just  
> to say "copy".  
>   
> For the *_backward variants, I admit that there's no such thing out of the  
> box in Hana. You could write it with a for_each with appropriate indices,  
> but you'd have to write it by hand. Same goes for transform with an output  
> iterator. I fear that adding iterators to Hana would spoil its functional  
> design, and I am reluctant to do so.  
>   
> I really think it's just a matter of writing convenience functions that  
> wrap these simple patterns:  
  
template <typename SeqA, typename SeqB>  
SeqB&  copy (SeqA const & seq_a, SeqB & seq_b)  
{  
    return seq_b = hana::tohana::ext::boost::fusion::vector_tag(seq_a);  
}  
  
template <typename SeqA, typename SeqB>  
SeqB&  copy_backward (SeqA const & seq_a, SeqB & seq_b)  
{  
    return seq_b = /\* something similarly simple for you but not anyone  
else :) */;  
}  
  
Don't get hung up on the return value; it's just there for chaining, if  
that's desirable.  
  
As you can see, iterators are not strictly necessary, and in fact are not  
in keeping with the way the new range-based STL is going.  If you do stuff  
like this example, I think you're a great deal of the way there.  When  
users inevitably ask for operations on susequences, give them STL2-style  
ranges based on indexes:  
  
template <typename Seq, typename First, typename Last>  
struct subsequence_view  
{  
    Seq seq;  
};  
  
template <typename Seq, typename First, typename Last>  
auto range (Seq && seq, First first, Last last)  
{  
    return subsequence_view<decltype(std::forward<Seq>(seq)), First,  
Last>{std::forward<Seq>(seq)};  
}  
  
auto tuple3 = hana::make_tuple(x, y, z);  
auto tuple2 = hana::make_tuple(s, t);  
  
hana::copy(range(tuple3, 1_c, 3_c), tuple2); // copies tuple3[1] ->  
tuple2[0] and tuple3[2] -> tuple2[1]  
hana::move(range(std::move(tuple2), 1_c, 2_c), range(tuple3, 0_c, 1_c)); //  
moves tuple2[1] -> tuple[0]  
  
Easy, peasy, lemon squeezy.  
  
> RVO simply cannot be applied to some functions:  
>   
> [...]  
>   
> I know, but my point was that RVO trivially applies to hana::transform.  
> Let's clarify things. For any given algorithm, we have four ways of  
> implementing it:  
>   
> Wait, you're skipping over my point. Consider:  
>   
> template  
> auto not_rvo_able (MySeq seq_a, MySeq seq_b, F f)  
> {  
> if (some_predicate())  
> return transform(seq_a, F);  
> else  
> return transform(seq_b, F);  
> }  
>   
> Ok, so now, why does it matter that transform() trivially gets RVO? The  
> point is that if I use copy-happy algorithms that are all RVO-able  
> individually, I end up writing code that does not use RVO.  
>   
> Ah, you're right. I was overlooking the context in which the function is  
> called.  
>   
> [...]  
>   
> But for other cases, maybe this is the optimal code:  
>   
> some_other_t result;  
> transform(some_predicate() ? seq_a : seq_b, F, result); // or begin(result)  
> for the last arg, you get the idea  
>   
> Note that the code above is allowed to copy, move, construct something new,  
> or even chose which to do on a per-element basis.  
>   
> The point is, I need the flexibility of choosing the right approach for my  
> particular circumstances.  
>   
> You're right, your alternative is more flexible. It also comes at a cost,  
> though, which is  
> 1. reduced expressiveness  
>   
> I hope not.  How so?  
> 1. you need to default-construct the result  
>   
> That always sucks, but is sometimes required.  
>   
> Hmmm this issue is hard. I want the best of both worlds, i.e. performance  
> _and_ expressiveness. I'll try to come up with a minimal implementation  
> of mutating algorithms and see where that leads me. Frankly, I don't think  
> this has its place in Hana as-is, because it would just clash massively  
> with the rest of the library. However, there might be a way to provide  
> these algorithms in another way, perhaps even a different library.  
  
Zach
