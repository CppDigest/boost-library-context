# #890 - Coding guidelines: single-line block. [Closed]

> Username: awulkiew  
> Created at: 2021-07-19 15:34:46 UTC  
> Updated at: 2021-07-21 19:26:34 UTC  
> Closed at: 2021-07-21 19:26:34 UTC  
> Url: https://github.com/boostorg/geometry/issues/890  

Our coding guidelines (https://github.com/boostorg/geometry/wiki/Guidelines-for-Developers) states that:  
  
> * Braces enclosing block of code (if-else, loops) should be placed in separate lines  
> ```  
> if (expr)  
> {  
> }  
> ```  
  
It doesn't say that single-line blocks must be enclosed with braces. However AFAIR we do that anyway.  
  
I'd like to propose to explicitly allow non-nested single-line blocks in if statements and loops. Examples:  
```  
if (x > 0)  
    return x;  
  
if (x > 0)  
    break;  
      
for (auto i : v)  
    do_something(i);  
      
if (x < 0)  
    return x;  
else if (x == 0)  
    return y;  
else  
    return z;  
```  
  
In these cases braces would be required:  
```  
// nested block  
for (;;)  
{  
    if (x > 0)  
        break;  
}  
  
// multi-line loop declaration  
for (typename boost::range_iterator<Range>::type it = boost::begin(range);  
     it != boost::end(range); ++it)  
{  
    f(*it);  
}  
```  
  
The rationale behind this proposal is that 50% of lines in such simple if statements and loops with brackets doesn't carry any information.  
In this case the braces don't improve readability. On the contrary. I'd argue that the braces make the code harder to read.  
In case of the version without brackets an eye can see more meaningful and readable code at the same time.  
  
Consider e.g. reading several functions like this one by one:  
```  
void f(int x)  
{  
    if (x < 0)  
        return x;  
    else if (x == 0)  
        return y;  
    else  
        return z;  
}  
```  
vs  
```  
void f(int x)  
{  
    if (x < 0)  
    {  
        return x;  
    }  
    else if (x == 0)  
    {  
        return y;  
    }  
    else  
    {  
        return z;  
    }  
}  
```  
  
What do you think?

---

## Comment 1

> Username: barendgehrels  
> Created at: 2021-07-19 16:27:23 UTC  
> Updated at: 2021-07-19 16:28:34 UTC  
> Url: https://github.com/boostorg/geometry/issues/890#issuecomment-882686585  

All blocks must be within braces. This is our convention, and it is the convention in all environments where I worked.  
Blocks without braces are error prone.  
The example you give can be rewritten with ternaries which don't need braces.

---

## Comment 2

> Username: awulkiew  
> Created at: 2021-07-19 17:05:17 UTC  
> Url: https://github.com/boostorg/geometry/issues/890#issuecomment-882711042  

> The example you give can be rewritten with ternaries which don't need braces.  
  
It was just an example, not all cases can be replaced, e.g.:  
```  
void f(int x)  
{  
    if (x < 0)  
    {  
        do_x();  
    }  
    else if (x == 0)  
    {  
        do_y();  
    }  
    else  
    {  
        do_z();  
    }  
}  
```  
  
> This is our convention, and it is the convention in all environments where I worked. Blocks without braces are error prone.  
  
Right, I agree that it can be error prone. I wasn't precise enough, sorry about that. My main concern is not the braces themselves but the bloat of the code caused by how we use them.  
  
E.g. google styleguide also requires braces (though not for single-statement loops, only if-statements). However they don't put braces on the next lines:  
```  
void f(int x)  
{  
    if (x < 0) {  
        do_x();  
    } else if (x == 0) {  
        do_y();  
    } else {  
        do_z();  
    }  
}  
```  
See: https://google.github.io/styleguide/cppguide.html#Conditionals  
  
See also the examples at isocpp: https://isocpp.github.io/CppCoreGuidelines/CppCoreGuidelines#Rf-single  
(they allow ifs without braces too but I'm showing specifically braces without new lines)  
  
See also the examples at cppreference.  
https://en.cppreference.com/w/cpp/algorithm/copy#Possible_implementation  
  
AFAIU this is what the rest of the world does with regards to braces because it's simply more readable. ;)  
  
What do you think about allowing it in our code?

---

## Comment 3

> Username: awulkiew  
> Created at: 2021-07-19 17:30:57 UTC  
> Updated at: 2021-07-19 18:06:00 UTC  
> Url: https://github.com/boostorg/geometry/issues/890#issuecomment-882729099  

More context:  
  
In msvc STL braces are used everywhere, but they are put at the same line:  
https://github.com/microsoft/STL/blob/main/stl/inc/algorithm#L864-L876  
  
In both gcc/libstdc++ and clang/libc++ braces are put at the next line (in case of the former additionally indented) but single-line statements don't require them:  
https://github.com/gcc-mirror/gcc/blob/master/libstdc%2B%2B-v3/include/bits/stl_algo.h#L159-L183  
https://github.com/llvm-mirror/libcxx/blob/master/include/algorithm#L977-L992

---

## Comment 4

> Username: barendgehrels  
> Created at: 2021-07-19 19:51:05 UTC  
> Updated at: 2021-07-19 19:51:48 UTC  
> Url: https://github.com/boostorg/geometry/issues/890#issuecomment-882814269  

It's true that braces at the end are used in many environments. But if they are used, they are used through out the code and not just for these if statements. And we'll not change that now, why should we...  
  
Apart from that, you proposed a change for a single statement and these are in general used for any number of statements.  
  
If you change something to get less lines, why not:  
```  
void f(int x)  
{  
    if (x < 0) { do_x(); }   
    else if (x == 0) { do_y(); }   
    else { do_z(); }  
}  
```  
But I don't really propose this, it's readable and short, but uncommon.  
  
I propose to not change it at all. Our style is readable and (more or less) consistent, I don't feel the need to get a long transition period from one to another style.  
clang-format might help but it often produces ugly results which will also take a lot of time.

---

## Comment 5

> Username: awulkiew  
> Created at: 2021-07-20 00:26:18 UTC  
> Url: https://github.com/boostorg/geometry/issues/890#issuecomment-882951436  

Yes changing the guidelines would require a lot of work. And to be honest I got used to the opening brace at the next line. This is why I proposed relaxing the use of braces for single staments. It doesn't require any additional work and is consistent with what we have right now. Plus it's consistent with libstdc++ and libc++.  
  
While I agree that the general lack of braces is indeed error prone, non-nested single statements are not. Unless I'm missing something. Could you give me an example where my original proposal could lead to errors?

---

## Comment 6

> Username: barendgehrels  
> Created at: 2021-07-21 09:09:48 UTC  
> Updated at: 2021-07-21 09:11:12 UTC  
> Url: https://github.com/boostorg/geometry/issues/890#issuecomment-884025814  

Okay, so your original proposal is only for `if` with one statement and (after my statement about ternary) only for constructs which cannot be written as a ternary.  
  
The problems I have with this are  
- I often add a lot of debug information. Which often is not committed.  This needs me to add an opening brace, add debugging, and add the closing brace. It's simply more work  
- If we can do this with one-line `if`, why not with `for`? And `while`? It's a moving target which makes the style less consistent  
- Visually, the only difference (if it belongs to the if, or not) is an indentation of 4 characters  
- If the only objective is having the code more condensed, my question is: why is non-condensed code a problem? I like space. I like readable code and (IMO) having one consistent style (always use braces) is more clear than some exceptions (in some cases they are not necessary)  
  
Apart from that, I agree with you that in some cases the code might be overly bloated, if you cannot use a switch statement, and handling multiple conditions as you say.  
  
Therefore, I can live without braces if it is in one line:  
```  
void f(int x)  
{  
    if (x < 0) do_x();   
    else if (x == 0) do_y();  
    else do_z();  
}  
void g(int x)  
{  
  int i;  
  for (i = 0; i < x; i++) std::cout << i << std::endl;  
  while (i > 0) i--;  
  do i++; while (i < x);  
}  
```  
The argument for why I can accept this:  
* it is consistent, can be applied for all constructs  
* we often use one-line constructs already (e.g. `static inline std::size_t size() { return 3u; }`)  
* it's more readable (to me) than having it on the next line, where just the indentation indicates that it belongs to the `if`  
  
I'm not really in favor of it (because of the debugging) but I can live with it.  
  
About your argument for libstdc++: yes, I see it. I don't find that so readable. And they also do things like:  
```  
    for (; __first1 != __last1; ++__first1)  
        for (_ForwardIterator2 __j = __first2; __j != __last2; ++__j)  
            if (__pred(*__first1, *__j))  
                return __first1;  
    return __last1;  
```  
where do we stop?  
  
So, in summary, this is my opinion and these are my arguments. But everyone else really dislikes the braces here and want your proposal, I will not object.

---

## Comment 7

> Username: vissarion  
> Created at: 2021-07-21 09:45:49 UTC  
> Url: https://github.com/boostorg/geometry/issues/890#issuecomment-884050847  

I am OK with the current guidelines since I have used to it. I see the point in Adam's proposal but I prefer a simpler rule like the one we have "always use braces" and I can leave with the side-effect of not very readable code that this rule can produce (as shown in many examples above).  
However, I do not have a strong opinion in this topic, so I am flexible.

---

## Comment 8

> Username: awulkiew  
> Created at: 2021-07-21 11:29:09 UTC  
> Updated at: 2021-07-21 13:15:26 UTC  
> Url: https://github.com/boostorg/geometry/issues/890#issuecomment-884117857  

@barendgehrels   
> Okay, so your original proposal is only for if with one statement and (after my statement about ternary) only for constructs which cannot be written as a ternary.  
  
Yes, and only for non-nested ifs and loops that have 1-line definitions and 1-line statement.  
  
> I often add a lot of debug information. (...)  
  
My point was to allow it, not force it. So to not make it mandatory, but optional. But I understand that the result could be inconsistent code and therefore less readable.  
  
> If we can do this with one-line if, why not with for? And while?  
  
Of course initially I had loops in mind too. Under the same conditions. 1-line definition, 1-line statement, non nested.  
  
> why is non-condensed code a problem?  
  
Because it's less dense in information. You have to scroll the page instead of moving your eye to see other parts.  
  
> having one consistent style (always use braces) is more clear than some exceptions  
  
Yes, I see your point.  
  
> The argument for why I can accept this: (...) it's more readable (to me) than having it on the next line, where just the indentation indicates that it belongs to the if  
  
Right, we sometimes use this already to save space. Based on the context this would be more or less readable. However I'd prefer version with braces around the statement, like you proposed in the previous comment. This would also be consistent with lambda expressions and functions. So:  
```  
if (x > 0) { do_x(); }  
while (i > 0) { i--; }  
[]() { do_x(); }  
int fun(int a) { return a + 1; }  
```  
Btw, consider the code like this, which is an unwound loop calculating the position of the last high bit in an 64bit integer:  
```  
if (i >= 4294967296ll) { r += 32; i >>= 32; }  
if (i >= 65536ll) { r += 16; i >>= 16; }  
if (i >= 256ll) { r += 8; i >>= 8; }  
if (i >= 16ll) { r += 4; i >>= 4; }  
if (i >= 4ll) { r += 2; i >>= 2; }  
if (i >= 2ll) { r += 1; i >>= 1; }  
```  
If the statements were put in separate lines and braces were added this function would occupy the majority of the screen and be less readable. Would the above code be acceptable to you even though there are 2 statements between the braces? Well, I guess it doesn't matter in this case because the statements could be rewritten with comma operator so they'd be one statement.  
  
> I'm not really in favor of it (because of the debugging) but I can live with it.  
  
Right, typically you use braces and sometimes to save spaces put everything in one line, e.g. `std::size_t size() { return 3u; }`. So this is optional and based on the context, correct?  
  
> About your argument for libstdc++ (...) And they also do things like: (...) where do we stop?  
  
Under what I initially proposed this wouldn't be allowed because they are nested. It'd be:  
```  
for (; __first1 != __last1; ++__first1)  
{  
    for (_ForwardIterator2 __j = __first2; __j != __last2; ++__j)  
    {  
        if (__pred(*__first1, *__j))  
            return __first1;  
    }  
}  
return __last1;  
```  
But I can see your point that this optional guideline that I'm proposing can be a slippery slope allowing to relax the guidelines even more in the future.  
  
> So, in summary, this is my opinion and these are my arguments.  
  
I see your points and agree with them. I propose the following:  
- to not use my original proposal ;)  
- to optionally allow what you proposed, 1-line ifs and loops (short, not necessarily 1-statement) but with braces and only if it's contextually reasonable. And whether or not it's reasonable we'd see during a review.  
  
@vissarion   
> I prefer a simpler rule like the one we have "always use braces"  
  
Yes I can see your point. The optional rule proposed above is based on the context in which it's used. So maybe we shouldn't explicitly put it in the guidelines but keep it in mind instead?

---

## Comment 9

> Username: vissarion  
> Created at: 2021-07-21 12:31:09 UTC  
> Url: https://github.com/boostorg/geometry/issues/890#issuecomment-884152420  

> Yes I can see your point. The optional rule proposed above is based on the context in which it's used. So maybe we shouldn't explicitly put it in the guidelines but keep it in mind instead?  
  
I am OK with the above rule. I lean towards mention this in the guidelines. It will help future contributors (but us too) to have it written even as an implicit note.

---

## Comment 10

> Username: barendgehrels  
> Created at: 2021-07-21 16:26:22 UTC  
> Url: https://github.com/boostorg/geometry/issues/890#issuecomment-884322903  

Sure, I'm also OK with the above rule. And I agree, in one line it's better to use there braces too (otherwise you might be attempted to add a statement and be surprised it's always executed).  
  
So this **allow 1-line ifs and loops (short, not necessarily 1-statement) but with braces and only if it's contextually reasonable** looks actually very good to me.  
  
Seems we agree! Yes, agree with that too, we should mention it in the guidelines.  
  
Thanks for your proposal and discussions.

---

## Comment 11

> Username: awulkiew  
> Created at: 2021-07-21 19:26:10 UTC  
> Url: https://github.com/boostorg/geometry/issues/890#issuecomment-884438291  

I updated the guidelines: https://github.com/boostorg/geometry/wiki/Guidelines-for-Developers#code-formatting-and-indentation  
Feel free to change it or add something.

---

## Comment 12

> Username: awulkiew  
> Created at: 2021-07-21 19:26:32 UTC  
> Url: https://github.com/boostorg/geometry/issues/890#issuecomment-884438547  

Thanks for the discussion and ideas!
