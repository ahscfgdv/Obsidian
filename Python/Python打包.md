
## nuitka

```powershell

nuitka .\test_gui.py `
--standalone `
--output-dir=dist2 `
--enable-plugin=pyqt5 `
--mingw64 `
--include-package=rasterio `
--include-package-data=rasterio `
--follow-import-to=rasterio `
--windows-disable-console `
--noinclude-unittest `
--noinclude-setuptools

```


```powershell

(base) PS D:\Project\RSAI_GUI> nuitka .\test_gui.py --standalone
Nuitka-Options: Used command line options:
Nuitka-Options:   .\test_gui.py --standalone
Nuitka: Starting Python compilation with:
Nuitka:   Version '2.8.9' on Python 3.12 (flavor 'Anaconda Python') commercial grade 'not installed'.
Nuitka: Completed Python level compilation and optimization.
Nuitka: Generating source code for C backend compiler.
Nuitka: Running data composer tool for optimal constant value handling.
Nuitka: Running C compilation via Scons.

scons: *** No tool module 'ilink' found in D:\Softwares\MiniConda\Lib\site-packages\nuitka\build\inline_copy\lib\scons-4.10.1\SCons\Tool
File "D:\Softwares\MiniConda\Lib\site-packages\nuitka\build\SconsUtils.py", line 233, in createEnvironment
FATAL: Failed unexpectedly in Scons C backend compilation.
Nuitka:WARNING:     Complex topic! More information can be found at
Nuitka:WARNING: https://nuitka.net/info/scons-backend-failure.html
Nuitka-Reports: Compilation crash report written to file 'nuitka-crash-report.xml'.


解决办法使用--mingw64参数

```