# lab-genAI-toolbox
A collection of tools and resources produced from the Seafood Globalization Lab's ongoing discussion of our GenAI policy. 

## Add this toolbox to your repo

Run this in your terminal to clone the toolbox repo into your active repo as a hidden folder. 
This records a pointer to a specific commit SHA of the toolbox repo. 

```bash
git submodule add https://github.com/your-org/lab-genAI-toolbox .lab-genAI-toolbox
```

## Update tool box in your repo

```bash
cd .artis-toolbox
git pull          # fetches latest toolbox commits, moves pointer to new SHA
cd ..
git add .lab-genAI-toolbox    # stages the new SHA pointer in ARTIS
git commit -m "chore: update toolbox to vx.x"
```