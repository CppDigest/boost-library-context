# #576 build superproject on drone CI [Closed]

> Username: sdarwin  
> Created at: 2021-10-26 17:03:09 UTC  
> Updated at: 2022-03-01 20:44:06 UTC  
> Closed at: 2022-03-01 20:44:05 UTC  
> Url: https://github.com/boostorg/boost/pull/576  

This PR implements two new functionalities:  
- switch the boost superproject to run on Drone instead of CircleCI, solving the issue that the CircleCI account doesn't have credits, causing builds to occasionally be delayed.  
- switch the release-tools scripts to use python3 rather than python2.   
  
A few steps are required: A boostorg level administrator (whoever is merging this) should log into https://drone.cpp.al/, sync repositories, enable this repo (which is boostorg/boost), and in the drone settings we will need to create a jfrog artifactory secret: artifactory_pass. Actually, I can set that after everything else is ready, so let's coordinate in slack.  
  
How to rollback in case of problems:  
  
.circleci/config.yml has been named .circleci/config.yml.original . Move the file back to .circleci/config.yml. Rename .drone.yml to .drone.yml.bck.  
  
At any time in the future, CircleCI can be re-enabled in this way. Do a quick comparison, update .circleci/config.yml to use the latest docker image and release-tools commands.

---

## Comment 1

> Username: sdarwin  
> Created_at: 2022-03-01 20:44:05 UTC  
> Url: https://github.com/boostorg/boost/pull/576#issuecomment-1055841600  

Closing pull request for now. It's still possible to use this but the config needs to be kept up-to-date with the latest CircleCI.

---
