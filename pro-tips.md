# Pro-tips

## One-off fanciness or a specific diff-so-fancy alias

You can do also do a one-off:

```shell
git diff --color | diff-so-fancy
```

or configure an alias and a corresponding pager to use `diff-so-fancy`:

```shell
git config --global alias.dsf "diff --color"
git config --global pager.dsf "diff-so-fancy | less --tabs=4 -RFXS"
```

## Opting-out

Sometimes you will want to bypass diff-so-fancy. Use `--no-pager` for that:

```shell
git --no-pager diff
```

## Raw patches

As a shortcut for a 'normal' diff to save as a patch for emailing or later
application, it may be helpful to configure an alias:

```ini
[alias]
    patch = !git --no-pager diff --no-color
```

which can then be used as `git patch > changes.patch`.

#### Moving around in the diff

You can pre-seed your `less` pager with a search pattern so that you can move
between files with `n`/`N` keys:

```ini
[pager]
    diff = diff-so-fancy | less --tabs=4 -RFXS --pattern '^(Date|added|deleted|modified): '
```

## Zsh plugin suppport for diff-so-fancy

This project includes a `.plugin.zsh` file providing ZSH framework support, so you can use any framework that supports the ZSH plugin standard to install `diff-so-fancy` and add it to your `$PATH`. Installation with Zinit, Zplug, and Zgen:

### Install with zinit

```sh
zinit ice lucid as"program" pick"bin/git-dsf"
zinit load so-fancy/diff-so-fancy
```

### Install with zplug

```sh
zplug "so-fancy/diff-so-fancy", as:command, use:bin/git-dsf
```

### zgenom and others

```sh
zgenom load so-fancy/diff-so-fancy
```

## Automatic fallback

You might automatically distribute your git config as part of your dotfiles to new machines, where you may not have access to `diff-so-fancy`.
In this case, rather than getting an error message every time you run `git diff` or `git add -p`, the following configuration may help:

To make `git diff` automatically fall back to just using `less`:

```shell
git config --global core.pager "command -v diff-so-fancy >/dev/null 2>&1 && diff-so-fancy | less --tabs=4 -RF || less"
```

Making the interactive filter automatically fall back is a bit more difficult. A helper script comes into play here, to check if `diff-so-fancy` is installed:

```zsh
#!/usr/bin/zsh
command -v diff-so-fancy >/dev/null 2>&1

if [[ $? -eq 0 ]] 
then
        cat | diff-so-fancy --patch
else
        cat
fi
```

Then, configure git (replace `dsf-filter` with whatever you named the helper script):

```shell
git config interactive.diffFilter "dsf-filter"
```

## `hg` configuration

You can configure `hg diff` output to use `diff-so-fancy` by adding this alias
to your `hgrc` file:

```ini
[alias]
    diff = !HGPLAIN=1 $HG diff --pager=on --config pager.pager=diff-so-fancy $@
```
