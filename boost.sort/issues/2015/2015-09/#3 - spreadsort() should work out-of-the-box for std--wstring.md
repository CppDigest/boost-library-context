# #3 - spreadsort() should work out-of-the-box for std::wstring [Closed]

> Username: Morwenn  
> Created at: 2015-09-17 19:09:04 UTC  
> Updated at: 2015-09-21 09:29:24 UTC  
> Closed at: 2015-09-20 10:39:37 UTC  
> Url: https://github.com/boostorg/sort/issues/3  

The following code sample fails:  
  
```  
int main()  
{  
    std::vector<std::wstring> vec(100000);  
    boost::sort::spreadsort::string_sort(vec.begin(), vec.end());  
}  
```  
  
It triggers the following static assertion:  
  
> `sizeof(x[u]) == sizeof(Unsigned_char_type)`  
  
For some reason, when `spreadsort` is directly called with an `std::wstring`, it is forwarded to the `string_sort` overload that takes two iterators (until there, it seems normal) that forwards it to the overload that takes two iterators and a character but it always gives an `unsigned char` for the character. Shoudn't it do a dispatch with `typename std::iterator_traits<RandomAccessIter>::value_type` instead so that `Unsigned_char_type` can be `wchar_t` when needed?  
  
Anyway, spreadsort says it works for `std::wstring` so I would expect to work out-of-the-box with `std::wstring` instead of triggering a compile-time error.

---

## Comment 1

> Username: spreadsort  
> Created at: 2015-09-20 03:37:32 UTC  
> Url: https://github.com/boostorg/sort/issues/3#issuecomment-141741953  

Done.  It wasn't as simple to implement as you suggested, as string_sort requires an unsigned wchar_t, but it works now by splitting the wrapper into 2 enable_ifs, each with its own unsigned character type (wchar_t is signed).

---

## Comment 2

> Username: Morwenn  
> Created at: 2015-09-20 09:12:20 UTC  
> Url: https://github.com/boostorg/sort/issues/3#issuecomment-141759737  

Ok, I didn't know about the `unsigned wchar_t`. To be honest, the library I am writing is 90% indirection & code architecture and 10% actual sorting logic if we exclude the algorithms that I didn't write, so I don't understand how some of the algorithms work (including this one), hence the bad suggestion, sorry for that.  
  
Anyway, I didn't have many tests for `std::wstring` but they are passing with this fix. Thanks :)

---

## Comment 3

> Username: spreadsort  
> Created at: 2015-09-20 10:39:37 UTC  
> Url: https://github.com/boostorg/sort/issues/3#issuecomment-141774714  

You're welcome.

---

## Comment 4

> Username: Morwenn  
> Created at: 2015-09-20 12:06:05 UTC  
> Url: https://github.com/boostorg/sort/issues/3#issuecomment-141781772  

Oops, seems that I spoke too soon. For some reason I totally missed a warning introduced by the fix:  
  
With g++:  
  
> `warning: long, short, signed or unsigned used invalidly for 'unused' [-Wpedantic]`  
  
With clang++:  
  
> `warning: 'wchar_t' cannot be signed or unsigned [-Wpedantic]`  
  
It seems like `wchar_t` shouldn't be declared `unsigned`, and it's true that its signedness is undefined by the standard. Would `std::make_unsigned<wchar_t>::type` (or a Boost equivalent) solve the problem?

---

## Comment 5

> Username: spreadsort  
> Created at: 2015-09-21 01:51:35 UTC  
> Url: https://github.com/boostorg/sort/issues/3#issuecomment-141854796  

On Sun, Sep 20, 2015 at 8:06 AM Morwenn notifications@github.com wrote:  
  
> Oops, seems that I spoke too soon. For some reason I totally missed a  
> warning introduced by the fix:  
>   
> With g++:  
>   
> warning: long, short, signed or unsigned used invalidly for 'unused'  
> [-Wpedantic]  
>   
> With clang++:  
>   
> warning: 'wchar_t' cannot be signed or unsigned [-Wpedantic]  
>   
> It seems like wchar_t shouldn't be declared unsigned, and it's true that  
> its signedness is undefined by the standard. Would  
> std::make_unsigned<wchar_t>::type (or a Boost equivalent) solve the  
> problem?  
>   
>  While resolving this issue, I ran into another complication I'd forgotten  
> about: wchar_t is 2 bytes on Windows, and 4 bytes on linux.  2 bytes sorts  
> nicely with the string_sort approach, but 4 bytes requires breaking up the  
> characters into pieces and feeding them to the algorithm, a bit of  
> complication I'm not ready to stick into the core library.  Internally  
> string_sort handles characters of over 2 bytes that aren't being managed by  
> custom functors by just falling back to std::sort and issuing a  
> compile-time warning.  
  
So my solution was:  
Enable spreadsort for 2-byte wstrings (using uint16_t as the unsigned  
character type), and update the documentation to make it clear that it  
doesn't work for 4-byte wstrings.  I've rolled this change out to master,  
and reverted my wstring testing change, as testing std::sort of wstring  
vectors on linux is just silly.  
  
Bottom line: sorting wstrings with spreadsort on linux just isn't going to  
work for you unless you're willing to write your own functors.  For your  
application, I think just skipping spreadsort wstring support should be  
fine.

---

## Comment 6

> Username: Morwenn  
> Created at: 2015-09-21 09:29:24 UTC  
> Url: https://github.com/boostorg/sort/issues/3#issuecomment-141923794  

Ok, I think I understand the problem a bit better, thanks for the explanation. I guess I'll just skip the `std::wstring` support for now as you suggest, or SFINAE it out when the character width is greater than 2 bytes.
