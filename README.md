Code::Blocks 是轻量免费C/C++ IDE，自带MinGW(GCC)编译器，适合初学C语言。

> ✅ 下载必须选带 **mingw** 的安装包：`codeblocks-25.03mingw-setup.exe`，不带mingw版本没有编译器，写代码无法编译

# 一、安装 + 编译器自检

1. 安装，勾选全部组件，**确保MinGW被勾选**，记住安装路径（不要中文、不要空格）
2. 打开CodeBlocks → `Settings` → `Compiler`
3. 选中 `GNU GCC Compiler` → 切换 `Toolchain executables` 标签
4. 点 `Auto-detect` 自动识别MinGW路径；识别成功直接OK保存
   - 如果识别失败，手动选择MinGW根目录，一般是 `C:\Program Files\CodeBlocks\MinGW`
5. 调试器检查：`Settings` → `Debugger`，选择GDB，填入gdb.exe完整路径

## ✅ CodeBlocks 25.03+内置原生中文

> 25.03 及更高版本安装包内置简体中文语言文件，直接软件内勾选切换即可，无需下载额外汉化包

1. 打开 CodeBlocks → 顶部菜单 **Settings（设置） → Environment（环境）**
2. 左侧点 **View（视图）**
3. 勾选：`Internationalization (will take place after restart)`
4. 下拉菜单选择：`Chinese (Simplified)`
5. 点击 OK，**完全关闭 CodeBlocks，重启软件**，界面自动变成简体中文。

# 二、新建项目（推荐用项目，不要单独单文件）

1. 菜单：`File` → `New` → `Project`
2. 选择 **Console application**（控制台程序）→ Go
3. 向导：Next → 选择语言 **C**（C++就选C++）
4. Project title：项目名（英文，不能中文）；选择项目保存文件夹（**路径不能含中文**）
5. Compiler：`GNU GCC Compiler`，Debug、Release全部勾选 → 完成
6. 左侧管理窗口展开项目 → Sources，双击 `main.c` 打开代码

## HelloWorld 测试代码

```c
#include <stdio.h>
int main()
{
    printf("Hello CodeBlocks!\n");
    system("pause");
    return 0;
}
```

# 三、编译、运行、快捷键

| 功能             | 快捷键        | 按钮图标    |
| -------------- | ---------- | ------- |
| 构建（编译链接）       | `Ctrl+F9`  | 齿轮      |
| 运行             | `Ctrl+F10` | 蓝色三角    |
| **构建并运行（最常用）** | `F9`       | 齿轮+绿色三角 |
| 调试             | `F8`       | 虫子图标    |

> 下方窗口：**构建日志**，报错信息全部在这里，红色就是编译错误。

# 四、断点调试（新手重点）

1. 在代码行号**左侧空白处点击**，出现红色圆点 = 断点
2. F8 启动调试
   - F7：单步进入（进入函数内部）
   - Shift+F7：单步跳过（执行本行，不进函数）
   - Ctrl+F7：继续运行到下一个断点
   - F4：查看当前变量窗口

# 五、常用基础设置优化

1. 显示行号：`Settings → Editor`，勾选Show line numbers
2. 字体大小：Editor → Font，调整字体
3. 自动保存：Settings → Environment，开启自动保存
4. 隐藏多余窗口：View，勾选/取消管理窗口、日志窗口

# 六、常见报错排查清单

1.**Can't find compiler executable** 原因：没装MinGW / 编译器路径配置错误。重新进入编译器页面Auto-detect。

2.**窗口一闪而过** 

方案一：调试模式F8运行；
方案二：代码末尾加 `system("pause");`，需要头文件 `#include <stdlib.h>`

3.项目路径含中文/空格：**直接报错**，全部改成纯英文路径

4.语法报错：看下方构建日志，定位行号修改代码

# 七、控制台中文乱码解决（高频坑）

✅ **根本原因：源码里面有中文，编码不匹配，GCC识别到非法字节**

CodeBlocks + MinGW 经典编码问题：

- Windows控制台：**GBK**
- 代码文件保存：**UTF-8**
- GCC默认把源码当成GBK读取，读到UTF-8中文就报 `Illegal byte sequence`

## 方案1（源码UTF-8，编译器输出GBK，推荐，最稳）

> 👉推荐，跨平台、放 GitHub、VSCode 打开都正常，Windows 控制台正常显示中文**
> 
> 原理：Windows控制台默认GBK；源码文件本身存 UTF-8；编译器读 UTF8 源码，编译时把程序里字符串转成 GBK 给 Windows 控制台输出，通过参数转换输出编码。

