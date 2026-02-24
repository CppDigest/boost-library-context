# #24 Added max_files option to text_file_backend's file_collector [Merged]

> Username: erichkeane  
> Created at: 2015-12-29 19:25:20 UTC  
> Updated at: 2016-02-25 19:11:16 UTC  
> Merged at: 2016-02-25 19:10:44 UTC  
> Closed at: 2016-02-25 19:10:44 UTC  
> Url: https://github.com/boostorg/log/pull/24  

As requested here: https://svn.boost.org/trac/boost/ticket/8746 this commit introduces the boost::log::keywords::max_files keyword and implements this functionality in the file collector.  
  
Additionally, the non-keyword make_collector call has max_files added as a uintmax_t parameter, which has a default value as to not break existing code.  
  
The purpose of this is to limit the total number of files in the collected logs.  If not specified, the limit will be std::numeric_limits<uintmax_t>::max(), which is likely greater than the capacity of the filesystem.

---

## Comment 1

> Username: erichkeane  
> Created_at: 2015-12-29 19:26:43 UTC  
> Url: https://github.com/boostorg/log/pull/24#issuecomment-167859934  

Wasn't ready yet, I noticed that one of my changes breaks the build, I'll open another one in the near future, I apologies for prematurely doing a pull-request.

---

## Comment 2

> Username: erichkeane  
> Created_at: 2015-12-29 19:31:31 UTC  
> Url: https://github.com/boostorg/log/pull/24#issuecomment-167860689  

I apologize about the churn, I expected the build issue to take longer to deal with.  
  
I'll note that I changed the init_from_settings, which makes it obvious where the parsing is done, however when looking throughout the boost log project, I did not see any place where it was stored.  Did I miss it?

---

## Comment 3

> Username: Lastique  
> Created_at: 2016-02-25 19:11:16 UTC  
> Url: https://github.com/boostorg/log/pull/24#issuecomment-188935248  

Merged, thanks for the patch.

---
