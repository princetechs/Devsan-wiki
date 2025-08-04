
Using Ctrl + R in the terminal (usually bash or zsh) launches **reverse search** through your command history. Here are some **powerful tips** to make the most of it:

---

### **🔍 Basic Usage**

- Press Ctrl + R, then start typing a part of the command you used before.
    

```
(reverse-i-search)`ssh': ssh user@server
```

  

---

### **💡 Tips to Speed It Up**

1. **Search by part of a command**
    
    - You don’t need to type the whole command, just a unique keyword:
        
    

```
Ctrl + R, then type: postgres
```

1.   
    
2. **Keep pressing Ctrl + R** to go further back in history with matching commands.
    
3. **Edit the command before running**
    
    - Once the command appears, press → (Right arrow) or End to edit it before executing.
        
    
4. **Cancel search without running**
    
    - Press Ctrl + G to cancel reverse search without executing anything.
        
    
5. **Forward search**
    
    - If you overshoot your match, use Ctrl + S to move forward (might need to disable terminal flow control: stty -ixon).
        
    
6. **Use partial search from anywhere**
    
    - Instead of reverse searching, try:
        
    

```
!git   # Runs the last command starting with "git"
```

6.   
    
7. **Search and copy without executing**
    
    - After finding your command:
        
        - Press Ctrl + R, find it.
            
        - Press Ctrl + G to cancel.
            
        - Then use Up arrow to go back to it manually and copy/edit it.
            
        
    

---

### **🧠 Pro Tip: Fuzzy History with** 

### **fzf**

  

Install fzf for better history search:

```
brew install fzf
```

Then add this to .zshrc or .bashrc:

```
bindkey '^R' fzf-history-widget
```

It gives a fuzzy, scrollable history UI with previews.

---

Want help setting up fzf or customizing .zshrc?