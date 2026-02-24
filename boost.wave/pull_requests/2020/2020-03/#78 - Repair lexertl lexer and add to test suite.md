# #78 Repair lexertl lexer and add to test suite [Merged]

> Username: jefftrull  
> Created at: 2020-03-08 20:56:16 UTC  
> Updated at: 2020-03-13 19:25:04 UTC  
> Merged at: 2020-03-13 19:24:49 UTC  
> Closed at: 2020-03-13 19:24:50 UTC  
> Url: https://github.com/boostorg/wave/pull/78  

I cannot tell why the original code used `dfa_offset` in these pointer  
calculations, but from a close review of the dfa state tables it  
appears incorrect. Removing it causes list_includes, as well as the  
lexertl test, to stop segfaulting and fixes #77.  
  
Perhaps at some point the related code inside Spirit changed?

---

## Review 1 [Commented]

> Username: hkaiser  
> Created_at: 2020-03-08 22:55:44 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/wave/pull/78#pullrequestreview-370864818  

If the tests pass with this change then all is fine. Things could have changed in Spirit that has caused the issues you saw.  
  
One thing I'd like to ask is that you also remove the statically generated lexertl tables (search for `BOOST_WAVE_LEXERTL_USE_STATIC_TABLES`) as the tools needed to regenerate those are not available anymore (see https://github.com/boostorg/wave/blob/develop/samples/list_includes/lexertl/lexertl_lexer.hpp#L571-L573).   
  
However, perhaps @BenHanson can help? Ben, a long time back you have removed the lexertl example demonstrating how to generate static tables for lexertl. Unfortunately I don't have those files any more and I don't remember how I generated the [file](https://github.com/boostorg/wave/blob/develop/samples/list_includes/lexertl/wave_lexertl_tables.hpp) :-( Would you see a way to help?

---

## Comment 2

> Username: BenHanson  
> Created_at: 2020-03-10 21:17:04 UTC  
> Updated_at: 2020-03-10 21:18:36 UTC  
> Url: https://github.com/boostorg/wave/pull/78#issuecomment-597322143  

Hello!  
  
Here is an example using the latest lexertl (lexertl14):  
  
```  
#include "include/lexertl/generator.hpp"  
#include "include/lexertl/generate_cpp.hpp"  
  
int main()  
{  
    try  
    {  
        lexertl::rules rules;  
        lexertl::state_machine sm;  
  
        // Silly rule set just for demo purposes  
        rules.push("a$", 1);  
        rules.push("a\r?\n", 2);  
        lexertl::generator::build(rules, sm);  
        lexertl::table_based_cpp::generate_cpp("lookup", sm, false, std::cout);  
    }  
    catch (const std::exception &e)  
    {  
        std::cout << e.what() << '\n';  
    }  
  
    return 0;  
}  
  
```  
Let me know if you need more info, I remember that lexertl was not updated to the modern interface in Spirit.  
  
Regards,  
  
Ben

---

## Comment 3

> Username: jefftrull  
> Created_at: 2020-03-12 22:31:52 UTC  
> Url: https://github.com/boostorg/wave/pull/78#issuecomment-598461158  

Thanks @BenHanson that plus the remaining hints in the code were enough for me to do the C++03 equivalent, the code for which is still in Spirit...  
  
My last commit updates the PR to include new static tables and comments explaining how to generate them.

---

## Review 4 [Approved]

> Username: hkaiser  
> Created_at: 2020-03-13 00:51:35 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/wave/pull/78#pullrequestreview-373986401  

Thanks a lot! @BenHanson, @jefftrull! LGTM!

---
