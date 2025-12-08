#### windows内存过大：
https://blog.csdn.net/qq_39463175/article/details/123827841
1. 在传统界面按Win键+ R键，在搜索框中输入msconfig，按回车键。
2. 点击”服务”选项，选择”隐藏所有的微软服务”，然后点击全部禁用。(如果可选)
3. 点击”启动”选项,，点击”打开任务管理器”，然后禁用全部启动项并确定。
3. 选择常规，选择正常启动或选择性启动


#### system clipboard is unavailable
按下Win+R，运行  cmd.exe /c "echo off | clip"   来清空剪切板，再重试。
或者直接重启 rdpclip.exe
