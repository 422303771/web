<!--toc-->

- [mac os 使用日常](#mac-os-使用日常)
	- [显示与隐藏文件](#显示与隐藏文件)
	- [mac os 与 win 按键区别](#mac-os-与-win-按键区别)
		- [输入表情](#输入表情)

<!-- tocstop -->

----

# mac os 使用日常

## 显示与隐藏文件

显示隐藏文件

```
defaults write com.apple.finder AppleShowAllFiles -boolean true ; killall Finder
```

隐藏文件

```
defaults write com.apple.finder AppleShowAllFiles -boolean false ; killall Finder
```

## mac os 与 win 按键区别

默认情况下:

| win 按键 | mac 按键 |
|:---------:|:---------:|
|<kbd>win</kbd>|<kbd>⌘</kbd>|
|<kbd>Ctrl</kbd>|<kbd>^</kbd>|
|<kbd>Alt</kbd>|<kbd>⌥</kbd>|
|<kbd>Shift</kbd>|<kbd>⇧</kbd>|
|<kbd>Caps Lock</kbd>|<kbd>⇪</kbd>|


修改后

| win 按键 | mac 按键 |
|:---------:|:---------:|
|<kbd>win</kbd>|<kbd>⌥</kbd>|
|<kbd>Ctrl</kbd>|<kbd>⌘</kbd>|
|<kbd>Alt</kbd>|<kbd>^</kbd>|
|<kbd>Shift</kbd>|<kbd>⇧</kbd>|


### 输入表情

<kbd>Ctrl</kbd>+<kbd>Alt</kbd>+<kbd>Space</kbd>
