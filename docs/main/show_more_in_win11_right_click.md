# 设置

1. 首先用鼠标右键点击“开始”按钮（或者按 Win+X 键），选择点击 "Windows 终端（管理员）"
2. 在终端应用程序里粘贴

```powershell
reg.exe add "HKCU\Software\Classes\CLSID\{86ca1aa0-34aa-4e8b-a509-50c905bae2a2}\InprocServer32" /f /ve
```

3. 操作成功之后，重启 Win11

# 恢复

1. 打开 Windows 终端（管理员），输入

```powershell
reg.exe delete "HKCU\Software\Classes\CLSID\{86ca1aa0-34aa-4e8b-a509-50c905bae2a2}\InprocServer32" /va /f
```

2、然后显示操作成功，重启即可恢复
