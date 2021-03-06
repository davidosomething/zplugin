<p align="center">
<img src="https://raw.githubusercontent.com/zdharma/zplugin/master/doc/img/zplugin.png" />
</p>

[![Build Status](https://travis-ci.org/zdharma/zplugin.svg?branch=master)](https://travis-ci.org/zdharma/zplugin)
[![MIT License](https://img.shields.io/badge/license-MIT-blue.svg?style=flat-square)](./LICENSE)
![ZSH 5.0.0](https://img.shields.io/badge/zsh-v5.0.0-orange.svg?style=flat-square)

# Zplugin

Zplugin gives **reports** from plugin load. Plugins are no longer black boxes,
report will tell what aliases, functions, bindkeys, Zle widgets, zstyles,
completions, variables, `PATH` and `FPATH` elements a plugin has set up.

Supported is **unloading** of plugin and ability to list, (un)install and
selectively disable, enable plugin's completions. Also, every plugin is
compiled and user can control this function.

The system does not use `$FPATH`, loading multiple plugins doesn't clutter
`$FPATH` with the same number of entries (e.g. `10`). Code is immune to
`KSH_ARRAYS`. Completion management functionality is provided to allow user
to call `compinit` only once in `.zshrc`.

**NEW**: **[Code documentation](zsdoc)**

# Quick Start

To install, execute:

```SystemVerilog
sh -c "$(curl -fsSL https://raw.githubusercontent.com/zdharma/zplugin/master/doc/install.sh)"
```

Then add to `~/.zshrc`, at bottom:

```SystemVerilog
zplugin load zdharma history-search-multi-word
zplugin load zdharma/zui

# Binary release in archive, from Github-releases page; after unpacking it provides command "fzf"
zplugin ice from"gh-r" as"command"; zplugin load "junegunn/fzf-bin"

zplugin light zsh-users/zsh-autosuggestions
zplugin light zsh-users/zsh-syntax-highlighting

# This one to be ran just once, in interactive session
zplugin creinstall %HOME/my_completions  # Handle completions without loading any plugin, see "clist" command
```

(No need to add:

```SystemVerilog
source "$HOME/.zplugin/bin/zplugin.zsh"
```

because the install script does this.)

`HSMW` – multi-word searching of history (bound to Ctrl-R), `zui` – textual UI library for Zshell.
The `ice` subcommand – modifiers for following single command. `notabug` – the site `notabug.org`

# News
* 06-11-2017
  - The subcommand `clist` now prints `3` completions per line (not `1`). This makes large amount
    of completions to look better. Argument can be given, e.g. `6`, to increase the grouping.
  - New Ice-mod `silent` that mutes `stderr` & `stdout` of a plugin or snippet.

* 04-11-2017
  - New subcommand `ls` which lists snippets-directory in a formatted and colorized manner. Example:

  ![zplugin-ls](https://raw.githubusercontent.com/zdharma/zplugin/images/zplg-ls.png)

* 29-10-2017
  - Subversion protocol (supported by Github) can be used to clone **subdirectories** when using
    snippets. This allows to load multi-file snippets. For example:

    ```SystemVerilog
    zstyle ':prezto:module:prompt' theme smiley
    zplugin ice svn; zplugin snippet PZT::modules/prompt
    ```

  - Snippets support `Prezto` modules (with dependencies), and can use **PZT::** URL-shorthand,
    like in the example above. One can load `Prezto` module as single file snippet, or use Subversion
    to download whole directory (see also description of [Ice Modifiers](#ice-modifiers)):

    ```SystemVerilog
    # Single file snippet, URL points to file
    zplg snippet PZT::modules/helper/init.zsh

    # Multi-file snippet, URL points to directory to clone with Subversion
    # The file to source (init.zsh) is automatically detected
    zplugin ice svn; zplugin snippet PZT::modules/prompt

    # An use of Subversion to load an OMZ plugin
    zplugin ice svn; zplugin snippet OMZ::plugins/git
    ```

  - Fixed a bug with `cURL` usage (snippets) for downloading, it will now be properly used

* 13-10-2017
  - Snippets can use "**OMZ::**" prefix to easily point to `Oh-My-Zsh` plugins and libraries, e.g.:

    ```SystemVerilog
    zplugin snippet OMZ::lib/git.zsh
    zplugin snippet OMZ::plugins/git/git.plugin.zsh
    ```

* 12-10-2017
  - The `cd` subcommand can now obtain URL and move session to **snippet** directory
  - The `times` subcommand now includes statistics on snippets. Also, entries
    are displayed in order of loading:

    ```SystemVerilog
    % zplg times
    Plugin loading times:
    0.010 sec - OMZ::lib/git.zsh
    0.001 sec - OMZ::plugins/git/git.plugin.zsh
    0.003 sec - zdharma/history-search-multi-word
    0.003 sec - rimraf/k
    0.003 sec - zsh-users/zsh-autosuggestions
    ```

* 24-09-2017
  - **[Code documentation](zsdoc)** for contributors and interested people.

* 13-06-2017
  - Plugins can now be absolute paths:

    ```SystemVerilog
    % zplg load %HOME/github/{directory}
    % zplg load /Users/sgniazdowski/github/{directory}
    % zplg load %/Users/sgniazdowski/github/{directory}
    ```

    Completions are not automatically installed, but user can run `zplg creinstall %HOME/github/{directory}`, etc.

* 23-05-2017
  - New `ice` modifier: `if`, to which you can provide a conditional expression:

    ```SystemVerilog
    % zplugin ice if"(( 0 ))"
    % zplugin snippet --command https://github.com/b4b4r07/httpstat/blob/master/httpstat.sh
    % zplugin ice if"(( 1 ))"
    % zplugin snippet --command https://github.com/b4b4r07/httpstat/blob/master/httpstat.sh
    Setting up snippet httpstat.sh
    Downloading httpstat.sh...
    ```

* 16-05-2017
  - A very slick feature: **adding ice to commands**. Ice is something added and something that
    melts. You add modifiers to single next command, and the format (using quotes) guarantees
    you will see syntax highlighting in editors:

    ```SystemVerilog
    % zplg ice from"notabug" atload"echo --Loaded--" atclone"echo --Cloned--"
    % zplg load zdharma/zui
    Downloading zdharma/zui...
    ...
    Checking connectivity... done.
    --Cloned--
    Compiling zui.plugin.zsh...
    --Loaded--
    % grep notabug ~/.zplugin/plugins/zdharma---zui/.git/config
        url = https://notabug.org/zdharma/zui
    ```

    One other `ice` is `proto` that changes network protocol. Use `proto"git"` with Github to be able to use private repositories.

  - Completion-management supports completions provided in subdirectory, like in `zsh-users/zsh-completions`
    plugin. With `ice` modifier `blockf` (block-fpath), you can manage completions in such plugins (plugin can be
    normally loaded and it will be blocked from updating `$fpath`):

    ```SystemVerilog
    % zplg ice blockf
    % zplg load zsh-users/zsh-completions
    ...
    Symlinking completion `_ack' to /Users/sgniazdowski/.zplugin/completions
    Forgetting completion `_ack'...

    Symlinking completion `_afew' to /Users/sgniazdowski/.zplugin/completions
    Forgetting completion `_afew'...
    ...
    Compiling zsh-completions.plugin.zsh...
    % echo $fpath
    /Users/sgniazdowski/.zplugin/completions /usr/local/share/zsh/site-functions
    % zplg cdisable vagrant
    Disabled vagrant completion belonging to zsh-users/zsh-completions
    Forgetting completion `_vagrant'...

    Initializing completion system (compinit)...
    ```

* 14-05-2017
  - The `all`-variants of commands (e.g. `update-all`) have been merged into normal variants (with `--all` switch)

* 13-05-2017
  - `100` `ms` gain in performance
  - List of new commits for each updated plugin
  - `lftp` as fallback transport support for snippets
  - Snippets are updated on `update --all` command

# Introduction

**Example use:**

```SystemVerilog
% zplugin load zdharma/history-search-multi-word
% zplugin light zsh-users/zsh-syntax-highlighting
```

Above commands show two ways of basic plugin loading. **load** causes reporting to be enabled –
you can track what plugin does, view the information with `zplugin report {plugin-spec}`.
**light** is a faster loading without tracking and reporting.

### Oh-My-Zsh, Prezto

To load Oh-My-Zsh and Prezto plugins, use `snippet` feature. Snippets are single files downloaded
by `curl`, `wget`, etc. directly from URL. For example:

```SystemVerilog
% zplugin snippet 'https://github.com/robbyrussell/oh-my-zsh/raw/master/plugins/git/git.plugin.zsh'
% zplugin snippet 'https://github.com/sorin-ionescu/prezto/blob/master/modules/helper/init.zsh'
```

Also, you can use `OMZ::` and `PZT::` shorthands:

```SystemVerilog
% zplugin snippet OMZ::plugins/git/git.plugin.zsh
% zplugin snippet PZT::modules/helper/init.zsh
```

Moreover, snippets support `Subversion` protocol, supported also by Github. This allows to load
snippets that are multi-file (for example a Prezto module can have file `init.zsh` and file `alias.zsh`).
Default files that will be sourced are: `*.plugin.zsh`, `init.zsh`, `*.zsh-theme`:

```SystemVerilog
# URL points to directory
% zplugin ice svn; zplugin snippet PZT::modules/docker
```

### Snippets and performance

Using `curl`, `wget`, etc., `Subversion` allows to almost completely avoid code dedicated to Oh-My-Zsh and
Prezto, and also to other frameworks. This gives profits in performance of `Zplugin`, it is really fast
and also compact (low memory usage, short loading time).

### Some Ice-modifiers

The command `zplugin ice` provides Ice-modifiers for single next command (see subsection [below](#ice-modifiers)).
The logic is that "ice" is something that melts (so it doesn't last long) and something that's added. Using other
Ice-modifier "**pick**" user can explicitly select the file to source:

```SystemVerilog
% zplugin ice svn pick"init.zsh"; zplugin snippet PZT::modules/git
```

Content of Ice-modifier is simply put into `"..."`, `'...'`, or `$'...'`. No need for `":"` after
Ice-mod name. This way editors like `vim` and `emacs` will highlight contents of Ice-modifiers.

### as"command"

A plugin might not be a file for sourcing, but a command to be added to `$PATH`. To obtain this
effect, use Ice-modifier `as` with value `command`.

```SystemVerilog
% zplugin ice as"command" cp"httpstat.sh -> httpstat" pick"httpstat"
% zplugin light b4b4r07/httpstat
```

Above command will add plugin directory to `$PATH`, copy file `httpstat.sh` into `httpstat` and add
execution rights (`+x`) to the file selected with `pick`, i.e. to `httpstat`. Other Ice-mod exists,
`mv`, which works like `cp` but **moves** a file (it is ran before `cp`).

### atpull"..."

Copying file is safe for doing later updates – original files of repository are unmodified and
`Git` will report no conflicts. However, `mv` also can be used, if a proper `atpull` (an Ice–modifier
ran at **update** of plugin) will be used:

```SystemVerilog
% zplugin ice as"command" mv"httpstat.sh -> httpstat" pick"httpstat" atpull'!git reset --hard'
% zplugin light b4b4r07/httpstat
```

If `atpull` starts with exclamation mark, then it will be run before `git pull`, and before `mv`.
Nevertheless, `atpull`, `mv`, `cp` are ran **only if new commits are to be fetched**. So in summary,
when user runs `zplugin update b4b4r07/httpstat` to update this plugin, and there are new commits,
what happens first is that `git reset --hard` is ran – and it restores original `httpstat.sh`,
**then** `git pull` is ran and it downloads new commits (doing fast-forward), **then** `mv` is
ran again so that the command is `httpstat` not `httpstat.sh`.

For exclamation mark to not be expanded by Zsh in interactive session, use `'...'` not `"..."` to
enclose contents of `atpull` Ice-mod.

### Snippets-commands

Commands can also be added to `$PATH` using **snippets**. For example:

```SystemVerilog
% zplugin ice cp"httpstat.sh -> httpstat" pick"httpstat" as"command"
% zplugin snippet https://github.com/b4b4r07/httpstat/blob/master/httpstat.sh
```

Support for `atpull` in snippets is coming soon.

### Completion Management

Zplugin allows to disable and enable each completion in every plugin. Try installing a
popular plugin that provides completions:

```SystemVerilog
% zplugin ice blockf
% zplugin light zsh-users/zsh-completions
```

First command will block the traditional method of adding completions. Zplugin uses own
method (based on simlinks instead of adding to `$fpath`). Zplugin will automatically *install*
completions of newly downloaded plugin. To uninstall, and install again, use

```SystemVerilog
% zplg cuninstall zsh-users/zsh-completions   # uninstall
% zplg creinstall zsh-users/zsh-completions   # install
```

#### Listing completions

(Note: `zplg` is an alias that can be used in interactive sessions). To see what completions
*all* plugins provide, in tabular formatting and with name of each plugin, use:

```SystemVerilog
% zplg clist
```

This command is specially adapted for plugins like `zsh-users/zsh-completions`, which provide
many completions – listing will have `3` completions per line, so that not many terminal pages
will be occupied, like this:

```SystemVerilog
...
atach, bitcoin-cli, bower    zsh-users/zsh-completions
bundle, caffeinate, cap      zsh-users/zsh-completions
cask, cf, chattr             zsh-users/zsh-completions
...
```

You can show more completions per line by providing an *argument* to `clist`, e.g. `zplg clist 6`,
will show:

```SystemVerilog
...
bundle, caffeinate, cap, cask, cf, chattr      zsh-users/zsh-completions
cheat, choc, cmake, coffee, column, composer   zsh-users/zsh-completions
console, dad, debuild, dget, dhcpcd, diana     zsh-users/zsh-completions
...
```

#### Enabling and disabling completions

Completions can be disabled, so that e.g. original Zsh completion will be used.
The commands are very basic, they only need completion *name*:

```
% zplg cdisable cmake
Disabled cmake completion belonging to zsh-users/zsh-completions
% zplg cenable cmake
Enabled cmake completion belonging to zsh-users/zsh-completions
```

That's all on completions. There's one more command, `zplugin csearch`, that will
*search* all plugin directories for available completions, and show if they are
installed. This sums up to complete control over completions.

# Ice Modifiers

Following `ice` modifiers are to be passed to `zplugin ice ...` to obtain described effects.

|  Modifier | Description |
|-----------|-------------|
| `proto`   | Change protocol to `git`,`ftp`,`ftps`,`ssh`, `rsync`, etc. Default is `https`. Works with plugins (i.e. not snippets). |
| `from`    | Clone plugin from given site. Supported are `from"github"` (default), `..."github-rel"`, `..."gitlab"`, `..."bitbucket"`, `..."notabug"` (short names: `gh`, `gh-r`, `gl`, `bb`, `nb`). Can also be a full domain name (e.g. for Github enterprise). |
| `ver`     | Used with `from"gh-r"` (i.e. downloading a binary release, e.g. for use with `as"command"`) – selects which version to download. Default is latest, can also be explicitly `ver"latest"`. |
| `pick`    | Select the file to source, or the file to set as command (when using `snippet --command` or ICE `as"command"`), e.g. `zplugin ice pick"*.plugin.zsh"`. Works with plugins and snippets. |
| `depth`   | Pass `--depth` to `git`, i.e. limit how much of history to download. Works with plugins. |
| `if`      | Load plugin or snippet only when given condition is fulfilled, for example: `zplugin ice if'[[ -n "$commands[otool]" ]]'; zplugin load ...`. |
| `blockf`  | Disallow plugin to modify `fpath`. Useful when a plugin wants to provide completions in traditional way. Zplugin can manage completions and plugin can be blocked from exposing them. |
| `silent`  | Mute plugin's or snippet's `stderr` & `stdout`. |
| `mv`      | Move file after cloning or after update (then, only if new commits were downloaded). Example: `mv "fzf-* -> fzf`. It uses `->` as separator for old and new file names. Works also with snippets. |
| `cp`      | Copy file after cloning or after update (then, only if new commits were downloaded). Example: `cp "docker-c* -> dcompose"`. Ran after `mv`. Works also with snippets. |
| `atclone` | Run command after cloning, within plugin's directory, e.g. `zplugin ice atclone"echo Cloned"`. Ran also after downloading snippet. |
| `atload`  | Run command after loading, within plugin's directory. Can be also used with snippets. |
| `atpull`  | Run command after updating (**only if new commits are waiting for download**), within plugin's directory. If starts with "!" then command will be ran before `mv` & `cp` ices and before `git pull`. Otherwise it is ran after them. |
| `svn`     | Use Subversion for downloading snippet. Github supports `SVN` protocol, this allows to clone subdirectories as snippets, e.g. `zplugin ice svn; zplugin snippet OMZ::plugins/git`. Other ice `pick` can be used to select file to source (default are: `*.plugin.zsh`, `init.zsh`, `*.zsh-theme`). |

# Installation

Execute:

```sh
sh -c "$(curl -fsSL https://raw.githubusercontent.com/zdharma/zplugin/master/doc/install.sh)"
```

To update run the command again (or just execute `doc/install.sh`) or run `zplugin self-update`.

`Zplugin` will be installed into `~/.zplugin/bin`. `.zshrc` will be updated with
three lines of code that will be added to the bottom (the lines will be sourcing
`zplugin.zsh` and setting up completion).

Completion will be available, for command **zplugin** and aliases **zpl**, **zplg**.

After installing and reloading shell give `Zplugin` a quick try with `zplugin help`.

## Manual Installation

To manually install `Zplugin` clone the repo to e.g. `~/.zplugin/bin`:

```sh
mkdir ~/.zplugin
git clone https://github.com/zdharma/zplugin.git ~/.zplugin/bin
```

and source it from `.zshrc` (**above compinit**):

```sh
source ~/.zplugin/bin/zplugin.zsh
```

If you place the `source` below `compinit`, then add those two lines after the `source`:
```sh
autoload -Uz _zplugin
(( ${+_comps} )) && _comps[zplugin]=_zplugin
```

Various paths can be customized, see section below [Customizing Paths](#customizing-paths).

After installing and reloading shell give `Zplugin` a quick try with `zplugin help`.

# Compilation
It's good to compile `zplugin` into `Zsh` bytecode:

```sh
zcompile ~/.zplugin/bin/zplugin.zsh
```

**NEW:** `zplugin self-update` now also performs full zplugin compilation on each run.

Zplugin will compile each newly downloaded plugin. You can clear compilation of
a plugin by invoking `zplugin uncompile {plugin-spec}`. There are also commands
`compile`, `compiled` that control the functionality of compiling plugins.

# Usage

```
% zpl help
Usage:
-h|--help|help           - usage information
man                      - manual
self-update              - updates Zplugin
zstatus                  - overall Zplugin status
times                    - statistics on plugin loading times
load {plugin-name}       - load plugin, can also receive absolute local path
light {plugin-name}      - light plugin load, without reporting (significantly faster)
unload {plugin-name}     - unload plugin (needs reporting)
snippet [-f] [--command] {url} - source (or add to PATH with --command) local or remote file (-f: force - don't use cache)
ice <ice specification>  - add ICE to next command, argument is e.g. from\"gitlab\"
update {plugin-name}     - Git update plugin (or all plugins and snippets if --all passed)
status {plugin-name}     - Git status for plugin (or all plugins if --all passed)
report {plugin-name}     - show plugin's report (or all plugins' if --all passed)
loaded|list [keyword]    - show what plugins are loaded (filter with `keyword')
ls                       - list snippets in formatted and colorized manner
cd {plugin-name}         - cd into plugin's directory (does completion on TAB)
create {plugin-name}     - create plugin (also together with Github repository)
edit {plugin-name}       - edit plugin's file with $EDITOR
glance {plugin-name}     - look at plugin's source (pygmentize, highlight, GNU source-highlight)
stress {plugin-name}     - test plugin for compatibility with set of options
changes {plugin-name}    - view plugin's git log
recently [time-spec]     - show plugins that changed recently, argument is e.g. 1 month 2 days
clist|completions        - list completions in use
cdisable {cname}         - disable completion `cname'
cenable  {cname}         - enable completion `cname'
creinstall {plugin-name} - install completions for plugin; can also receive absolute local path
cuninstall {plugin-name} - uninstall completions for plugin
csearch                  - search all for available completions from any plugin, even unused ones
compinit                 - reload installed completions
dtrace|dstart            - start tracking what's going on in session
dstop                    - stop tracking what's going on in session
dunload                  - revert changes recorded between dstart and dstop
dreport                  - report what was going on in session
dclear                   - clear report of what was going on in session
compile  {plugin-name}   - compile plugin (or all plugins if --all passed)
uncompile {plugin-name}  - remove compiled version of plugin (or of all plugins if --all passed)
compiled                 - list plugins that are compiled
```

### Using Oh-My-Zsh Themes

To use **themes** created for `Oh-My-Zsh` you might want to first source the `git` library there:

```SystemVerilog
zplugin snippet 'http://github.com/robbyrussell/oh-my-zsh/raw/master/lib/git.zsh'
# Or using OMZ:: shorthand:
zplugin snippet OMZ::lib/git.zsh
```

Then you can use the themes as snippets (`zplugin snippet {file path or Github URL}`).
Some themes require not only
`Oh-My-Zsh's` `git` **library**, but also `git` **plugin** (error about function `current_branch` appears, or a similar one).
Load this plugin as single-file snippet:

```SystemVerilog
zplugin snippet OMZ::plugins/git/git.plugin.zsh
```

Such lines should be added to `.zshrc`. Snippets are cached locally, use `-f` option to download
a fresh version of a snippet, or `zplugin update --all` to refresh all snippets.

Most themes require `promptsubst` option (`setopt promptsubst` in `zshrc`), if it isn't set, then
prompt will appear as something like: `... $(build_prompt) ...`.

You might want to supress completions provided by the git plugin by issuing `zplugin cdclear -q`
(`-q` is for quiet) – see below **Ignoring Compdefs**.

To summarize:

```SystemVerilog
# Load OMZ Git library
zplugin snippet OMZ::lib/git.zsh

# Load Git plugin from OMZ
zplugin snippet OMZ::plugins/git/git.plugin.zsh
zplugin cdclear -q # <- forget completions provided up to this moment

setopt promptsubst

# Load theme from OMZ
zplugin snippet OMZ::themes/dstufft.zsh-theme

# Load normal Github plugin with theme depending on OMZ Git library
zplugin light NicoSantangelo/Alpharized
```

# Calling compinit

Compinit can be called after loading of all plugins and before possibly calling `cdreply`.
`Zplugin` takes control over completions, symlinks them to `~/.zplugin/completions` and adds
this directory to `$FPATH`. You manage those completions via commands starting with `c`:
`csearch`, `clist`, `creinstall`, `cuninstall`, `cenable`, `cdisable`.
All this brings order to `$FPATH`, there is only one directory there. 
Also, plugins aren't allowed to simply run `compdefs`. You can decide whether to run `compdefs`
by issuing `zplugin cdreplay` (reads: `compdef`-replay). To summarize:

```sh
source ~/.zplugin/bin/zplugin.zsh

zplugin load "some/plugin"
...
zplugin load "other/plugin"

autoload -Uz compinit
compinit

zplugin cdreplay -q # -q is for quiet
```

This allows to call compinit once.
Performance gains are huge, example shell startup time with double `compinit`: **0.980** sec, with
`cdreplay` and single `compinit`: **0.156** sec.

# Ignoring Compdefs

If you want to ignore compdefs provided by some plugins or snippets, place their load commands
before commands loading other plugins or snippets, and issue `zplugin cdclear`:

```SystemVerilog
source ~/.zplugin/bin/zplugin.zsh
zplugin snippet OMZ::plugins/git/git.plugin.zsh
zplugin cdclear -q # <- forget completions provided by Git plugin

zplugin load "some/plugin"
...
zplugin load "other/plugin"

autoload -Uz compinit
compinit
zplugin cdreplay -q # <- execute compdefs provided by rest of plugins
zplugin cdlist # look at gathered compdefs
```

# Non-Github (Local) Plugins

Use `create` command with user name `_local` (the default) to create plugin's
skeleton in `$ZPLGM[PLUGINS_DIR]`. It will be not connected with Github repository (because of user name
being `_local`). To enter the plugin's directory use `cd` command with just
plugin's name (without `_local`, it's optional).

The special user name `_local` is optional also for other commands, e.g. for
`load` (i.e. `zplugin load myplugin` is sufficient, there's no need for
`zplugin load _local/myplugin`).

# Customizing Paths

Following variables can be set to custom values, before sourcing Zplugin. The
previous global variables like `$ZPLG_HOME` have been removed to not pollute
the namespace – there's single `$ZPLGM` hash instead of `5` string variables.
Please update your dotfiles.

```
local -A ZPLGM # (initial Zplugin's hash definition)
ZPLGM[BIN_DIR] – where Zplugin code resides, e.g.: "/home/user/.zplugin/bin"
ZPLGM[HOME_DIR] – where Zplugin should create all working directories, e.g.: "/home/user/.zplugin"
ZPLGM[PLUGINS_DIR] – override single working directory – for plugins, e.g. "/opt/zsh/zplugin/plugins"
ZPLGM[COMPLETIONS_DIR] – as above, for completion files, e.g. "/opt/zsh/zplugin/root_completions"
ZPLGM[SNIPPETS_DIR] – as above, for snippets
```

# IRC Channel
Simply connect to [chat.freenode.net:6697](ircs://chat.freenode.net:6697/%23zplugin) (SSL) or [chat.freenode.net:6667](irc://chat.freenode.net:6667/%23zplugin) and join #zplugin.

Following is a quick access via Webchat [![IRC](https://kiwiirc.com/buttons/chat.freenode.net/zplugin.png)](https://kiwiirc.com/client/chat.freenode.net:+6697/#zplugin)
