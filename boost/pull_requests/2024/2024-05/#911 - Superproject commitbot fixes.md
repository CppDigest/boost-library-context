# #911 Superproject commitbot fixes [Merged]

> Username: sdarwin  
> Created at: 2024-05-22 19:47:43 UTC  
> Updated at: 2024-05-23 16:05:17 UTC  
> Merged at: 2024-05-23 16:05:17 UTC  
> Closed at: 2024-05-23 16:05:17 UTC  
> Url: https://github.com/boostorg/boost/pull/911  

Note: Before merging, please add a gha secret "CI_PAT" .  
  
- In a personal github account, create a token that has permission to push to this superproject.    
- At https://github.com/boostorg/boost/settings/secrets/actions  create a gha secret "CI_PAT", and enter the token.    
  
From https://github.com/orgs/community/discussions/25702  "If an action pushes code using the repository’s GITHUB_TOKEN, a new workflow will not run"    
  
Updates in this PR:  
  
- Modify the git token to be `secrets.CI_PAT`  
- Only run on a cron schedule, not from pushes  
- Fix the git submodule url variable. During testing it had been set to a full url such as `https://github.com/boostorg/math.git`. In production the format is `../math.git`.  Convert to a url.    
  
cc. @glenfe @pdimov

---
