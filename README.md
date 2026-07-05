# Python-to-C
# py2c — Python → C 转换器

将 Python 代码转换为 C 语言的源到源编译器。支持普通程序转换和裸机内核开发。

---

## 快速使用

```
dist/
├── py2c-gui.exe    ← 双击运行（图形界面）
└── runtime/        ← 编译所需运行时库
```

**双击 `py2c-gui.exe`**，选择 `.py` 文件，点「转换并编译」即可。

## 功能

### 图形界面

| 功能 | 说明 |
|------|------|
| 📂 选择文件夹 | 扫描文件夹内所有 `.py` 文件 |
| 📄 选择单个文件 | 选择单个文件转换 |
| ⚙ 仅转换 | 生成 `.c` 文件 |
| ▶ 转换并编译 | 生成 `.c` + 编译为 `.exe` |
| 错误显示 | 编译失败时红字显示详细错误 |

### 支持转换的 Python 语法

```
变量/赋值/类型注解     ✅   类/OOP/方法调用       ✅
算术/比较/布尔运算     ✅   列表/字典/集合         ✅
if/while/for           ✅   推导式                 ✅
函数 def/return        ✅   元组解包               ✅
异常 try/except        ✅   with 语句              ✅
f-string/字符串操作     ✅   内建函数 30+           ✅
```

### 内核开发（Freestanding 模式）

```bash
# 命令行的裸机模式（GUI 暂未集成）
py2c kernel.py --freestanding -o kernel.c
```

```
裸机函数             ✅   MMIO 指针 ptr()        ✅
@interrupt 中断处理   ✅   @naked 无栈帧函数      ✅
@section 段指定      ✅   内联汇编 asm()          ✅
asm 约束操作数       ✅   alignof/offsetof       ✅
--ld-script 链接脚本  ✅   --target 多架构        ✅
```

### 不支持转换

```
async/await     需要协程运行时，无法静态编译
eval/exec       动态代码执行无法在编译时确定
元类            需要运行时 type() 调用
@property       需要描述符协议运行时实现
super()        需要 C3 MRO 算法
类型联合 int|str 需要运行时类型检查
```

## 项目结构

```
py2c/                  # 转译器核心（8 个 Python 模块）
├── __main__.py        # CLI 入口
├── transpiler.py      # 主协调器
├── parser.py          # AST 解析
├── type_infer.py      # 类型推断
├── symbol_table.py    # 符号表
├── c_decl.py          # C 声明生成
├── c_expr.py          # C 表达式生成
├── c_stmt.py          # C 语句生成
├── builtins.py        # 内建函数映射
└── runtime/           # C 运行时库（runtime.h + runtime.c）
examples/              # Python 示例
dist/                  # 编译好的 exe
```

## 从源码运行

```bash
pip install -e .
py2c input.py -o output.c
# 或
python -m py2c input.py -o output.c
```

## 命令行参数

```
py2c input.py -o output.c             转译
py2c input.py --print-only            只打印 C 代码
py2c input.py --freestanding         裸机模式
py2c input.py --kernel               内核项目（含链接脚本）
py2c input.py --target riscv64       指定架构
```

## 构建 exe

```bash
pip install pyinstaller
python -m PyInstaller --onefile --windowed --name py2c-gui --add-data "py2c/runtime;runtime" py2c_gui.py
```
