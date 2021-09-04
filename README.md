# fzf-mksh
*fzf's key-bindings and tab completion for mksh*

## What it is

[`fzf`](https://github.com/junegunn/fzf) provides `CTRL-T` (file selection), `ALT-C` (change to directory), `CTRL-R` (history search), and some bespoke tab completion support for bash and zsh (and partially for fish). This repo ports the three key-bindings to pure mksh. It also provides tab completion, but instead of doing a bespoke form of it meant to work alongside the builtin completion, this repo provides the ability to select candidates from mksh's built-in tab completion capabilities, instead of displaying them in a columnized form as mksh normally does.

## Installation

You'll need mksh R53a or later for key-bindings support (requires the `evaluate-region` editing command), and a current development snapshot for tab completion (requires a functional `quote-region` editing command).

Any reasonably modern version of `fzf` will do.

1. Clone this repo wherever you like, and optionally copy or symlink the `completion.mksh` and `key-bindings.mksh` files to any location of your choice. (I have mine symlinked at `~/.fzf/shell/`, which is where the analogous bash, zsh, and fish files were placed when I first installed `fzf` through the provided directions.)
2. Source the files in your current shell, or add them to your `~/.mkshrc` (what you write is the same either way). The first provides the three key-bindings, and the second provides tab completion. You can pick just one or the other if you like:
    ```sh
    . /path/to/key-bindings.mksh
    . /path/to/completion.mksh
    ```
3. Set any of the fzf-specific environment variables that you'd like (see the `fzf` README [here](https://github.com/junegunn/fzf#environment-variables) and [here](https://github.com/junegunn/fzf#key-bindings-for-command-line)). The following are respected for now:
    ```sh
    $FZF_DEFAULT_OPTS
    $FZF_TMUX
    $FZF_TMUX_OPTS
    $FZF_TMUX_HEIGHT
    $FZF_CTRL_T_COMMAND
    $FZF_CTRL_T_OPTS
    $FZF_ALT_C_COMMAND
    $FZF_ALT_C_OPTS
    $FZF_CTRL_R_OPTS
    ```
4. In the shell where these files are sourced, press `CTRL-T`, `ALT-C`, `CTRL-R`, or `<TAB>` to your heart's content! All four key-bindings are already made for you in the relevant files.

4. Optionally, install `fzf-tmux.mksh` to replace the otherwise-used `fzf-tmux` script written in bash. In any case, you're good to go as long as it's on your `$PATH` and named `fzf-tmux`. You can do this either linking, moving, or copying `fzf-tmux.mksh` to you a directory on your `$PATH` that has higher precedence than the existing executable:
    ```sh
    ln -s /path/to/fzf-tmux.mksh /path/to/higher-precedence/directory/on/PATH/fzf-tmux
    ```
    or by replacing the `fzf-tmux` executable you were previously using:
    ```sh
    mv /path/to/fzf-bindir/fzf-tmux /path/to/fzf-bindir/fzf-tmux.bash
    ln -s /path/to/fzf-tmux.mksh /path/to/fzf-bindir/fzf-tmux
    ```

## Details

### Design choices
The initial target of this project is to a) provide a pure-mksh experience for mksh+fzf users, b) provide the three key-bindings that the main repo provides for bash, and c) for any features that interact with built-in mksh capabilities, to replicate mksh's behavior as much as possible while simply providing the completion candidates via `fzf` instead of via the built-in interface.

Porting `fzf-tmux` to mksh done, but hasn't been tested much, though it should work identically to the bash version provided by `fzf`.

The three key-bindings are mostly feature-complete, although there are some small additions and optimizations to be made (see below).

Completion should be working, but hasn't been tested much, and has some known bugs or incomplete features. For anyone not familiar with mksh, it provides two forms of completion: command completion and file completion. Command completion will provide executables in your `$PATH`, as well as some extras like builtins and a few other things: basically, anything that's valid at the start of a line. File completion will provide any type of file (directories, sockets, etc) that match the current word. Any unambiguous completion will automatically be entered, and at that point if there are mulitple candidates, a list will be displayed. This project, as said before, simply sends the candidates in that list to `fzf`. While mksh doesn't provide complex command completion e.g. for command arguments, it provides a deterministic, predictable, and comprehensible set of candidates. In the future, I plan to extend the completion capabilities of mksh through some of these mechanisms, but for now, work will focus on reaching feature parity and accuracy against built-in mksh completion.

### TODO
- [ ] fix vim edit mode keybindings (currently untested and haven't been looked at in years)
- [ ] tab completion is missing some candidates for command completion (i.e., things not found on your `$PATH`)
- [ ] tab completion breaks in some cases if the file/folder in the path you're completing has spaces in it
- [ ] tab completion isn't aware of `` ` ``, `$(`, or `./`, to trigger either command completion (first two cases) or file completion (last case) when it otherwise would trigger the other
- [ ] add tab completion for some of the special term lists that are present in `fzf`, like environment variables & aliases, ssh/telnet hostnames, PIDs, etc.
- [x] fix port of `fzf-tmux` to mksh (the `fzf-tmux.mksh` file in this repo has a race condition or some other error)
- [ ] more options for completion, to deviate in possibly preferable ways from mksh's built-in behavior
