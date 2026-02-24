# #39 line_pos_iterator column numbering bug,get_current_line bug. [Closed]

> Username: tomoki  
> Created at: 2014-06-14 03:55:18 UTC  
> Updated at: 2014-06-15 03:54:37 UTC  
> Closed at: 2014-06-14 08:33:10 UTC  
> Url: https://github.com/boostorg/spirit/pull/39  

This pull request fixes following bugs.  
- line_pos_iterator's column numbering bug.  
- line_pos_iterator's get_current_line bug.  
  
Small sample that causes bug is here.  
  
```  
#include <iostream>  
#include <string>  
#include <boost/spirit/include/qi.hpp>  
#include <boost/spirit/include/support_line_pos_iterator.hpp>  
  
int main(){  
    typedef boost::spirit::line_pos_iterator<std::string::const_iterator>  
                pos_iterator_t;  
    {  
        std::string test = "abc\ndef";  
        pos_iterator_t begin(test.begin()),  
                       end(test.end());  
        // print test for debug.  
        std::cout << std::string(begin,end) << std::endl;  
  
        // !!!!column numbering bug!!!!  
        // 'd'(2,2) should be 'd'(2,1).  
        for(pos_iterator_t iter=begin;iter!=end;++iter){  
            std::cout << "'" << *iter << "'"   
                << "(" << get_line(iter) << ":" << get_column(begin,iter) << ")"  
                << std::endl;  
        }  
  
        // !!!!get_current_line bug!!!!  
        //  When iter is at 'd','e','f',current_line.length() should be 3.  
        //   and,what get_current_line should return when iter is at '\n'?  
        for(pos_iterator_t iter = begin; iter != end; ++iter){  
            auto range = get_current_line(begin,iter,end);  
            std::string current_line(range.begin(),range.end());  
            std::cout << current_line << " (len:" <<  current_line.length() << ")"  
                << std::endl;  
        }  
    }  
    return 0;  
}  
  
```  
  
And,I have a few questions.  
- Spirit "must" handle CR,LF,CR+LF,LF+CR completely?  
  I think,it is very hard mission.I couldn't handle it well.  
- Should I add Copyright on line_pos_iterator.hpp?

---

## Comment 1

> Username: K-ballo  
> Created_at: 2014-06-14 04:12:50 UTC  
> Updated_at: 2014-06-14 06:56:31 UTC  
> Url: https://github.com/boostorg/spirit/pull/39#discussion_r13778849  

Don't use alternative tokens like `and` or `or`, even while being part of the standard they do not work out of the box on some compilers (noticeable MSVC).

---

## Comment 2

> Username: K-ballo  
> Created_at: 2014-06-14 04:13:40 UTC  
> Updated_at: 2014-06-14 06:56:31 UTC  
> Url: https://github.com/boostorg/spirit/pull/39#discussion_r13778857  

What's the rationale for changing this? Does this introduce a change in functionality?

---

## Comment 3

> Username: K-ballo  
> Created_at: 2014-06-14 04:14:17 UTC  
> Updated_at: 2014-06-14 06:56:31 UTC  
> Url: https://github.com/boostorg/spirit/pull/39#discussion_r13778861  

We don't generally comment closed namespaces like this

---

## Comment 4

> Username: K-ballo  
> Created_at: 2014-06-14 04:24:36 UTC  
> Url: https://github.com/boostorg/spirit/pull/39#issuecomment-46078184  

Thank you for looking into this! Your pull request needs some regression tests. The examples you posted here could do, please have a look at the test suite and the style in which those are done.   
  
Additionally, it would be nice if you could add spaces here and there, for better readability. Look at the surroundings to keep the code consistent (spaces after keywords, comma, between most operators, braces, ...)

---

## Comment 5

> Username: tomoki  
> Created_at: 2014-06-14 05:26:59 UTC  
> Updated_at: 2014-06-14 06:56:31 UTC  
> Url: https://github.com/boostorg/spirit/pull/39#discussion_r13779079  

I read spirit/include/boost/spirit/home/support/iterators/multi_pass.hpp to check style.  
It has comment like this that the others don't have.  
I'll remove this comment.Thank you.

---

## Comment 6

> Username: tomoki  
> Created_at: 2014-06-14 05:33:36 UTC  
> Updated_at: 2014-06-14 06:56:31 UTC  
> Url: https://github.com/boostorg/spirit/pull/39#discussion_r13779092  

Thanks for info.  
I didn't know that.I'll change it to `&&` and `||`.

---

## Comment 7

> Username: tomoki  
> Created_at: 2014-06-14 05:41:14 UTC  
> Updated_at: 2014-06-14 06:56:31 UTC  
> Url: https://github.com/boostorg/spirit/pull/39#discussion_r13779105  

This change does'nt change anything.  
Should I remove this change?  
  
EDIT:  
I did this change for readability.It was a little complex code for me.

---

## Comment 8

> Username: K-ballo  
> Created_at: 2014-06-14 05:48:25 UTC  
> Updated_at: 2014-06-14 06:56:31 UTC  
> Url: https://github.com/boostorg/spirit/pull/39#discussion_r13779138  

Definitively remove it if it is not a functional change. That will make your pull request smaller and easier to review.

---

## Comment 9

> Username: tomoki  
> Created_at: 2014-06-14 08:07:34 UTC  
> Url: https://github.com/boostorg/spirit/pull/39#issuecomment-46081567  

There seemed to be no way to remove commits in PullRequest.  
(My first commit contains unwanted change.)  
So, I'll open another pullrequest.

---

## Comment 10

> Username: K-ballo  
> Created_at: 2014-06-14 18:13:38 UTC  
> Url: https://github.com/boostorg/spirit/pull/39#issuecomment-46095375  

For future reference, you can keep modifying your branch as you like while the pull request is open. This means that if you feel confident enough with git, you can rewrite history (all kinds of --force operations) and the changes will reflect here automatically. That allows you to remove unwanted commit, rewrite their messages, combine them, and pretty much everything.

---

## Comment 11

> Username: tomoki  
> Created_at: 2014-06-15 03:51:53 UTC  
> Updated_at: 2014-06-15 03:54:37 UTC  
> Url: https://github.com/boostorg/spirit/pull/39#issuecomment-46106123  

Thank you.  
I'll do so when I need to do pullrequest in the future.  
  
I'm sorry for making another pullrequest. https://github.com/boostorg/spirit/pull/40

---
