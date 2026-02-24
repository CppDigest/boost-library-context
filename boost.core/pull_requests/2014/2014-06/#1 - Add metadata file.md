# #1 Add metadata file [Merged]

> Username: danieljames  
> Created at: 2014-06-01 10:56:09 UTC  
> Updated at: 2014-06-01 22:25:15 UTC  
> Merged at: 2014-06-01 19:05:32 UTC  
> Closed at: 2014-06-01 19:05:32 UTC  
> Url: https://github.com/boostorg/core/pull/1  

Should this be treated as a new library in the release notes and root index file? (i.e. the 'Changes in this release' section at http://www.boost.org/doc/libs/master/index.html).

---

## Comment 1

> Username: Beman  
> Created_at: 2014-06-01 14:42:52 UTC  
> Url: https://github.com/boostorg/core/pull/1#issuecomment-44779373  

On Sun, Jun 1, 2014 at 6:56 AM, Daniel James notifications@github.com  
wrote:  
  
> Should this be treated as a new library in the release notes and root  
> index file? (i.e. the 'Changes in this release' section at  
>   
> ## http://www.boost.org/doc/libs/master/index.html).  
>   
> You can merge this Pull Request by running  
>   
>   git pull https://github.com/danieljames/core metadata  
>   
> Or view, comment on, or merge it at:  
>   
>   https://github.com/boostorg/core/pull/1  
> Commit Summary  
> - Add metadata file  
>   
> File Changes  
> - _A_ meta/libraries.json  
>   https://github.com/boostorg/core/pull/1/files#diff-0 (16)  
>   
> For the description, perhaps change "A collection of core utilities." to  
> something like "A collection of simple core utilities used by other Boost  
> libraries and not dependent on headers outside of Core and Config. Serves  
> to break dependencies."  
  
We need to publicize the criteria at every opportunity to avoid, as Peter  
put it, "this might be useful" proposals:-)  
  
--Beman

---

## Comment 2

> Username: danieljames  
> Created_at: 2014-06-01 18:41:52 UTC  
> Updated_at: 2014-06-01 18:42:19 UTC  
> Url: https://github.com/boostorg/core/pull/1#issuecomment-44785308  

FWIW I took the description from libs/libraries.htm in the super project.  
  
I don't think this change is appropriate here, as it's only applicable for a short period of time. The libraries this can depend on can change, which is already indicated in the documentation (assert, static assert and predef are mentioned). "serves to break dependencies" won't really be the case once this is finished. Also, "used by other Boost libraries" suggests that users shouldn't use this.  
  
Although I think it would be a good idea to say all those things in the release notes which are more concerned with the present situation.  
  
How about "A collection of simple core utilities with minimal dependencies"?

---

## Comment 3

> Username: Beman  
> Created_at: 2014-06-01 22:25:15 UTC  
> Url: https://github.com/boostorg/core/pull/1#issuecomment-44791313  

On Sun, Jun 1, 2014 at 2:41 PM, Daniel James notifications@github.com  
wrote:  
  
> FWIW I took the description from libs/libraries.htm in the super project.  
>   
> I don't this change is appropriate here, as it's only applicable for a  
> short period of time. The libraries this can be depended on can change,  
> which is already indicated in the documentation (assert, static assert and  
> predef are mentioned). "serves to break dependencies" won't really be the  
> case once this is finished. Also, "used by other Boost libraries" suggests  
> that users shouldn't use this.  
>   
> Although I think it would be a good idea to say all those things in the  
> release notes which are more concerned with the present situation.  
>   
> How about "A collection of simple core utilities with minimal  
> dependencies"?  
  
OK with me.  
  
Thanks,  
  
--Beman

---
