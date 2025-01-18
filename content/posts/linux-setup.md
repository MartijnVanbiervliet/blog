+++
date = '2025-01-13T21:48:55+01:00'
draft = true
title = 'My personal Linux setup'
+++

As a software developer, I spend a lot of time in the terminal.  The terminal is the holy grail of power users. Every action you take is directly at your fingertips - the keys on your keyboard. Learning to use the terminal efficiently not only boosts your productivity but also makes the experience more enjoyable. And for things you use every day, what’s stopping you from making it more enjoyable?

Brandon Rhodes expresses it well in this [talk](https://youtu.be/I56oFTm9UlE?si=9gMgSN2pq7l2tNmF). Every once in a while, it’s a good idea to stop to sharpen your tools. It’s no use mowing a large field of grass with a blunt scythe.

Learning how your tools work is essential if you use them day in and day out. You can tailor the configuration to your specific preferences and requirements to get the best out of it.

In this guide, I'll share the tools and configurations I use when setting up a new Linux/Unix terminal environment. If you want, you can copy it entirely. But to get the most enjoyment out of it, I recommend to use it as a source of inspiration. Here's some other sources that inspired me on this topic:

* [mischavandenburg](https://github.com/mischavandenburg/dotfiles/tree/main)
* [bartekspitza](https://youtu.be/mSXOYhfDFYo?si=XOWWMjgAvOWjGMcx)
* [ThePrimeagen](https://github.com/ThePrimeagen)
* [omerxx](https://github.com/omerxx/dotfiles/tree/master)

# Basic setup
The following instructions assume you're on a Linux system with the `apt` package manager, like Ubuntu or Debian. However, if you're using other distro's that's fine as well, just replace `apt` with the package manager of your choice.

### Update apt
```bash
sudo apt-get update
```
### Install and upgrade basic packages
```bash
sudo apt-get update && sudo apt-get --upgrade install \
	zip unzip sed curl wget git htop ncdu tree
```
### Install Brew
Install the Brew package manager to get access to tools that are not available on `apt`.
https://brew.sh/
Install Brew and run the suggested command to add it to your PATH.
```bash
(echo; echo 'eval "$(/home/linuxbrew/.linuxbrew/bin/brew shellenv)"') >> /home/martijn/.bashrc  
eval "$(/home/linuxbrew/.linuxbrew/bin/brew shellenv)"
```
Install the additional packages:
```bash
apt-get install build-essential
```
```bash
brew install gcc
```

# Configure shell theme
Your shell theme is based on personal preference, and I sometimes switch between different ones. When getting started, you'll need to choose your shell technology: Bash, Zsh, or Fish are the most popular options. For beginners, I recommend either Bash or Zsh since most tools we'll be installing support their configuration formats.

###  Zsh with Powerlevel10K theme (optional)
We can use Zsh to improve and augment our terminal, feels more familiar to MacOS systems. Or choose to keep using Bash and [install Oh My Bash instead](#Oh My Bash).

Use Oh My Zsh to manage the Zsh configuration.
First, [install Zsh using apt or brew](https://github.com/ohmyzsh/ohmyzsh/wiki).
```bash
brew install zsh
```
Now [install Oh My Zsh](https://github.com/ohmyzsh/ohmyzsh/wiki).
```bash
sh -c "$(curl -fsSL https://raw.githubusercontent.com/ohmyzsh/ohmyzsh/master/tools/install.sh)"
```
Install a [nerd font for the terminal](https://github.com/romkatv/powerlevel10k?tab=readme-ov-file#fonts) to support more icons.
Install the [Powerlevel10k theme for Oh My Zsh](https://github.com/romkatv/powerlevel10k?tab=readme-ov-file#manual).

### Oh My Bash
Consider skipping Zsh and modifying Bash instead with [Oh My Bash](https://github.com/ohmybash/oh-my-bash)

### Starship (Bash, Zsh, Fish and others)
Modify the Bash shell with Starship
https://starship.rs/guide/
```
curl -sS https://starship.rs/install.sh | sh
```

# Install personal dotfiles
Every time you move to a new machine, it's quite cumbersome to set everything up. Luckily on Linux, a lot of your basic configuration is stored inside dotfiles and `.config` inside your home directory. You can store them on a Git repository or any other place where you can access them easily. I stored my dotfiles in a public Github repository, so I can easily clone them on any machine I work on.

### Github CLI
Get the GitHub CLI to easily authorize with private git repositories.
[Install using Brew](https://github.com/cli/cli?tab=readme-ov-file#homebrew)
```bash
brew install gh
```
Authorize with Github
```bash
gh auth login
```
### Get dotfiles
```bash
gh repo clone MartijnVanbiervliet/dotfiles
```

In my dotfiles repository, I've included an installer script that will symlink the dotfiles to my home directory. Using parameters to the installer script, you can choose to install only specific parts of the configuration. I recommend setting up your own repository for your personal dotfiles!
```bash
chmod +x ~/dotfiles/installer
~/dotfiles/installer
```

# Text editor
Modern code and text editors are ubiquitous. There are dozens to choose from. However, the old-school Vim - introduced in 1991 - still has a large following thanks to its keyboard-first approach. Even though, most developers remember it as the editor they couldn’t close when navigating through files on a Linux server, spawning an entire category of internet memes.

Vim encourages users to abandon their mouse and perform all actions with the keyboard. Although there’s a steeper learning curve, this experience allows you to perform frequent actions faster and more satisfyingly.  By not having to remove your hands from the keyboard and staying inside the terminal, there are fewer distractions to your work. Since I tried Vim motions - the default keyboard strokes and combos in Vim - I have fully embraced them in writing and coding. There is a reason why most modern code editors support Vim motions out-of-the-box or using a plugin.

However, Vim misses a lot of those features that modern code editors support and programmers can not live without: extensions, code debuggers, language engines, and visually interesting graphical interfaces. Neovim, a newer and modern version of Vim, attempts to solve all these limitations.

## Neovim
[Use Brew to install](https://github.com/neovim/neovim/blob/master/INSTALL.md#homebrew-on-macos-or-linux)
```bash
brew install neovim
```

### LazyVim
[Install LazyVim](http://www.lazyvim.org/installation) from scratch for a clean install. Or choose for the personal configuration as in the following section

### Personal NeoVim config
```bash
cd ~/.config
gh repo clone MartijnVanbiervliet/nvim-config
ln -s ~/.config/nvim-config ~/.config/nvim
```

# Terminal multiplexer
## Tmux
tmux is the go-to terminal multiplexer for the Unix shell.

Install tmux plugin manager
```bash
git clone https://github.com/tmux-plugins/tpm ~/.tmux/plugins/tpm
```
Install tmux config from dotfiles
```
gh repo clone MartijnVanbiervliet/dotfiles
cd dotfiles
./install.sh
```
Start tmux `tmux` and install plugins: `Ctrl+b I`
To use tmux resurrect, use `Ctrl+b + Ctrl+s` to save
Detach from tmux session: `Ctrl+b d`
Attach to tmux session: `tmux a`

# Optional

### WSLU (If using WSL)
If using Windows Subsystem For Linux, [install WSLU](https://wslutiliti.es/wslu/install.html#ubuntu) to get more tools.

```bash
sudo add-apt-repository ppa:wslutilities/wslu
sudo apt update
sudo apt install wslu
```
Set BROWSER env to support opening host browser from WSL VM.
```bash
echo "export BROWSER=wslview" >> ~/.bashrc
````
This adds support to open your browser to authenticate with Github CLI later in the installation.
[Reference](https://superuser.com/questions/1262977/open-browser-in-host-system-from-windows-subsystem-for-linux)

### LazyGit
[Install LazyGit](https://github.com/jesseduffield/lazygit?tab=readme-ov-file#installation)
```bash
brew install lazygit
```

### tldr-pages
[Install tldr-pages](https://github.com/tldr-pages/tldr?tab=readme-ov-file#how-do-i-use-it) 
tldr-pages is a super useful tool for getting a quick glance at how to use a command-line tool. Instead of digigng through the man pages, tldr shows - at a glance - what the most prevalent way is to use that command. tldr-pages is a community-driven open-source project that can be use addition to traditional `man` pages.

### Pyenv
Manage multiple Python versions using `pyenv`. An essential tool for Python developers. It allows you to install and manage multiple versions of Python. This is a must-have if you're working on Python projects that are based on different versions.
[Install with Brew.](https://github.com/pyenv/pyenv?tab=readme-ov-file#homebrew-in-macos)
[Set up the shell environment for Bash or Zsh.](https://github.com/pyenv/pyenv?tab=readme-ov-file#set-up-your-shell-environment-for-pyenv)

### NodeJS and NPM
Install these to install Pyright via Mason on Neovim
https://github.com/nvm-sh/nvm

# Other interesting command line tools
https://www.youtube.com/watch?v=2OHrTQVlRMg
* exa (better ls)
* ripgrep (better grep)
* bat (cat alternative with syntax highlighting and line numbers)
* fzf (fuzzy finder)
* zoxide (better cd)
* entr (pipe to this tool to automatically rerun based on given files changes)
* midnight commander (file explorer)
https://www.youtube.com/watch?v=szehPBOwqlI
* rmline
* rsync
* rclone (rsync for cloud storage)
* btop (top, htop alternative)
* ntfy (notifications from terminal on mac)

