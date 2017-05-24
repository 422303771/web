## surface pro 3 启动项 清理

UEFI 适用
使用window 控制台
```
// 显示启动项

BCDEDIT /ENUM FIRMWARE

// 删除启动项

BCDEDIT /delete {XX-XX-XX}
```
随后重启有效
