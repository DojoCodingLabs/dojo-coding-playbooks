# Master Multi-Agent Development with Claude Code, Cursor and Git Worktrees 🚀

## 📌 Learning Objectives

After completing this tutorial you will be able to:

- **Set up a multi-agent environment** using Git Worktrees to isolate the work of different AI agents
- **Automate worktree creation** with Codename Goose and optimized prompts
- **Manage multiple instances of Claude Code and Cursor** working simultaneously on different features
- **Integrate work from multiple agents** resolving conflicts and maintaining code coherence
- **Optimize your development workflow** overcoming the limitations of a single agent working sequentially

## 💡 Introduction

Can you imagine having a team of AI developers working for you simultaneously on the same project? In this tutorial, you'll learn to create and manage an army of coding agents using Claude Code, Git Worktrees, and Codename Goose. The idea is to overcome the limitation that Claude Code can only handle one request at a time, allowing you to perform multiple tasks and significantly accelerate your development workflow.

---

## Tools You'll Need 🛠️

To follow this tutorial, you'll need to familiarize yourself with the following tools:

- **Claude Code**: A powerful command-line development agent (CLI) created by Anthropic. It's excellent for generating, modifying, and understanding code.
- **Codename Goose**: An open-source tool from Block that acts as a local agent on your machine, capable of manipulating files, folders, and executing commands. We'll use it to automate our environment setup.
- **Cursor**: An "AI-first" code editor that we'll use to visualize and manage the different folders and branches of our project. It works very well with the integrated terminal.

> **Important note**: This tutorial is based on current versions of the tools (July 2025). Claude Code is in preview and some configurations may change with future updates.

## The Problem: Multitasking with AI Agents

When you work with an AI agent like Claude Code, it takes a task, processes it, and gives you a result. During that "thinking" and execution time, you can't assign another task to it. This creates a bottleneck. The obvious solution would be to open multiple instances of the agent, but this presents a new problem: if all agents work on the same branch (main, for example), they can overwrite each other's work, generating conflicts and chaos in the code.

## The Solution: Git Worktrees for Isolation

This is where `git worktree` comes into play. This Git functionality allows us to have multiple branches of the same repository "deployed" in different folders simultaneously. Each folder (or worktree) behaves like an independent repository with its own branch, but all are connected to the same underlying `.git` repository.

This means we can assign each AI agent its own folder and its own branch to work on. Once each agent has completed its task, we can merge their work back to the main branch, just as we would do with a human development team.

## Manual Setup: Step by Step with Git Worktrees

Before automating the process, it's important to understand how manual setup works. This will give you a solid foundation to later appreciate the automation.

### Initial Project Setup

1. **Base Project Structure**
   ```
   my-project/
   ├── app/              # Main repository 
   │   ├── src/
   │   ├── .git/
   │   ├── package.json
   │   └── README.md
   └── worktrees/        # Directory for agents (we'll create this)
       ├── feature-auth/
       ├── feature-dashboard/
       └── feature-api/
   ```

2. **Create the Worktrees Directory**
   ```bash
   # From the project root directory
   mkdir worktrees
   cd app/  # Navigate to the main repository
   ```

### Manual Agent Creation with Worktrees

3. **Create the First Agent (Example: Authentication)**
   ```bash
   # Create worktree with new branch
   git worktree add ../worktrees/feature-auth -b feature/auth
   
   # Verify it was created correctly
   git worktree list
   ```

4. **Configure the Agent Environment**
   ```bash
   # Navigate to the new worktree
   cd ../worktrees/feature-auth/
   
   # Copy base configurations
   cp -r ../../app/.claude ./
   cp -r ../../app/.vscode ./
   cp ../../app/package.json ./
   cp ../../app/.gitignore ./
   ```

5. **Create Unique VS Code/Cursor Configuration**
   
   Create `.vscode/settings.json`:
   ```json
   {
     "workbench.colorTheme": "Monokai",
     "workbench.colorCustomizations": {
       "titleBar.activeBackground": "#FF6B35",
       "titleBar.activeForeground": "#FFFFFF",
       "activityBar.background": "#FF6B35",
       "statusBar.background": "#FF6B35"
     },
     "window.title": "${rootName} - Agent AUTH"
   }
   ```

