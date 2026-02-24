# #76 - Make underflow/overflow option more flexible, also allow underflow-only [Closed]

> Username: HDembinski  
> Created at: 2018-09-12 21:27:03 UTC  
> Updated at: 2018-12-19 20:58:10 UTC  
> Closed at: 2018-12-19 20:58:10 UTC  
> Url: https://github.com/boostorg/histogram/issues/76  

Jim Pivarski wrote:  
I'm less worried about nanflow (as long as NaNs go somewhere, it's okay) and more about the fact that the existence of one bin implies the existence of another. The lack of obvious use-cases aside, pieces ought to be composable like Legos.  
  
If the existence of underflow/etc bins are generically provided by a "features" bitset, that would leave open many possibilities. The first feature (1) is kUnderflow, the second (2) is kOverflow, and maybe the third (4) is kNanflow, but that could be added later. Further bits added to this set do not need to be bins but anything else that it makes sense to add that we haven't thought of yet.  
  
To address the question of runtime cost: if the existence of bins are independent, your interpretation of array items depends on the presence of absence of all optional bins below the current one. Putting all the optional bins at the end of the array helps, because then the code branches only need to be applied to other optional bins.  
  
You can put all branches checking for optional bins before the fill loop. For instance, (assuming underflow comes before overflow because it looks easier to explain that way):  
```  
    undernum = -1;  
    overnum = -1;  
    if (h.features & kUnderflow) {  
        undernum = h.numbins;  
    }  
    if (h.features & kOverflow) {  
         overnum = h.numbins + (h.features & kUnderflow != 0);  
    }  
```  
and then in the loop over values to fill:  
```  
    int normalized = float((x - h.low)/(h.high - h.low));  
    if (normalized < 0) {  
        normalized = undernum;  
    }  
    else if (normalized >= h.numbins) {  
        normalized = overnum;  
    }  
    if (normalized >= 0) {  
        h.internal_array[normalized]++;  
    }  
```  
(Of course, the division would be done outside the loop, too, but you get the idea.) It's not that the bit-checking if likely to be all that show compared to the other checks that do have to be in the loop; my point is that they can be removed from the loop. The flexibility has no cost.

---

## Comment 1

> Username: HDembinski  
> Created at: 2018-09-12 23:24:55 UTC  
> Url: https://github.com/boostorg/histogram/issues/76#issuecomment-420830901  

@jpivarski The bin order is [0] ... [n - 1][overflow][underflow], because this works well when you pass a pointer to the memory to a numpy array `a`. You can then access the underflow bin naturally as `a[-1]` and the overflow bin as `a[n]`.

---

## Comment 2

> Username: jpivarski  
> Created at: 2018-09-12 23:40:48 UTC  
> Url: https://github.com/boostorg/histogram/issues/76#issuecomment-420833914  

That doesn't make a lot of sense to me— you could make the same argument that you get the overflow naturally as `a[-1]` and underflow as `a[n]`— but I also don't have strong opinions on it. I consider the array indexes of all the bins to be an internal matter, with named public access methods for regular and special bins.  
  
(That's an example of a method that must have access to private data yet doesn't change the state, because it provides the public view of that data.)

---

## Comment 3

> Username: HDembinski  
> Created at: 2018-09-13 07:42:55 UTC  
> Url: https://github.com/boostorg/histogram/issues/76#issuecomment-420914539  

> That doesn't make a lot of sense to me— you could make the same argument that you get the overflow naturally as a[-1] and underflow as a[n].  
  
The logical order is [underflow][normal bins][overflow]. Can we agree on that? Then how the bins are physically ordered in memory doesn't matter if you have full control over how the bins are accessed. But when you convert the memory in C++ to a numpy array, and you don't want to move anything around, you need to optimize your physical layout so that it works with Python conventions. It so happens that an index -1 in Python goes to the last item in an array. Therefore, the best physical layout in memory is [normal bins][overflow][underflow]. Then it works also in Python without any hacks.

---

## Comment 4

> Username: jpivarski  
> Created at: 2018-09-13 08:08:20 UTC  
> Url: https://github.com/boostorg/histogram/issues/76#issuecomment-420920976  

Ah, I get it now! Because `-1` is one less than `0`, and therefore it's "under." I didn't recognize that before, and just now got it like a pun. Yeah, that's cute.  
  
However, even coming from Python, I would hide the actual index used to access special bins, so I don't see a constraint on their placement— it can be anything. Some people might want to think about underflow as being one less than the lowest finite bin, which your convention provides.  
  
Following that logic, if you do add a nanflow bin, the order would have to be  
  
   * finite bins: `a[i]`  
   * overflow: `a[numbins]`  
   * nanflow: `a[numbins + 1]` or `a[-2]` (no natural choice)  
   * underflow: `a[-1]`

---

## Comment 5

> Username: HDembinski  
> Created at: 2018-09-13 09:06:33 UTC  
> Url: https://github.com/boostorg/histogram/issues/76#issuecomment-420937445  

> However, even coming from Python, I would hide the actual index used to access special bins, so I don't see a constraint on their placement.  
  
I think that is not possible when I hand the counter memory to users directly as a numpy array. My plan was to make the physical layout of the bins part of the public interface. Well documented and not changeable.

---

## Comment 6

> Username: jpivarski  
> Created at: 2018-09-13 09:30:32 UTC  
> Url: https://github.com/boostorg/histogram/issues/76#issuecomment-420944318  

Oh, yeah, I didn't mean that you should hide it, just that any layers built over it that I build (histbook) would hide it. (Loosely in the Python sense of "hiding." Just an underscore.)

---

## Comment 7

> Username: HDembinski  
> Created at: 2018-12-19 20:58:10 UTC  
> Url: https://github.com/boostorg/histogram/issues/76#issuecomment-448741460  

Under and overflow can be set separetely now.
