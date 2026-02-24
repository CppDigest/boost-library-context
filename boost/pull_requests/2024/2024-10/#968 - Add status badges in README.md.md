# #968 Add status badges in README.md [Open]

> Username: Lastique  
> Created at: 2024-10-21 00:35:01 UTC  
> Updated at: 2024-10-28 22:03:11 UTC  
> Url: https://github.com/boostorg/boost/pull/968  

This PR adds a new script `status/update_status_badges.py`. The script scans through git submodules in the superproject and generates a table with CI status badges as well as other relevant links for all the submodules. Multiple tables can be created for different git branches.  
  
The script inserts the generated content between two marker Markdown comments in `README.md`, removing any previous content between them. This allows the script to be simply rerun to update the list of submodules.  
  
This PR also adds the generated tables with CI badges for master and develop in `README.md`. You may view the result [here](https://github.com/Lastique/boost/tree/feature/add_ci_badges?tab=readme-ov-file#build-status-for-master-branch).  
  
The intended workflow is to run `status/update_status_badges.py` periodically when new git submodules are added or removed from the superproject, or the CI badges are added or removed in the submodules. It might be a good idea to run the script every once in a while (e.g. before a new Boost release preparation starts) to make sure the badges are up to date. Note that you do *not* need to run this script to update badge statuses (i.e. whether the CIs are passing or failing). This happens automatically as this information is pulled from CI via the badge image URLs.  
  
I believe, this information could be useful to Boost release managers (@mclow, @glenfe, @igaztanaga) in order to monitor overall Boost health during the release preparation. This is specifically useful for monitoring libraries that are not actively maintained, as it sometimes happens that breakages that are visible on CI are not noticed by anyone until after the release.  
  
I'm also open to suggestions on how this can be improved to be more useful. Also please let me know if this is not useful at all and not worth the effort.

---

## Comment 1

> Username: Lastique  
> Created_at: 2024-10-28 22:03:09 UTC  
> Url: https://github.com/boostorg/boost/pull/968#issuecomment-2442751284  

Any comments? It might be a good time for a tool like this since 1.87 release is preparing.

---
