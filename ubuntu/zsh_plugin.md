<!--toc-->

- [zsh plugin](#zsh-plugin)
	- [zsh tmux](#zsh-tmux)
	- [zsh git](#zsh-git)
	- [zsh laravel](#zsh-laravel)
	- [zsh ubuntu](#zsh-ubuntu)
	- [zsh npm](#zsh-npm)
	- [zsh ruby](#zsh-ruby)
	- [zsh bundler](#zsh-bundler)

<!-- tocstop -->

----

# zsh plugin

## zsh tmux

缩写   | 命令
:--- | :-------------------
ta   | tmux attach -t
ts   | tmux new-session -s
tl   | tmux list-sessions
tksv | tmux kill-server
tkss | tmux kill-session -t

## zsh git

缩写    | 命令
:---- | :----------------------------------------------
g     | git
ga    | git add
gaa   | git add --add
gapa  | git add --patsh
      |
gb    | git branch
gba   | git branch -a
gbnm  | git branch --no-merged
gbr   | git branch --remote
      |
gc    | git commit -v                                   | 可以看到差异
gca   | git commit -v -a                                | 一般提交
gcam  | git commit -a -m
gcmsg | git commit -m
      |
gco   | git checkout
gcm   | git checkout master
gcb   | git checkout -b
      |
gcl   | git clone --recursive
gcf   | git config --list
      |
gd    | git diff
gf    | git fetch
      |
gl    | git pull
gm    | git merge
gmom  | git merge origin/master
gp    | git push
gpd   | git push --dry-run
gpoat | git push origin --all && git push origin --tags
      |
gr    | git remote
gra   | git remote add
grmv  | git remote rename
grrm  | git remote remove
grv   | git remote -v
      |
grb   | git rebase                                      | 变基
grbm  | git rebase master
      |
grh   | git reset HEAD
grhh  | git reset HEAD --hard
      |
gsb   | git status -sb
gss   | git status -s
gst   | git status
      |
gts   | git tag -s
gtv   | git tag                                         | sort -V

## zsh laravel

缩写        | 命令
:-------- | :-------------------------
la5       | php artisan
la5cache  | php artisan cache:clear
la5routes | php artisan route:list
la5vendor | php artisan vendor:publish

## zsh ubuntu

缩写   | 命令
:--- | :-----------------------------------------------
ag   | sudo apt-get
aga  | sudo apt-get autoclean
agar | sudo apt-get autoremove
agb  | sudo apt-get build-dep
agc  | sudo apt-get clean
agd  | sudo apt-get dselect-upgrade
agi  | sudo apt-get install
agp  | sudo apt-get purge
agr  | sudo apt-get remove
agu  | sudo apt-get update
agud | sudo apt-get update && sudo apt-get dist-upgrade
agug | sudo apt-get upgrade
aguu | sudo apt-get update && sudo apt-get upgrade

## zsh npm

缩写   | 命令
:--- | :-----------
npmg | npm i -g
npmS | npm i -S
npmD | npm i -D
npmO | npm outdated

## zsh ruby

缩写   | 命令
:--- | :-------
sgem | sudo gem

## zsh bundler

缩写   | 命令
:--- | :--------------
be   | bundle exec
bl   | bundle list
bp   | bundle package
bo   | bundle open
bout | bundle outdated
bu   | bundle update
bi   | bundle install
bcn  | bundle clean