1.设置编辑器保存编码 `Settings` → `Editor` → General settings → Encoding → ✅ 勾选 `Use UTF-8 encoding`（源码永久 UTF-8）

2.CodeBlocks菜单：`Settings` → `Compiler` → `Global compiler settings` → `Other compiler options` 粘贴两行参数：

```
-finput-charset=UTF-8
-fexec-charset=GBK
```

- `-finput-charset=UTF-8`：告诉编译器：**源码文件是UTF-8编码**

- `-fexec-charset=GBK`：告诉编译器：**输出字符集GBK适配Windows控制台**

> 编辑器ANSI，编译器可只写 `-fexec-charset=GBK`
> 
> 编辑器UTF8，编译器写 `-finput-charset=UTF-8 -fexec-charset=GBK

3.清理旧编译缓存（每次改完编码配置必做一步）
菜单 `Build` → `Clean`清理旧 obj 缓存，然后重新 `Build and run(F9编译)`

✅ 优点：

源码 UTF-8：上传 GitHub、VSCode、Linux 打开，中文不会乱
编译出来 exe 在 Windows 控制台中文正常显示
不用来回切换 ANSI/UTF8，不会出现编码冲突报错
❌ 局限：**编译出来的 exe 放到 Linux 终端运行，中文会乱码**（Windows 控制台本身是 GBK 体系，这是系统限制，不是 bug）

## 方案2（源码直接保存GBK，简单但跨平台差）

编辑器编码改成 `GBK`，编译器不需要加 `-finput-charset`。

> 缺点：放到Linux/VSCode打开中文会乱码。

## 额外排查

1. 文件路径没有中文
2. 不要直接复制网上代码（网页UTF8隐形特殊字符，也会触发Illegal byte sequence），建议手动输入中文
3. 如果复制粘贴过来的引号 `“ ”`（中文全角引号），也会报错！**必须用英文半角引号 " "**

## 避坑提醒

千万不要来回切换编辑器的 ANSI/UTF8！
一旦切换，旧文件编码就变了，很容易出现： `Illegal byte sequence` 非法字节报错，就是你刚才遇到的问题。

> 固定：**编辑器永久 UTF8 + 上面两行编译参数**，一劳永逸。

## exe 在 Windows 和 Linux 都正常显示中文

上面编译参数方式做不到，需要**代码层面兼容**，不依赖编译选项：
C 语言中，控制台输出中文，推荐两种代码写法，不需要依赖 `-fexec-charset`：

1. 方案①：用宽字符（`wprintf`），推荐 Windows

```c
#include <stdio.h>
#include <wchar.h>
#include <stdlib.h>
int main(void)
{
    wprintf(L"测试中文\n");
    system("pause");
    return 0;
}
```

2. 方案②：C11 原生 UTF8 字符串（`u8"中文"`），现代编译器支持，跨平台更好

```c
#include <stdio.h>
#include <stdlib.h>
int main(void)
{
    printf(u8"测试中文\n");
    system("pause");
    return 0;
}
```

> 注意：`u8` 字符串在 Windows 旧 cmd 仍然会乱码，Windows 终端需要开启 UTF-8 支持。

# 八、快速自检清单

- 引号是英文 `"`，不是中文 `“`
- 编译器选项添加 `-finput-charset=UTF-8 -fexec-charset=GBK`
- Editor编码设为UTF-8
- Build → Clean 清理旧obj再编译

# 九、CodeBlocks项目.gitignore模板（GitHub上传用）

```gitignore
# CodeBlocks
*.cbp
*.layout
*.depend
*.obj
*.o
*.exe
*.bin
*.pdb
*.elf
*.map
# Debug/Release目录
bin/
obj/
# 临时文件
*.bak
*.tmp
# 系统文件
Thumbs.db
.DS_Store
```

# 十、README.md 简易模板（CodeBlocks C项目）

# CodeBlocks C Demo

## 环境

IDE：CodeBlocks 20.03 + MinGW GCC
语言：C语言

## 编译运行

1. 使用CodeBlocks打开 .cbp 项目文件
2. F9 构建并运行
3. F8 启动调试

## 目录结构

- src/main.c : 主程序代码
- bin/ : 编译后exe
- obj/ : 目标文件

## 快捷键速查表

### 🔨 编译 & 运行 & 调试（最常用）

