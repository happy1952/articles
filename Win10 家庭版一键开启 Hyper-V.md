# Win10 家庭版一键开启 Hyper-V

粘贴如下命令到文本编辑器，另存为 `hyper-v.bat` 后，右击以管理员身份运行即可开启 Windows10 家庭版的 Hyper-V。

```bat
pushd "%~dp0"

dir /b %SystemRoot%\servicing\Packages\*Hyper-V*.mum >hyper-v.txt

for /f %%i in ('findstr /i . hyper-v.txt 2^>nul') do dism /online /norestart /add-package:"%SystemRoot%\servicing\Packages\%%i"

del hyper-v.txt

Dism /online /enable-feature /featurename:Microsoft-Hyper-V-All /LimitAccess /ALL
```