6. **Create Agent-Specific README.md**
   ```markdown
   # Authentication Agent 🔐
   
   ## Responsibilities
   - Implement login/logout system
   - User session management
   - JWT token integration
   - Authentication middleware
   
   ## Project Context
   This worktree is exclusively dedicated to developing
   the authentication system. You work on the 
   feature/auth branch and should focus only on these functionalities.
   ```

7. **Make Initial Commit**
   ```bash
   git add .
   git commit -m "feat: initialize authentication worktree"
   ```

### Repeat the Process for More Agents

8. **Create Second Agent (Dashboard)**
   ```bash
   cd ../../app/  # Return to main repo
   git worktree add ../worktrees/feature-dashboard -b feature/dashboard
   
   cd ../worktrees/feature-dashboard/
   cp -r ../../app/.claude ./
   cp -r ../../app/.vscode ./
   cp ../../app/package.json ./
   cp ../../app/.gitignore ./
   ```

9. **Configure Unique Colors for Dashboard**
   
   `.vscode/settings.json`:
   ```json
   {
     "workbench.colorTheme": "Dark+ (default dark)",
     "workbench.colorCustomizations": {
       "titleBar.activeBackground": "#00D4AA",
       "titleBar.activeForeground": "#FFFFFF",
       "activityBar.background": "#00D4AA",
       "statusBar.background": "#00D4AA"
     },
     "window.title": "${rootName} - Agent DASHBOARD"
   }
   ```

### Open and Manage Multiple Instances

10. **Open Each Agent in Cursor/VS Code**
    ```bash
    # Open each worktree in a separate window
    cursor ../worktrees/feature-auth
    cursor ../worktrees/feature-dashboard
    cursor ../worktrees/feature-api
    ```

11. **Initialize Claude Code in Each Instance**
    ```bash
    # In each terminal of each window
    claude
    ```

12. **Verify Configuration**
    - Each window should have a different color in the title bar
    - Each agent should be on its corresponding branch
    - Configuration files should be present

### Manual Workflow

13. **Assign Specific Tasks**
    ```bash
    # In the AUTH agent window
    > Read your README.md and start implementing the JWT authentication system
    
    # In the DASHBOARD agent window  
    > Read your README.md and create a dashboard with reusable components
    ```

14. **Integrate Changes Manually**
    ```bash
    # Once an agent completes its work
    cd worktrees/feature-auth/
    git add .
    git commit -m "feat: implement complete JWT authentication"
    
    # Return to main repo and merge
    cd ../../app/
    git merge feature/auth
    ```

### Advantages of the Manual Process

- **Total Control**: You understand each step of the process
- **Customization**: You can adjust each configuration specifically
- **Debugging**: Easier to identify configuration problems
- **Learning**: You deeply understand how worktrees work

## Automating Agent Creation with Goose and an Intelligent Prompt

Now that you understand the manual process, we'll automate using Codename Goose so you can create multiple agents in seconds.

### The Prompt for Worktree Creation

This prompt tells an agent (in our case, Goose executing Claude Code) to configure the entire environment for us.

