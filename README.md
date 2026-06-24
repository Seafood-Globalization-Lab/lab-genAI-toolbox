# lab-genAI-toolbox
A collection of tools and resources produced from the Seafood Globalization Lab's ongoing discussion of our GenAI policy. 

> [!CAUTION]
> As of 2026-06-18 this is very much a work in progress and all outlined features may not be fully working. As always, careful human review and editing of GenAI work is required.

## Add this tool box to your project repo

Run this in your terminal to clone the toolbox repo into your active repo as a hidden folder. 
This records a pointer to a specific commit SHA of the toolbox repo. 

```bash
git submodule add https://github.com/Seafood-Globalization-Lab/lab-genAI-toolbox .lab-genAI-toolbox
```

## Update this tool box in your project repo

```bash
cd .lab-genAI-toolbox
git pull          # fetches latest toolbox commits, moves pointer to new SHA
cd ..
git add .lab-genAI-toolbox    # stages the new SHA pointer in your project
git commit -m "chore: update toolbox to vx.x"
```

## Skills

> ## What are Agent Skills?
>
> Agent Skills are a lightweight, open format for extending AI agent capabilities with specialized knowledge and workflows.
>
>At its core, a skill is a folder containing a `SKILL.md` file. This file includes metadata (`name` and `description`, at minimum) and instructions that tell an agent how to perform a specific task. Skills can also bundle scripts, reference materials, templates, and other resources.
>
> ```
> my-skill/
> ├── SKILL.md          # Required: metadata + instructions
> ├── scripts/          # Optional: executable code
> ├── references/       # Optional: documentation
> ├── assets/           # Optional: templates, resources
> └── ...               # Any additional files or directories
> ```
> Text from [https://github.com/agentskills/agentskills/blob/main/README.md](https://github.com/agentskills/agentskills/blob/main/README.md)

### Create a new skill

  1) Use the directory architecture shown above
  2) Check out the descriptions, instructions and guidence provided by [agentskills.io](https://agentskills.io/home)
  3) Follow the open-standard structure of a skill specified here [agentskills.io/specification](https://agentskills.io/specification)

### Keep in mind

> #### Start from real expertise
>
> A common pitfall in skill creation is asking an LLM to generate a skill without providing domain-specific context — relying solely on the LLM’s general training knowledge. The result is vague, generic procedures (“handle errors appropriately,” “follow best practices for authentication”) rather than the specific API patterns, edge cases, and project conventions that make a skill valuable. Effective skills are grounded in real expertise. The key is feeding domain-specific context into the creation process.
> 
> Text from [https://agentskills.io/skill-creation/best-practices#start-from-real-expertise](https://agentskills.io/skill-creation/best-practices#start-from-real-expertise)

### Advanced skill making (with a skill)

Check out the [Anthropic `skill-creator` skill](https://github.com/anthropics/skills/tree/main/skills/skill-creator) that encapsulates a lot of knowledge and formalized testing and optimization workflows. 
