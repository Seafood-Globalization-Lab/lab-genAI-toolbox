# lab-genAI-toolbox
A collection of tools and resources produced from the Seafood Globalization Lab's ongoing discussion of our GenAI policy. 

> [!CAUTION]
> As of 2026-06-18 this is very much a work in progress and all outlined features may not be fully working. As always, careful human review and editing of GenAI work is required.

## Add this toolbox to your project repo

Run this in your terminal to clone the toolbox repo into your active repo as a hidden folder. 
This records a pointer to a specific commit SHA of the toolbox repo. 

```bash
git submodule add https://github.com/Seafood-Globalization-Lab/lab-genAI-toolbox .lab-genAI-toolbox
```

## Update tool box in your project repo

```bash
cd .lab-genAI-toolbox
git pull          # fetches latest toolbox commits, moves pointer to new SHA
cd ..
git add .lab-genAI-toolbox    # stages the new SHA pointer in your project
git commit -m "chore: update toolbox to vx.x"
```
