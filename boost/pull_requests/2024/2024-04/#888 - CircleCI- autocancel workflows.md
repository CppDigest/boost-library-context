# #888 CircleCI: autocancel workflows [Merged]

> Username: sdarwin  
> Created at: 2024-04-23 22:25:44 UTC  
> Updated at: 2024-05-07 18:17:09 UTC  
> Merged at: 2024-05-07 18:00:40 UTC  
> Closed at: 2024-05-07 18:00:41 UTC  
> Url: https://github.com/boostorg/boost/pull/888  

Note: create an API token before proceeding.  
  
To prevent a problem with multiple commits occurring at nearly the same time and overwriting snapshots, there are two solutions:    
  
1. Force sequential builds. However, a difficulty is that each build takes 45 minutes, so when 10 commits happen in sequence the final snapshot won't be ready for 7 hours.  
  
or  
  
2. Cancel previous in-progress builds. Only the newest commit is relevant when building a snapshot. This generally seems preferable since it's faster. A small disadvantage is that if one commit out of ten caused an error, it won't be immediately obvious which commit caused the error.  Not often an issue.  
  
This PR cancels in-progress builds. As an enhancement, it checks the time, and only cancels other jobs from within the last 10 minutes. Any job that's been running longer will be unlikely to conflict.  
  
Token  
  
1. Create a Personal API Token https://app.circleci.com/settings/user/tokens (any token name)  
2. In the Project, add an Environment Variable https://app.circleci.com/settings/project/github/boostorg/boost/environment-variables  
Name: PERS_API_TOKEN_BOOST_5   (this name must match the script autocancel.sh)  
Value: the token from step 1

---

## Comment 1

> Username: glenfe  
> Created_at: 2024-05-07 18:17:08 UTC  
> Url: https://github.com/boostorg/boost/pull/888#issuecomment-2099035830  

@mclow  could you please handle the token part of this?

---
