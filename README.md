# macOS Dev Setup

This document describes how I set up my developer environment on a new MacBook or iMac. We will set up popular programming languages (for example [Node](http://nodejs.org/) (JavaScript), [Python](http://www.python.org/), and [Ruby](http://www.ruby-lang.org/)). You may not need all of them for your projects, although I recommend having them set up as they always come in handy.

The document assumes you are new to Mac, but can also be useful if you are reinstalling a system and need some reminder. The steps below were tested on **macOS High Sierra** (10.13), but should work for more recent versions as well.

**Contributing**: If you find any mistakes in the steps described below, or if any of the commands are outdated, do let me know! For any other suggestions, please understand if I don't include everything. This guide was originally written for some friends getting started with programming on a Mac, and as a personal reference for myself. I'm trying to keep it simple!

- [macOS Dev Setup](#macos-dev-setup)
  - [System update](#system-update)
  - [System preferences](#system-preferences)
  - [Security](#security)
  - [iTerm2](#iterm2)
    - [Install](#install)
    - [Beautiful terminal](#beautiful-terminal)
  - [Homebrew](#homebrew)
    - [Install](#install-1)
    - [Usage](#usage)
    - [Homebrew Services](#homebrew-services)
  - [Git](#git)
  - [Visual Studio Code](#visual-studio-code)
  - [Vim](#vim)
  - [Python](#python)
    - [UV](#uv)
    - [pip (replacement via uv)](#pip-replacement-via-uv)
    - [virtualenv](#virtualenv)
  - [Node.js](#nodejs)
    - [npm](#npm)
  - [Ruby](#ruby)
    - [Install](#install-2)
    - [Usage](#usage-1)
    - [RubyGems \& Bundler](#rubygems--bundler)
  - [Heroku](#heroku)
  - [PostgreSQL](#postgresql)
    - [GUI](#gui)
  - [Redis](#redis)
  - [Elasticsearch](#elasticsearch)
    - [Install](#install-3)
    - [Usage](#usage-2)
    - [GUI](#gui-1)
  - [Projects folder](#projects-folder)
  - [Apps](#apps)
  - [AWS CLI](#aws-cli)
  - [AWS CDK](#aws-cdk)

## System update

First thing you need to do, on any OS actually, is update the system! For that: **Apple Icon > About This Mac** then **Software Update...**.

## System preferences

If this is a new computer, there are a couple of tweaks I like to make to the System Preferences. Feel free to follow these, or to ignore them, depending on your personal preferences.

In **Apple Icon > System Preferences**:

- Trackpad > Tap to click
- Keyboard > Key Repeat > Fast (all the way to the right)
- Keyboard > Delay Until Repeat > Short (all the way to the right)
- Dock > Automatically hide and show the Dock

## Security

I recommend checking that basic security settings are enabled. You will be happy to have done so if ever your Mac is lost or stolen.

In **Apple Icon > System Preferences**:

- Users & Groups: If you haven't already set a password for your user during the initial set up, you should do so now
- Security & Privacy > General: Require password immediately after sleep or screen saver begins (you can keep a grace period of a couple minutes if you prefer, but I like to know that my computer locks as soon as I close it)
- Security & Privacy > FileVault: Make sure FileVault disk encryption is enabled
- iCloud: If you haven't already done so during set up, enable Find My Mac

## iTerm2

### Install

Since we're going to be spending a lot of time in the command-line, let's install a better terminal than the default one. Download and install [iTerm2](http://www.iterm2.com/).

In **Finder**, drag and drop the **iTerm** Application file into the **Applications** folder.

You can now launch iTerm, through the **Launchpad** for instance.

Let's just quickly change some preferences. In **iTerm2 > Preferences...**, under the tab **General**, uncheck **Confirm closing multiple sessions** and **Confirm "Quit iTerm2 (Cmd+Q)" command** under the section **Closing**.

In the tab **Profiles**, create a new one with the "+" icon, and rename it to your first name for example. Then, select **Other Actions... > Set as Default**. Under the section **General** set **Working Directory** to be **Reuse previous session's directory**. Finally, under the section **Window**, change the size to something better, like **Columns: 125** and **Rows: 35**.

When done, hit the red "X" in the upper left (saving is automatic in macOS preference panes). Close the window and open a new one to see the size change.

### Beautiful terminal

Since we spend so much time in the terminal, we should try to make it a more pleasant and colorful place. What follows might seem like a lot of work, but trust me, it'll make the development experience so much better.

First let's add some color. There are many great color schemes out there, but if you don't know where to start you can try [Atom One Dark](https://github.com/nathanbuchar/atom-one-dark-terminal). Download the iTerm presets for the theme by running:

```bash
cd ~/Downloads
curl -o "Atom One Dark.itermcolors" https://raw.githubusercontent.com/nathanbuchar/atom-one-dark-terminal/master/scheme/iterm/One%20Dark.itermcolors
curl -o "Atom One Light.itermcolors" https://raw.githubusercontent.com/nathanbuchar/atom-one-dark-terminal/master/scheme/iterm/One%20Light.itermcolors
```

Then, in **iTerm2 Preferences**, under **Profiles** and **Colors**, go to **Color Presets... > Import...**, find and open the **Atom One Dark.itermcolors** file we just downloaded. Repeat these steps for **Atom One Light.itermcolors**.  Now open **Color Presets...** again and select **Atom One Dark** to activate the dark theme (or choose the light them if that's your preference).

Not a lot of colors yet. We need to tweak a little bit our Unix user's profile for that. This is done (on macOS and Linux), in the `~/.bash_profile` text file (`~` stands for the user's home directory).

We'll come back to the details of that later, but for now, just download the files [.bash_profile](https://raw.githubusercontent.com/nicolashery/mac-dev-setup/master/.bash_profile), [.bash_prompt](https://raw.githubusercontent.com/nicolashery/mac-dev-setup/master/.bash_prompt), [.aliases](https://raw.githubusercontent.com/nicolashery/mac-dev-setup/master/.aliases) attached to this document into your home directory (`.bash_profile` is the one that gets loaded, I've set it up to call the others):

```bash
cd ~
curl -O https://raw.githubusercontent.com/nicolashery/mac-dev-setup/master/.bash_profile
curl -O https://raw.githubusercontent.com/nicolashery/mac-dev-setup/master/.bash_prompt
curl -O https://raw.githubusercontent.com/nicolashery/mac-dev-setup/master/.aliases
```

With that, open a new terminal tab (**Cmd+T**) and see the change! Try the list commands: `ls`, `ls -lh` (aliased to `ll`), `ls -lha` (aliased to `la`).

Now we have a terminal we can work with!

(Thanks to Mathias Bynens for his awesome [dotfiles](https://github.com/mathiasbynens/dotfiles).)

Note: simplified this iTerm2 setup to use dark mode (Profiles->Color->Color Presets->Dark Background, use for light mode and dark mode when prompted). See <https://stackoverflow.com/questions/78526264/how-to-set-iterm2-color-to-darkmode-with-powerlevel10k>

## Homebrew

Package managers make it so much easier to install and update applications (for Operating Systems) or libraries (for programming languages). The most popular one for macOS is [Homebrew](http://brew.sh/).

### Install

An important dependency before Homebrew can work is the **Command Line Developer Tools** for **Xcode**. These include compilers that will allow you to build things from source. You can install them directly from the terminal with:

```bash
xcode-select --install
```

Once that is done, we can install Homebrew by copy-pasting the installation command from the [Homebrew homepage](http://brew.sh/) inside the terminal:

```bash
/usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
```

Follow the steps on the screen. You will be prompted for your user password so Homebrew can set up the appropriate permissions.

Once installation is complete, you can run the following command to make sure everything works:

```bash
brew doctor
```

### Usage

To install a package (or **Formula** in Homebrew vocabulary) simply type:

```bash
brew install <formula>
```

To see if any of your packages need to be updated:

```bash
brew outdated
```

To update a package:

```bash
brew upgrade <formula>
```

Homebrew keeps older versions of packages installed, in case you want to rollback. That rarely is necessary, so you can do some cleanup to get rid of those old versions:

```bash
brew cleanup
```

To see what you have installed (with their version numbers):

```bash
brew list --versions
```

### Homebrew Services

A nice extension to Homebrew is [Homebrew Services](https://github.com/Homebrew/homebrew-services). It will automatically launch things like databases when your computer starts, so you don't have to do it manually every time.

Homebrew Services will automatically install itself the first time you run it, so there is nothing special to do.

After installing a service (for example a database), it should automatically add itself to Homebrew Services. If not, you can add it manually with:

```bash
brew services <formula>
```

Start a service with:

```bash
brew services start <formula>
```

At anytime you can view which services are running with:

```bash
brew services list
```

## Git

macOS comes with a pre-installed version of [Git](http://git-scm.com/), but we'll install our own through Homebrew to allow easy upgrades and not interfere with the system version. To do so, simply run:

```bash
brew install git
```

When done, to test that it installed fine you can run:

```bash
which git
```

The output should be `/usr/local/bin/git`.

Let's set up some basic configuration. Download the [.gitconfig](https://raw.githubusercontent.com/nicolashery/mac-dev-setup/master/.gitconfig) file to your home directory:

```bash
cd ~
curl -O https://raw.githubusercontent.com/nicolashery/mac-dev-setup/master/.gitconfig
```

It will add some color to the `status`, `branch`, and `diff` Git commands, as well as a couple aliases. Feel free to take a look at the contents of the file, and add to it to your liking.

Next, we'll define your Git user (should be the same name and email you use for [GitHub](https://github.com/) and [Heroku](http://www.heroku.com/)):

```bash
git config --global user.name "Your Name Here"
git config --global user.email "your_email@youremail.com"
```

They will get added to your `.gitconfig` file.

On a Mac, it is important to remember to add `.DS_Store` (a hidden macOS system file that's put in folders) to your project `.gitignore` files. You also set up a global `.gitignore` file, located for instance in your home directory (but you'll want to make sure any collaborators also do it):

```bash
cd ~
curl -O https://raw.githubusercontent.com/nicolashery/mac-dev-setup/master/.gitignore
git config --global core.excludesfile ~/.gitignore
```

## Visual Studio Code

With the terminal, the text editor is a developer's most important tool. Everyone has their preferences, but if you're just getting started and looking for something simple that works, [Visual Studio Code](https://code.visualstudio.com/) is a pretty good option.

Go ahead and [download](https://code.visualstudio.com/Download) it. Open the **.dmg** file, drag-and-drop in the **Applications** folder, you know the drill now. Launch the application.

**Note**: At this point I'm going to create a shortcut on the macOS Dock for both for Visual Studio Code and iTerm. To do so, right-click on the running application and select **Options > Keep in Dock**.

Just like the terminal, let's configure our editor a little. Go to **Code > Preferences > Settings**. In the very top-right of the interface you should see an icon with brackets that appeared **{ }** (on hover, it should say "Open Settings (JSON)"). Click on it, and paste the following:

```json
{
  "editor.tabSize": 2,
  "editor.rulers": [80],
  "files.insertFinalNewline": true,
  "files.trimTrailingWhitespace": true,
  "workbench.editor.enablePreview": false
}
```

Feel free to tweak these to your preference. When done, save the file and close it.

Pasting the above JSON snippet was handy to quickly customize things, but for further setting changes feel free to search in the "Settings" panel that opened first (shortcut **Cmd+,**). When you're happy with your setup, you can save the JSON to quickly restore it on a new machine.

If you remember only one keyboard shortcut in VS Code, it should be **Cmd+Shift+P**. This opens the **Command Palette**, from which you can run pretty much anything.

Let's open the command palette now, and search for `Shell Command: Install 'code' command in PATH`. Hit enter when it shows up. This will install the command-line tool `code` to quickly open VS Code from the terminal. When in a projects directory, you'll be able to run:

```bash
cd myproject/
code .
```

VS Code is very extensible. To customize it further, open the **Extensions** tab on the left.

Let's do that now to customize the color of our editor. Search for the [Atom One Dark Theme](https://marketplace.visualstudio.com/items?itemName=akamud.vscode-theme-onedark) extension, select it and click **Install**. Repeat this for the [Atom One Light Theme](https://marketplace.visualstudio.com/items?itemName=akamud.vscode-theme-onelight).

Finally, activate the theme by going to **Code > Preferences > Color Theme** and selecting **Atom One Dark** (or **Atom One Light** if that is your preference).

## Vim

Although VS Code will be our main editor, it is a good idea to learn some very basic usage of [Vim](http://www.vim.org/). It is a very popular text editor inside the terminal, and is usually pre-installed on any Unix system.

For example, when you run a Git commit, it will open Vim to allow you to type the commit message.

I suggest you read a tutorial on Vim. Grasping the concept of the two "modes" of the editor, **Insert** (by pressing `i`) and **Normal** (by pressing `Esc` to exit Insert mode), will be the part that feels most unnatural. Also, it is good to know that typing `:x` when in Normal mode will save and exit. After that, it's just remembering a few important keys.

Vim's default settings aren't great, and you could spend a lot of time tweaking your configuration (the `.vimrc` file). But if you only use Vim occasionally, you'll be happy to know that [Tim Pope](https://github.com/tpope) has put together some sensible defaults to quickly get started.

Using Vim's built-in package support, install these settings by running:

```bash
mkdir -p ~/.vim/pack/tpope/start
cd ~/.vim/pack/tpope/start
git clone https://tpope.io/vim/sensible.git
```

With that, Vim will look a lot better next time you open it!

## Python

Python version, package, and project management will be done using [Astral's uv](https://docs.astral.sh/uv/).

### UV

```bash
brew install uv
```

We can now list all available Python versions by running:

```bash
uv python list
```

A new version of Python can be installed in the following way:

```bash
uv python install 3.13
```

`uv` supersedes the legacy `pip` workflow with new commands like `init`, `add`, and `sync`, see the [Working on projects page](https://docs.astral.sh/uv/guides/projects/).

### pip (replacement via uv)

A [pip](https://docs.astral.sh/uv/pip/) replacement is available through `uv`. It is a way to manage packages for Python.

Here are a couple uv + Pip commands to get you started. To install a Python package:

```bash
uv pip install <package>
```

To upgrade a package:

```bash
uv pip install --upgrade <package>
```

To see what's installed:

```bash
uv pip list
```

To list what's installed in a `requirements.txt` format:

```bash
uv pip freeze
```

To uninstall a package:

```bash
uv pip uninstall <package>
```

### virtualenv

To use your project's virtualenv, you need to **activate** it first (in every terminal where you are working on your project):

```bash
source .venv/bin/activate
```

Now when you install something:

```bash
uv pip install <package>
```

It will get installed in that virtualenv's folder, and not conflict with other projects.

## Node.js

The recommended way to install [Node.js](http://nodejs.org/) is to use [nvm](https://github.com/creationix/nvm) (Node Version Manager) which allows you to manage multiple versions of Node.js on the same machine.

Install `nvm` by copy-pasting the [install script command](https://github.com/creationix/nvm#install--update-script) into your terminal.

Once that is done, open a new terminal and verify that it was installed correctly by running:

```bash
command -v nvm
```

View the all available stable versions of Node with:

```bash
nvm ls-remote --lts
```

Install the latest stable version with:

```bash
nvm install node
```

It will also set the first version installed as your default version. You can install another specific version, for example Node 10, with:

```bash
nvm install 10
```

And switch between versions by using:

```bash
nvm use 10
nvm use default
```

See which versions you have install with:

```bash
nvm ls
```

Change the default version with:

```bash
nvm alias default 10
```

In a project's directory you can create a `.nvmrc` file containing the Node.js version the project uses, for example:

```bash
echo "10" > .nvmrc
```

Next time you enter the project's directory from a terminal, you can load the correct version of Node.js by running:

```bash
nvm use
```

### npm

Installing Node also installs the [npm](https://npmjs.org/) package manager.

To install a package:

```bash
npm install <package> # Install locally
npm install -g <package> # Install globally
```

To install a package and save it in your project's `package.json` file:

```bash
npm install --save <package>
```

To see what's installed:

```bash
npm list --depth 1 # Local packages
npm list -g --depth 1 # Global packages
```

To find outdated packages (locally or globally):

```bash
npm outdated [-g]
```

To upgrade all or a particular package:

```bash
npm update [<package>]
```

To uninstall a package:

```bash
npm uninstall --save <package>
```

## Ruby

Like Python, [Ruby](http://www.ruby-lang.org/) is already installed on Unix systems. But we don't want to mess around with that installation. More importantly, we want to be able to use the latest version of Ruby.

### Install

The recommended way to install Ruby is to use [rbenv](https://github.com/rbenv/rbenv), which allows you to manage multiple versions of Ruby on the same machine. You can install `rbenv` with Homebrew:

```bash
brew install rbenv
```

After installation, add the following line to your `.bash_profile`:

```bash
eval "$(rbenv init -)"
```

And reload it with:

```bash
source ~/.bash_profile
```

### Usage

The following command will show you which versions of Ruby are available to install:

```bash
rbenv install --list
```

You can find the latest version in that list and install it with (replace `.x.x` with actual version numbers):

```bash
rbenv install 2.x.x
```

Run the following to see which versions you have installed:

```bash
rbenv versions
```

The start (`*`) will show you that we are currently using the default `system` version. You can switch your terminal to use the one you just installed:

```bash
rbenv shell 2.x.x
```

You can also set it as the default version if you want:

```bash
rbenv global 2.x.x
```

In a specific project's directory, you can ask `rbenv` to create a `.ruby-version` file. Next time you enter that project's directory from the terminal, it will automatically load the correct Ruby version:

```bash
rbenv local 2.x.x
```

Check anytime which version you are using with:

```bash
rbenv version
```

See [rbenv's command reference](https://github.com/rbenv/rbenv#command-reference) for more information.

### RubyGems & Bundler

[RubyGems](http://rubygems.org/), the Ruby package manager, was also installed:

```bash
which gem
```

The first thing you want to do after installing a new Ruby version is to install [Bundler](https://bundler.io/). This tool will allow you to set up separate environments for your different Ruby projects, so their required gem versions won't conflict with each other. Install Bundler with:

```bash
gem install bundler
```

In a new Ruby project directory, create a new `Gemfile` with:

```bash
bundle init
```

Add a dependency to the `Gemfile`, for example the [Jekyll]() static site generator:

```ruby
source "https://rubygems.org"

gem "jekyll"
```

Then install the project's dependencies with:

```bash
bundle install
```

Make sure you check in both the `Gemfile` and `Gemfile.lock` into your Git repository.

Update a specific dependency with:

```bash
bundle update <gem>
```

For more information, see the [Bundler documentation](https://bundler.io/docs.html).

## Heroku

[Heroku](http://www.heroku.com/) is a [Platform-as-a-Service](http://en.wikipedia.org/wiki/Platform_as_a_service) (PaaS) that makes it really easy to deploy your apps. There are other similar solutions out there, but Heroku is among the most popular. Not only does it make a developer's life easier, but I find that having Heroku deployment in mind when building an app forces you to follow modern app development [best practices](http://www.12factor.net/).

Assuming that you have an account (sign up if you don't), let's install the [Heroku CLI](https://devcenter.heroku.com/articles/heroku-cli):

```bash
brew tap heroku/brew
brew install heroku
```

Login to your Heroku account using:

```bash
heroku login
```

(This will prompt you to open a page in your web browser and log in to your Heroku account.)

Once logged-in, you're ready to deploy apps! Heroku has great [Getting Started](https://devcenter.heroku.com/start) guides for different languages, so I'll let you refer to that. Heroku uses Git to push code for deployment, so make sure your app is under Git version control. A quick cheat sheet (if you've used Heroku before):

```bash
cd myapp/
heroku create myapp
git push heroku master
heroku ps
heroku logs -t
```

The [Heroku Dev Center](https://devcenter.heroku.com/) is full of great resources, so be sure to check it out!

## PostgreSQL

[PostgreSQL](https://www.postgresql.org/) is a popular relational database, and Heroku has first-class support for it.

Install PostgreSQL using Homebrew:

```bash
brew install postgresql
```

It will automatically add itself to Homebrew Services. Start it with:

```bash
brew services start postgresql
```

If you reboot your machine, PostgreSQL will be restarted at login.

### GUI

You can interact with your SQL database by running `psql` in the terminal.

If you prefer a GUI (Graphical User Interface), [Postico](https://eggerapps.at/postico/) has a simple free version that let's you explore tables and run SQL queries.

## Redis

[Redis](http://redis.io/) is a fast, in-memory, key-value store, that uses the disk for persistence. It complements nicely a database such as PostgreSQL. There are a lot of [interesting things](http://oldblog.antirez.com/post/take-advantage-of-redis-adding-it-to-your-stack.html) that you can do with it. For example, it's often used for session management or caching by web apps, but it has many other uses.

To install Redis, use Homebrew:

```bash
brew install redis
```

Start it through Homebrew Services with:

```bash
brew services start redis
```

I'll let you refer to Redis' [documentation](http://redis.io/documentation) or other tutorials for more information.

## Elasticsearch

[Elasticsearch](https://www.elastic.co/products/elasticsearch) is a distributed search and analytics engine. It uses an HTTP REST API, making it easy to work with from any programming language.

You can use elasticsearch for things such as real-time search results, autocomplete, recommendations, machine learning, and more.

### Install

Elasticsearch runs on Java, so check if you have it installed by running:

```bash
java -version
```

If Java isn't installed yet, dismiss the window that just appeared by clicking "Ok", and install Java via Homebrew:

```bash
brew cask install homebrew/cask-versions/java8
```

Next, install Elasticsearch with:

```bash
brew install elasticsearch
```

### Usage

Start the Elasticsearch server with:

```bash
brew services start elasticsearch
```

Test that the server is working correctly by running:

```bash
curl -XGET 'http://localhost:9200/'
```

(You may need to wait a little bit for it to boot up if you just started the service.)

Elasticsearch's [documentation](https://www.elastic.co/guide/en/elasticsearch/reference/current/index.html) is more of a reference. To get started, you can also take a look at [Elasticsearch: The Definitive Guide](https://www.elastic.co/guide/en/elasticsearch/guide/master/index.html).

### GUI

You can interact with the Elasticsearch server using `curl`, or anything that can send an HTTP request.

However, if you prefer a graphical interface, you can take a look at [Dejavu](https://opensource.appbase.io/dejavu/). You can easily install it via the [Dejavu Chrome Extension](https://chrome.google.com/webstore/detail/dejavu-elasticsearch-web/jopjeaiilkcibeohjdmejhoifenbnmlh).

## Projects folder

This really depends on how you want to organize your files, but I like to put all my version-controlled projects in `~/Projects`. Other documents I may have, or things not yet under version control, I like to put in `~/Dropbox` (if you have [Dropbox](https://www.dropbox.com/) installed), or `~/Documents` if you prefer to use [iCloud Drive](https://support.apple.com/en-ca/HT206985).

## Apps

Here is a quick list of some apps I use, and that you might find useful as well:

- [1Password](https://1password.com/): Securely store your login and passwords, and access them from all your devices. **($3/month)**
- [Dropbox](https://www.dropbox.com/): File syncing to the cloud. It is cross-platform, but if all your devices are Apple you may prefer [iCloud Drive](https://support.apple.com/en-ca/HT206985). **(Free for 2GB)**
- [Postman](https://www.getpostman.com/): Easily make HTTP requests. Useful to test your REST APIs. **(Free for basic features)**
- [GitHub Desktop](https://desktop.github.com/): I do everything through the `git` command-line tool, but I like to use GitHub Desktop just to review the diff of my changes. **(Free)**
- [Spectacle](https://www.spectacleapp.com/): Move and resize windows with keyboard shortcuts. **(Free)**

## AWS CLI

This is for the [AWS CLI](https://docs.aws.amazon.com/cli/latest/userguide/getting-started-quickstart.html) - overall management of AWS.

Install Homebrew [as above](#homebrew).

Use `brew install awscli` to install the AWS CLI.

```bash
brew install awscli
```

Verify the AWS CLI was installed by checking the version number:

```bash
aws --version
aws-cli/2.25.3 Python/3.12.9 Darwin/24.3.0 source/x86_64
```

## AWS CDK

This is for the [AWS CDK v2](https://docs.aws.amazon.com/cdk/v2/guide/getting_started.html).

Install Node.js [as above](#nodejs).

Use `npm` to install the AWS CDK CLI (globally):

```bash
npm install -g aws-cdk
```

Verify the AWS CDK was installed by checking the version number.

```bash
~ cdk --version
2.1006.0 (build a3b9762)
```

Note: as of this writing, AWS CDK is expecting Node.js v22 as the highest supported version, but Node.js v23 is the latest stable version. We'll install Node.js v22 explicitly so it can be used with AWS CDK projects.

```bash
nvm install 22
Downloading and installing node v22.14.0...
Downloading https://nodejs.org/dist/v22.14.0/node-v22.14.0-darwin-x64.tar.xz...
####################################################################################################################################################################################################################### 100.0%
Computing checksum with sha256sum
Checksums matched!
Now using node v22.14.0 (npm v10.9.2)
```

And, repeat the `aws-cdk` installation for `npm`.

```bash
npm install -g aws-cdk
```
