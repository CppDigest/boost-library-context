# #37 Context debug fix swap [Merged]

> Username: cppljevans  
> Created at: 2014-06-08 19:09:31 UTC  
> Updated at: 2014-06-20 22:02:59 UTC  
> Merged at: 2014-06-08 22:15:20 UTC  
> Closed at: 2014-06-08 22:15:20 UTC  
> Url: https://github.com/boostorg/spirit/pull/37  

Solve problem with confusion output from  context_debug as described here:  
  
http://article.gmane.org/gmane.comp.parsers.spirit.general/26803

---

## Comment 1

> Username: djowel  
> Created_at: 2014-06-08 22:15:12 UTC  
> Url: https://github.com/boostorg/spirit/pull/37#issuecomment-45450244  

I'm not quite sure why there are more "Files changed" than needed. I can see that the other files have already been changed. I'm going to merge this anyway. I do not want to be too bureaucratic.

---

## Comment 2

> Username: djowel  
> Created_at: 2014-06-08 22:38:30 UTC  
> Url: https://github.com/boostorg/spirit/pull/37#issuecomment-45450718  

Oh man, this has become gnarly! Being bureaucratic is one thing, being careful is another thing. I think I messed up the repo. I'm attempting a revert. Let;s do thi  properly, Larry.

---

## Comment 3

> Username: cppljevans  
> Created_at: 2014-06-08 22:59:37 UTC  
> Url: https://github.com/boostorg/spirit/pull/37#issuecomment-45451170  

On 06/08/14 17:38, Joel de Guzman wrote:  
  
> Oh man, this has become gnarly! Being bureaucratic is one thing, being   
> careful is another thing. I think I messed up the repo. I'm attempting   
> a revert. Let;s do thi properly, Larry.  
>   
> —  
> Reply to this email directly or view it on GitHub   
> https://github.com/boostorg/spirit/pull/37#issuecomment-45450718.  
>   
> I tried to do it properly; however, I've no idea what I did wrong.  
> In summary, here's what I did.  
- fork spirit  
- clone spirit:  
  git clone https://github.com/cppljevans/spirit.git  
- checkout out the develop branch:  
  git checkout remotes/origin/develop  
- create a branch with:  
  git checkout -b context_debug_fix_swap  
- modified detail/rule.hpp  
- commited change:  
  git commit -am 'fix context_debug use in detail/rule.hpp'  
  [context_debug_fix_swap b1cdd15] fix context_debug use in detail/rule.hpp  
  1 file changed, 29 insertions(+), 18 deletions(-)  
- pushed the change:  
  git push -u origin context_debug_fix_swap  
  Username for 'https://github.com': cppljevans  
  Password for 'https://cppljevans@github.com':  
  To https://github.com/cppljevans/spirit.git  
  0f5844c..b1cdd15 context_debug_fix_swap -> context_debug_fix_swap  
  Branch context_debug_fix_swap set up to track remote branch   
  context_debug_fix_swap from origin.  
- then went to web to make the push request. The following is what I   
  recorded  
  as happening there:  
  https://github.com/boostorg/spirit/tree/develop  
  
That page had:  
  
Your recently pushed branches:  
  
[ cppljevans:context_debug_fix_swap (4 minutes ago)]  
  
So I clicked on that link. This lead me (after several more clicks)  
to:  
  
https://github.com/cppljevans/spirit/blob/context_debug_fix_swap/include/boost/spirit/home/x3/nonterminal/detail/rule.hpp  
  
which showed my changes.  
  
Then, back at:  
  
https://github.com/boostorg/spirit/tree/develop  
  
which showed the  
  
[ cppljevans:context_debug_fix_swap](4 minutes ago)...[Compare & pull   
request]  
  
clicked on the [Compare & pull request]. This sent me to:  
  
https://github.com/cppljevans/spirit/compare/boostorg:master...cppljevans:context_debug_fix_swap?expand=1  
  
which showed:  
  
11 commits  
8 files changed  
0 comments  
4 contributors  
  
