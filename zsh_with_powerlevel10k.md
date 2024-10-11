# **Setting Up Zsh with Powerlevel10k for Multiple Users**

## **1. Install Zsh**

### **1.1. Install Zsh**

Install Zsh on the system if it’s not already installed. This is typically done once and applies to all users.

**For Debian-based systems (Ubuntu, etc.):**

```bash
sudo apt update
sudo apt install zsh

```

**For Red Hat-based systems (Fedora, CentOS, etc.):**

```bash

sudo dnf install zsh

```

**For Arch-based systems:**

```bash

sudo pacman -S zsh

```

### **1.2. Set Zsh as Default Shell for Each User**

To set Zsh as the default shell for each user, execute the following for each user:

```bash

sudo chsh -s $(which zsh) username

```

Replace `username` with the actual username of the user.

---

## **2. Install Oh My Zsh**

### **2.1. Install Oh My Zsh for Each User**

1. **Switch to the user account:**
    
    ```bash
    
    sudo su - username
    
    ```
    
2. **Install Oh My Zsh:**
    
    ```bash
    
    sudo apt install curl
    sh -c "$(curl -fsSL https://raw.githubusercontent.com/ohmyzsh/ohmyzsh/master/tools/install.sh)"
    
    ```
    
    Repeat this process for each user account.
    

---

## **3. Install Powerlevel10k**

### **3.1. Clone Powerlevel10k Repository for Each User**

1. **Switch to the user account:**
    
    ```bash
    
    sudo su - username
    
    ```
    
2. **Clone the Powerlevel10k repository:**
    
    ```bash
    
    git clone --depth=1 https://github.com/romkatv/powerlevel10k.git ~/.oh-my-zsh/custom/themes/powerlevel10k
    
    ```
    
    Repeat this process for each user account.
    

### **3.2. Configure Oh My Zsh to Use Powerlevel10k**

1. **Edit `.zshrc` for each user:**
    
    ```bash
    
    nano ~/.zshrc
    
    ```
    
2. **Set Powerlevel10k as the theme:**
    
    ```
    iniCopy code
    ZSH_THEME="powerlevel10k/powerlevel10k"
    
    ```
    
3. **Save and exit:**
    - Press `Ctrl+X`, then `Y`, then `Enter`.
4. **Apply the configuration:**
    
    ```bash
    
    source ~/.oh-my-zsh/custom/themes/powerlevel10k/powerlevel10k.zsh-theme
    
    ```
    
    Repeat these steps for each user account.
    

---

## **4. Configure Powerlevel10k**

### **4.1. Run Powerlevel10k Configuration Wizard for Each User**

1. **Run the configuration wizard:**
    
    ```bash
    
    p10k configure
    
    ```
    
2. **Follow the prompts:** Customize the prompt style, icons, and other settings.
    
    Repeat this process for each user account.
    

### **4.2. Manually Edit Powerlevel10k Configuration**

1. **Open the configuration file:**
    
    ```bash
    
    nano ~/.p10k.zsh
    
    ```
    
2. **Make your changes:** Customize the appearance by editing the settings.
3. **Save and exit:**
    - Press `Ctrl+X`, then `Y`, then `Enter`.
4. **Apply changes:**
    
    ```bash
    
    source ~/.zshrc
    
    ```
    
    Repeat these steps for each user account.
    

---

## **5. Additional Customizations**

### **5.1. Install Recommended Fonts for All Users**

Ensure all users have the recommended fonts installed for Powerlevel10k. Install a font from Nerd Fonts or Powerline Fonts and configure it in each user’s terminal emulator.

### **5.2. Install Plugins for All Users**

1. **Install plugins for each user:**
    
    **Switch to the user account:**
    
    ```bash
    
    sudo su - username
    
    ```
    
    **Clone the plugins:**
    
    ```bash
    
    git clone https://github.com/zsh-users/zsh-autosuggestions ~/.oh-my-zsh/custom/plugins/zsh-autosuggestions
    git clone https://github.com/zsh-users/zsh-syntax-highlighting ~/.oh-my-zsh/custom/plugins/zsh-syntax-highlighting
    
    ```
    
    **Edit `.zshrc` to enable plugins:**
    
    ```bash
    
    nano ~/.zshrc
    
    ```
    
    **Enable plugins:**
    
    ```
    iniCopy code
    plugins=(git zsh-autosuggestions zsh-syntax-highlighting)
    
    ```
    
    **Save and exit:**
    
    - Press `Ctrl+X`, then `Y`, then `Enter`.
    
    **Apply changes:**
    
    ```bash
    
    source ~/.zshrc
    
    ```
    
    Repeat these steps for each user account.
    

---

## **6. Troubleshooting**

### **6.1. Common Issues**

- **Powerlevel10k not appearing:** Ensure `ZSH_THEME="powerlevel10k/powerlevel10k"` is set in `.zshrc` and the file is sourced (`source ~/.zshrc`).
- **Fonts not displaying correctly:** Install the recommended fonts and set them in each user’s terminal emulator.
- **Plugins not working:** Verify plugins are installed in `~/.oh-my-zsh/custom/plugins` and listed in `.zshrc`.

### **6.2. Logs and Errors**

- **View Zsh startup errors:**
    
    ```bash
    
    zsh -x
    
    ```
    
- **Check Oh My Zsh logs:**
    
    ```bash
    
    tail -f ~/.oh-my-zsh/logs/*
    
    ```
    

---

## **7. Conclusion**

By following this guide, you have set up Zsh with Powerlevel10k for multiple users, allowing each user to enjoy a customized and efficient shell experience. Make sure to apply the configurations and customizations individually for each user to ensure consistency across the system.

