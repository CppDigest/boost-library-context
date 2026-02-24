# #12 - [peer-review] Add intuition behind mathematical equations [Closed]

> Username: Becheler  
> Created at: 2025-05-31 10:19:03 UTC  
> Updated at: 2025-06-24 18:21:21 UTC  
> Closed at: 2025-06-24 18:21:21 UTC  
> Url: https://github.com/boostorg/bloom/issues/12  

> Peter Turcan, May 17: The equations are somewhat daunting and difficult to determine what is good practice and what is not. Perhaps add text to describe best practices in certain situations.  
  
# Suggestions of Best Practices for Equations in Documentation  
  
In Applied Mathematics/Biology papers we general use this setup to talk to both biologists and mathematicians without losing both:  
  
- **Intuitive Explanation (Plain English)**  
  - Describe the purpose and “story” behind the equation without symbols.  
  - Use analogies or simple visuals to illustrate why the formula matters.  
- **Formal Statement (Math Symbols)**  
  - Display the equation in a visually distinct call-out box or shaded block.  
  - Define every symbol immediately below the equation (or in a margin note).  
  
Also I'm used to seeing formal math isolated in standalone boxes across papers: it is useful to isolate high-level technical description from the document flow.  
Maybe it's something that can be applied here.  
  
```markdown  
> **Intuition (Plain-English):**    
> Describe what the equation achieves and why it’s useful in everyday terms.  
>  
> **(Optional) Visual/Analogy:**    
> Provide a short diagram or metaphor to reinforce understanding.  
>  
> ```latex  
> \[  
>   \text{(Equation Here)}  
> \]  
> ```  
> <small>  
> **Where**    
> - \(a\) = …    
> - \(b\) = …    
> - \(\ln\) = natural logarithm    
> </small>  
>  
> **Step-by-Step Numeric Example:**    
> 1. Substitute sample values.    
> 2. Show intermediate calculations in plain language.    
> 3. Present the final numeric result and interpretation.

---

## Comment 1

> Username: joaquintides  
> Created at: 2025-06-14 08:26:51 UTC  
> Url: https://github.com/boostorg/bloom/issues/12#issuecomment-2972475238  

I'm totally lost with this one. Requoting Peter's observation:  
> *Appendix A*  
The equations are somewhat daunting and difficult to determine what is good  
practice and what is not. Perhaps add text to describe best practices in  
certain situations.   
  
I don't understand what is meant by "best practices" in this context. The appendix is added for completeness (and, well, because I love math formulas) to inform those interested in the implementation of `fpr_for`. As an appendix, it can be skipped altogether by users of the library. I honestly don't know how to make these formulas more palatable to people without some mathematical fluency. I could show the derivation of some of the formulas but I doubt this will help casual readers at all.

---

## Comment 2

> Username: Becheler  
> Created at: 2025-06-14 08:33:09 UTC  
> Url: https://github.com/boostorg/bloom/issues/12#issuecomment-2972479789  

I guess I can help you with that: if someone as naive as I am can understand the formulas it means the documentation is good enough :D   
  
I will go through the maths during the weekend and come back with suggestions (or more likely: dumb questions)

---

## Comment 3

> Username: Becheler  
> Created at: 2025-06-24 18:20:02 UTC  
> Url: https://github.com/boostorg/bloom/issues/12#issuecomment-3001459055  

I am sorry I could not find time to properly review the mathematical equations as promised. I will close the issue for now as it is needed for merging the review branch, and may reopen it in the future when I'm ready to go through the maths 😁

---

## Comment 4

> Username: Becheler  
> Created at: 2025-06-24 18:20:02 UTC  
> Url: https://github.com/boostorg/bloom/issues/12#issuecomment-3001459065  

I am sorry I could not find time to properly review the mathematical equations as promised. I will close the issue for now as it is needed for merging the review branch, and may reopen it in the future when I'm ready to go through the maths 😁