and my changes as well as other changes. Selected the "create pull  
request" after adding comment, resulting in:  
  
cppljevans commented a minute ago  
[  
Solve problem with confusion output from context_debug as described here:  
  
http://article.gmane.org/gmane.comp.parsers.spirit.general/26803  
]  
  
on:  
  
https://github.com/boostorg/spirit/pull/37  
  
As I mentioned before, I'm unfamiliar with git; hence, I probably made  
some obvious errors, but, as of now, I've no idea what that might be :(  
  
Sorry about that.  
  
-regards,  
Larry

---

## Comment 4

> Username: djowel  
> Created_at: 2014-06-09 02:18:43 UTC  
> Url: https://github.com/boostorg/spirit/pull/37#issuecomment-45456253  

One obvious problem here is that it is targeting the master branch again, instead of develop. I thought that was already known. This, I think, is what got me confused. There should only be one commit in a pull request.

---

## Comment 5

> Username: cppljevans  
> Created_at: 2014-06-09 13:38:11 UTC  
> Url: https://github.com/boostorg/spirit/pull/37#issuecomment-45490978  

So what should I do now?  I could try to fork again and then switch to develop and then  
create another context_debug_fix_swap branch and then make sure there's only  
1 commit.

---

## Comment 6

> Username: K-ballo  
> Created_at: 2014-06-09 16:38:39 UTC  
> Url: https://github.com/boostorg/spirit/pull/37#issuecomment-45512503  

Here is how your pull request should look like: https://github.com/K-ballo/spirit/compare/boostorg:develop...context_debug_fix_swap?expand=1  
  
The steps I took to get those results were: (no command line, sorry)  
- Switch to `develop`, and pull from `origin/develop`. In my setup, `origin` is the remote for the boostorg repository; in your case that would probably be `upstream`.  
- Branch `context_debug_fix_swap` and switch to it  
- Apply the changes  
- Push to my own fork  
  
After that, I went on github to my own fork, switched to `context_debug_fix_swap` and clicked "Compare & Pull Request". Finally, I changed the target branch from `boostorg:master` to `boostorg:develop` by clicking "Edit" at the top.

---

## Comment 7

> Username: djowel  
> Created_at: 2014-06-10 22:46:57 UTC  
> Url: https://github.com/boostorg/spirit/pull/37#issuecomment-45681932  

Larry, I can simply merge the file myself, but I'd really prefer to merge a PR from you so we have the proper history. Anyway, tell me what you think.

---

## Comment 8

> Username: cppljevans  
> Created_at: 2014-06-11 12:27:47 UTC  
> Url: https://github.com/boostorg/spirit/pull/37#issuecomment-45734249  

On 06/10/14 17:46, Joel de Guzman wrote:  
  
> Larry, I can simply merge the file myself, but I'd really prefer to   
> merge a PR from you so we have the proper history. Anyway, tell me   
> what you think.  
>   
> —  
> Reply to this email directly or view it on GitHub   
> https://github.com/boostorg/spirit/pull/37#issuecomment-45681932.  
>   
> I need to learn how to do a PR properly anyway; so,  
> I'll give it another shot. This time I'll make  
> sure there's only one change, mine, before I make  
> the PR.  
  
Thanks for the prompt ;)  
  
-regards,  
Larry

---

## Comment 9

> Username: cppljevans  
> Created_at: 2014-06-11 14:34:55 UTC  
> Url: https://github.com/boostorg/spirit/pull/37#issuecomment-45749539  

On 06/10/14 17:46, Joel de Guzman wrote:  
  
> Larry, I can simply merge the file myself, but I'd really prefer to   
> merge a PR from you so we have the proper history. Anyway, tell me   
> what you think.  
>   
> —  
> Reply to this email directly or view it on GitHub   
> https://github.com/boostorg/spirit/pull/37#issuecomment-45681932.  
>   
> I think I got it right this time.  
> Here's the pull request page:  
  
https://github.com/boostorg/spirit/pull/38  
  
-regards,  
Larry

---