| 快捷键           | 功能说明                      |
| ------------- | ------------------------- |
| F9            | 构建并运行（编译+链接+执行，日常写代码首选）   |
| Ctrl+F9       | 仅构建（编译+链接，不运行）            |
| Ctrl+F10      | 只运行，不重新编译                 |
| F8            | 启动调试                      |
| F7            | 单步进入（step into，跳进自定义函数内部） |
| Shift+F7      | 单步跳过（step over，本行执行，不进函数） |
| Ctrl+F7       | 继续运行到下一个断点                |
| Alt+F7        | 跳出当前函数（step out）          |
| F4            | 查看当前选中变量                  |
| Ctrl+Shift+F8 | 删除所有断点                    |

### 📝 代码编辑

| 快捷键                         | 功能说明            |
| --------------------------- | --------------- |
| Ctrl + C / V / X            | 复制 / 粘贴 / 剪切    |
| Ctrl + Z / Ctrl + Shift + Z | 撤销 / 重做         |
| Ctrl + A                    | 全选              |
| Ctrl + F                    | 查找              |
| Ctrl + R                    | 查找并替换           |
| Ctrl + G                    | 跳转到指定行号         |
| Tab                         | 选中内容向右缩进        |
| Shift + Tab                 | 选中内容向左缩进        |
| Ctrl + /                    | 批量注释/取消注释（选中多行） |
| Ctrl + D                    | 复制当前行           |
| Ctrl + Y                    | 删除当前行           |

### 📂 项目与窗口操作

| 快捷键              | 功能说明           |
| ---------------- | -------------- |
| Ctrl + N         | 新建文件           |
| Ctrl + O         | 打开文件           |
| Ctrl + S         | 保存当前文件         |
| Ctrl + Shift + S | 全部保存           |
| Alt + F1         | 切换管理项目窗口       |
| F2               | 显示/隐藏日志、构建消息窗口 |

### ⚙️ 其他实用快捷键

| 快捷键                      | 功能说明          |
| ------------------------ | ------------- |
| Ctrl + PageUp / PageDown | 在打开的多个标签页之间切换 |
| Ctrl + Shift + C         | 打开代码颜色高亮设置    |

> 提示：
> 
> 快捷键无效时，切换为英文输入法；
> 
> 可在 `Settings → Environment → Keyboard shortcuts` 自定义快捷键。

## 常见问题

1. 控制台中文乱码
   Settings → Editor → General settings → Encoding 选择 UTF-8
   编译器设置添加：`-fexec-charset=GBK`

2. 程序运行窗口一闪而过
   在代码末尾添加 `system("pause");`，需要引入头文件 `#include <stdlib.h>`

3. 报错 Can't find compiler executable
   进入 Settings → Compiler，点击 Auto-detect 自动识别MinGW路径。

> ⚠️ 项目存放路径**不能包含中文、空格**，否则容易编译报错。

## 常见报错排查清单

| 报错信息                                                                                | 原因                         | 解决方案                                                                   |
| ----------------------------------------------------------------------------------- | -------------------------- | ---------------------------------------------------------------------- |
| Can't find compiler executable in your configured search paths for GNU GCC Compiler | 未识别MinGW编译器                | Settings → Compiler → Toolchain executables → Auto-detect；手动指定mingw根目录 |
| No such file or directory                                                           | 头文件不存在 / 文件名写错 / 路径有中文     | 检查头文件名拼写；项目路径改为纯英文无空格                                                  |
| expected ';' before 'return'                                                        | 语法错误，少分号、括号不匹配             | 看构建日志提示行号，检查上一行代码，补充分号/括号                                              |
| multiple definition of `main'                                                       | 多个文件写了main函数，一个项目只能有1个main | 删除多余main函数，或者新建独立项目                                                    |
| undefined reference to `xxx'                                                        | 调用函数但是没有实现，或者没有链接对应源码      | 检查函数名拼写；确认.c文件已经添加进项目                                                  |
| permission denied                                                                   | exe被占用，正在运行中               | 关闭正在运行的程序，重新F9编译                                                       |
| variable 'xxx' is uninitialized                                                     | 变量未初始化，警告，不阻止编译            | 定义变量时给初始值                                                              |
| cannot find -lgdi32 之类链接错误                                                          | 缺少链接库                      | 在编译器链接选项添加对应库                                                          |

## Git提交规范

- feat: 新增功能代码
- fix: 修复bug
- docs: 修改文档、README
- style: 代码格式化，无逻辑改动
- refactor: 代码重构
- test: 添加/修改测试代码
- chore: 构建、环境、gitignore等配置修改
