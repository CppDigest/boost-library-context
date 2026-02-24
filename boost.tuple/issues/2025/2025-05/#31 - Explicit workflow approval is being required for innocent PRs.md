# #31 - Explicit workflow approval is being required for innocent PRs [Open]

> Username: saki7  
> Created at: 2025-05-16 14:55:36 UTC  
> Updated at: 2025-05-16 14:58:12 UTC  
> Url: https://github.com/boostorg/tuple/issues/31  

AFAIK, this GitHub warning is intended for protecting repositories from attackers executing arbitrary code on CI. Normal PRs should be able to run tests without approval. If my understanding is correct, this behavior is likely occurring due to CI misconfiguration.  
  
(Noticed on #30.)  
  
![Image](https://github.com/user-attachments/assets/e0b0bcb1-45d3-49a8-aeac-d970dcd78847)  
  
### Expected behavior:   
Such approval should be only required on PRs that explicitly *touch* GHA configuration (`ci.yml`).   
  
### Current behavior:  
Seemingly innocent PR such as #30 leads to warning message demanding explicit approval from maintainers to run the workflows.  
  
### Solution  
  
As per [the documentation](https://docs.github.com/en/actions/managing-workflow-runs-and-deployments/managing-workflow-runs/approving-workflow-runs-from-public-forks#about-workflow-runs-from-public-forks) suggests, someone with access to the "Approval for running fork pull request workflows from contributors" setting need to take a look into this. (I don't have the privilege right now)

---

## Comment 1

> Username: saki7  
> Created at: 2025-05-16 14:58:10 UTC  
> Url: https://github.com/boostorg/tuple/issues/31#issuecomment-2886968216  

Regarding #30, I'm going to press that "Approve workflows to run" button for now.
