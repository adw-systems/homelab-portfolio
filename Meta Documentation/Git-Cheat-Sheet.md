### Quick Reference: Daily GitHub Sync Commands

Run these three commands in your PowerShell terminal whenever you finish updating your notes in Obsidian. 

powershell

```
# Step 1: Stage all new and modified notes
git add .

# Step 2: Lock in your changes with a short, professional description
git commit -m "docs: update homelab logs"

# Step 3: Securely upload your notes to your public GitHub profile
git push
```

Use code with caution.

### Useful Status Commands

- `git status` — Check which files have been modified or are currently untracked. 
    
- `git log --oneline` — View a clean, chronological list of your past save points.