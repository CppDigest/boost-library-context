# #250 - optional_attribute is useless in x3/support/traits/attribute_category.hpp [Closed]

> Username: cppljevans  
> Created at: 2017-10-16 12:36:46 UTC  
> Updated at: 2017-12-07 10:43:59 UTC  
> Closed at: 2017-12-07 10:43:59 UTC  
> Url: https://github.com/boostorg/spirit/issues/250  

Unlike the other *_attribute tags, optional_attribute is not used anywhere.  
Shouldn't there be:  
  
    template <typename T>  
    struct attribute_category<boost::optional<T>>  
        : mpl::identity<optional_attribute> {};  
  
Also, in move_to.hpp, shouldn't there be a move_to function for optional?

---

## Comment 1

> Username: djowel  
> Created at: 2017-10-17 03:10:37 UTC  
> Url: https://github.com/boostorg/spirit/issues/250#issuecomment-337104974  

Hmmmm.. seems you are right. If you have a patch we can test if the test-suite still passes.

---

## Comment 2

> Username: cppljevans  
> Created at: 2017-10-17 15:00:38 UTC  
> Url: https://github.com/boostorg/spirit/issues/250#issuecomment-337260740  

I've made changes to my local copy of:  
  
https://github.com/cppljevans/spirit/tree/get_rhs  
  
However, that branch has many other changes which  
are not yet ready for a pull request.  
Also, I'm no expert with git and I'm wary of  
trying to isolate the changes into another  
branch.   
  
Attached is a `git diff`.  Hope that helps.  
[git-diff.txt](https://github.com/boostorg/spirit/files/1391476/git-diff.txt)

---

## Comment 3

> Username: cppljevans  
> Created at: 2017-12-03 13:47:01 UTC  
> Url: https://github.com/boostorg/spirit/issues/250#issuecomment-348770247  

On 10/16/2017 10:10 PM, Joel de Guzman wrote:  
> Hmmmm.. seems you are right. If you have a patch we can test if the test-suite still passes.  
>   
Unfortunately, I just tried the list.cpp test and it fails at the test:  
  
https://github.com/cppljevans/spirit-experiments/blob/get_rhs/test/x3/list.cpp#L82  
  
So, I wouldn't try this patch yet.  
Investigating why the test fails.  
Also, the debug.cpp test fails :(  
  
-regards,  
Larry
