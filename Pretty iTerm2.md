# iTerm2 + Oh My Zsh + Powerlevel10K

## 1. Install and configure iTerm2

Install iterm2 by using `brew`

Run the below command in the MacOS Terminal:

```bash
brew install --cask iterm2
```

### Configure Color Theme

The color theme can be changed in the `Settings` by 

iTerm2 → Preferences... → Profiles → Colors → Color Presets...

You can also find the [iterm2 color schemes](https://iterm2colorschemes.com) online and load the theme that you like

### Configure Transparency and Blur

The background transparency and blur can be set at:

iTerm2 → Preferences... → Profiles → Window

Enable the `Blur` and do the adjustment that you like.

## 2. Install Oh My Zsh

The more details can be found [here](https://github.com/ohmyzsh/ohmyzsh)

Run the below command to install the Oh My Zsh

curl:
```bash
sh -c "$(curl -fsSL https://raw.githubusercontent.com/ohmyzsh/ohmyzsh/master/tools/install.sh)"
```

Or

wget:
```
sh -c "$(fetch -o - https://raw.githubusercontent.com/ohmyzsh/ohmyzsh/master/tools/install.sh)"
```

## 3. Powerlevel10K

Powerlevel10K is a Zsh theme, the details can be found [here](https://github.com/romkatv/powerlevel10k)

Installation
```bash
git clone --depth=1 https://github.com/romkatv/powerlevel10k.git ~/powerlevel10k
echo 'source ~/powerlevel10k/powerlevel10k.zsh-theme' >>~/.zshrc
```

Once you install the Powerlevel10k, there will be a Powerlevel10k configure wizard shows when you restart the iTerm2.

Download the `Meslo Nerd Fonts` that Powerlevel10k recommand you and follow the options to config your settings that you prefer.

You can also run

```bash
p10k configure
```
to reconfigure your Powerlevel10k Settings.


## 4. Zsh Auto Suggestion

For `Oh My Zsh`, you can install [zsh-autosuggestions](https://github.com/zsh-users/zsh-autosuggestions/blob/master/INSTALL.md#oh-my-zsh) plugin. 


1. Clone this repository into `$ZSH_CUSTOM/plugins` (by default `~/.oh-my-zsh/custom/plugins`)

    ```sh
    git clone https://github.com/zsh-users/zsh-autosuggestions ${ZSH_CUSTOM:-~/.oh-my-zsh/custom}/plugins/zsh-autosuggestions
    ```

2. Add the plugin to the list of plugins for Oh My Zsh to load (inside `~/.zshrc`):

    ```sh
    plugins=(git zsh-autosuggestions)
    ```
    ***Note:*** git is the default plugin that has been added by Oh My Zsh

3. Start a new terminal session.

## 5. Syntax highlighting

Install the zsh-syntax-highlighting

Run the below command:

```zsh
brew install zsh-syntax-highlighting
```

After the installation, add the following line

```zsh
source /usr/local/share/zsh-syntax-highlighting/zsh-syntax-highlighting.zsh
```

To the end of the `~/.zshrc` file and then run:

```zsh
source ~/.zshrc
```

## 6. Visual Studio Code Config
After you install the Powerlevel10k, it will ask you to install the font `MesloLGS NF`.

The new installed font (`MesloLGS NF`) will mess up the VS Code terminal.

Open the VS Code Settings (Cmd + ,), locating the font part and edit `settings.json`. 

Add `"terminal.integrated.fontFamily": "MesloLGS NF"` and save the file.


## 7. Main reference
[iTerm2 + Oh My Zsh + Solarized color scheme + Source Code Pro Powerline + Font Awesome + [Powerlevel10k] - (macOS)](https://gist.github.com/kevin-smets/8568070)