```
🎯 PROMPT FOR CLAUDE CODE:
Create new feature worktrees with organized structure and unique VS Code configurations. Execute the entire configuration process immediately.

**FIRST: Ask the user what feature branches should be created.**

Question: "What feature branches would you like to create? Please provide the feature names (without the 'feature-' prefix). For example: auth, dashboard, payments, notifications"

Wait for the user's response with the list of features, then proceed with execution.

**Expected Project Structure:**
[project-name]/
├── app/ # Main application (git repository)
│   ├── src/
│   ├── .claude/
│   └── ... (application files)
└── worktrees/ # Feature worktrees directory
    ├── feature-auth/ # Feature worktrees to create
    ├── feature-dashboard/
    ├── feature-payments/
    └── feature-[user-specified]/

**FOR EACH FEATURE SPECIFIED BY THE USER, EXECUTE THESE STEPS:**

1. **Validate Current Environment**
   - Verify we are in the correct project directory
   - Check if git repository exists in app/ directory
   - Ensure worktrees/ directory structure is ready

2. **Create Git Worktrees**
   ```bash
   cd app/
   git worktree add ../worktrees/feature-[FEATURE_NAME] -b feature/[FEATURE_NAME]
   ```

3. **Copy Base Configuration**
   ```bash
   cd ../worktrees/feature-[FEATURE_NAME]/
   cp -r ../../app/.claude ./
   cp -r ../../app/.vscode ./ # Copy VS Code/Cursor configurations
   cp ../../app/.gitignore ./
   cp ../../app/package.json ./
   ```

4. **Create Unique VS Code Configuration** (with distinct color schemes so we don't get confused)
   Create `.vscode/settings.json` with feature-specific configurations:
   ```json
   {
     "workbench.colorTheme": "[UNIQUE_THEME_FOR_FEATURE]",
     "workbench.colorCustomizations": {
       "titleBar.activeBackground": "[UNIQUE_COLOR]",
       "titleBar.activeForeground": "#FFFFFF",
       "activityBar.background": "[UNIQUE_COLOR]",
       "statusBar.background": "[UNIQUE_COLOR]"
     },
     "window.title": "${rootName} - Agent [FEATURE_NAME]"
   }
   ```

5. **Create Feature Documentation** (a `README.md` for each agent with their responsibilities)
   Create detailed README.md for each feature with specific responsibilities

6. **Initialize Feature Branches** (making the first commit)
   ```bash
   git add .
   git commit -m "feat: initialize feature worktree [FEATURE_NAME]"
   ```

7. **Verify Each Configuration**
   - Confirm the worktree is correctly linked to the main repository
   - Test that git operations work correctly
   - Verify unique configurations are applied
```

You can find the complete prompt here: [Execution Prompt for Worktree Creation](https://gist.github.com/brolag/76e77785cd5233dbe91bad1d75457550)

### Steps for Automation:

1. **Open Codename Goose**: Navigate to the parent directory containing your application (the `app` folder).

2. **Paste the Prompt**: Copy the previous prompt and paste it into Goose's chat interface.

3. **Define Your Agents**: Goose will ask what feature branches you want to create. Enter the names separated by commas. For example: `landing-page, tutorial-system, code-generator, terminal-simulator, dashboard`.

4. **Execution**: Goose will start creating a `worktrees` folder and inside it, a folder for each agent with its own Git branch, a README.md, and a unique color configuration for VS Code or Cursor.

5. **Create a "Recipe"**: One of Goose's best features is that you can save this chat session as a "recipe". This allows you to reuse the complete process in the future with a single click, without having to copy and paste the prompt again.

## Running Your Army of Agents 🎖️

Once Goose has finished, it will ask you to open each worktree in a separate instance of Cursor.

### Instance Configuration:

1. **Open the Instances**: Accept Goose's suggestion to open each folder. You'll see that each Cursor window has a different color in the title bar, making it easy to identify each agent.

2. **Initialize Claude Code**: In the terminal of each Cursor instance, initialize Claude Code with the `claude` command.

3. **Assign Tasks**: Now you can start giving instructions to each agent independently. A good starting point is asking it to read its own README.md file so it understands its context and responsibilities.

```bash
# In the 'landing-page' agent window
claude
> Please start working on the landing page task. Make sure to read the readme file.
```

While one agent is "thinking", you can jump to another window and give instructions to another agent, optimizing your time to the maximum.

## Managing Workflow and Resolving Conflicts

As each agent completes its work, you need to integrate their changes into the main branch (`main`).

### Make Commits:

In each worktree, commit the changes generated by the agent.

```bash
# Inside worktrees/feature-dashboard
git add .
git commit -m "feat: add initial dashboard structure"
```

### Merge Branches:

Return to your application's main folder (`app`) and merge the feature branch.

```bash
# Inside the /app folder
git merge feature/dashboard
```

### Conflict Resolution:

Inevitably, conflicts will arise! Especially if several agents modify the same file (like `package.json`). When this happens, you can ask Claude Code itself for help resolving them.

```bash
# After a failed merge attempt
claude
> Help me resolve these merge conflicts. When merging the 'terminal-simulator' feature, 
> create a new page for it and add navigation links for all the new features.
```

Claude is surprisingly good at understanding the context of conflicts and proposing logical solutions, like creating new components or pages to avoid collisions.

## Results and Next Steps

At the end of this process, you'll have a `main` branch that integrates the work of all your agents. You'll be able to see the different pages and functionalities that each one created, all merged into a single coherent project.

**Key tip**: The quality of the result depends heavily on the quality of the instructions. Make sure each README.md is very specific about the responsibilities and scope of each agent.

⛩️ ## Final Reflection

Multi-agent development with Git Worktrees represents a paradigmatic shift in how we approach complex software projects. We are no longer limited by the sequential processing of a single agent; instead, we can orchestrate a true team of AI developers, each specialized in a specific part of the project.

This methodology not only significantly accelerates development but also improves code quality by allowing each agent to focus deeply on its specific domain. The combination of Claude Code's power with the perfect isolation of Git Worktrees creates an environment where productivity can multiply exponentially.

The benefits go beyond speed: you get better architecture, more maintainable code, more comprehensive testing, and more detailed documentation. Each agent, working in its specialized area, can dedicate all its processing capacity to perfecting that specific functionality.

## 📚 Extra Resources

### Essential Prompts and Scripts
- **[Execution Prompt for Worktree Creation](https://gist.github.com/brolag/76e77785cd5233dbe91bad1d75457550)** - Main prompt for automation
- **[Prompt for branch creation](https://gist.github.com/brolag/301311f0ff4e41d8cd1ef76075a4423e)** - Management and monitoring scripts
- **[Prompt for semantic commit creation](https://gist.github.com/brolag/647cf9870bb433b5f31db1aeccab9273)** - Techniques for resolving conflicts
- **[Advanced Workflow Templates](https://gist.github.com/brolag/55d232650fcec6f35493398f1ab6adc4)** - Templates for complex projects

### Multi-agent Managers
- **[Magnet](https://www.magnet.run/)** - Platform for orchestrating AI agent teams
- **[Claude Code Agent Farm](https://github.com/Dicklesworthstone/claude_code_agent_farm)** - Distributed system for managing multiple Claude agents
- **[Conductor](https://conductor.build/)** - Framework for building multi-agent applications
- **[Claude Squad](https://github.com/smtg-ai/claude-squad)** - Tools for coordinating Claude teams

### Other AI Development Tools
- **[OpenCode AI](https://opencode.ai/)** - AI-assisted development platform
- **[Gemini CLI](https://github.com/google-gemini/gemini-cli)** - Command line interface for Google Gemini

### Certification and Training
- **[Claude Code in Action - Certification](https://anthropic.skilljar.com/claude-code-in-action)** - Official Claude Code certification course from Anthropic

### Official Documentation
- **[Codename Goose](https://block.xyz/goose)** - Official Codename Goose page
- **[Claude Code](https://claude.ai/code)** - Official Claude Code page
- **[Cursor Documentation](https://docs.cursor.sh/)** - Complete Cursor guide
- **[Worktrees Documentation in Claude Code](https://docs.anthropic.com/en/docs/build-with-claude/claude-code/guides/worktrees)** - Running parallel Claude Code sessions with Git worktrees

### Recommended Reading
- **[The AI Native Software Engineer](https://addyo.substack.com/p/the-ai-native-software-engineer)** - How the developer role evolves with AI
- **[The "Trust but Verify" Pattern for AI Engineering](https://addyo.substack.com/p/the-trust-but-verify-pattern-for)** - Strategies for working effectively with AI
- **[The Prompt Engineering Playbook for Developers](https://addyo.substack.com/p/the-prompt-engineering-playbook-for)** - Complete guide to prompt engineering
- **[AI Won't Kill Junior Devs, But Your Mindset Might](https://addyo.substack.com/p/ai-wont-kill-junior-devs-but-your)** - Perspectives on the future of AI development

### Complementary Resources
- **[Git Worktrees Documentation](https://git-scm.com/docs/git-worktree)** - Official Git Worktrees documentation
- **[Multi-agent Development Best Practices](https://github.com/topics/multi-agent)** - Repositories and examples
- **[Semantic Commits](https://www.conventionalcommits.org/)** - Standard for commit messages

I hope this tutorial is very useful to you! Try this methodology, experiment with different agents and share your results. Happy coding with your new AI army! 🚀