# cctrans

> Ref: https://github.com/roy-jiang-opus/cctrans

## Installation

```bash
npm install -g cctrans@latest
cctrans install
```

## Configuration

```bash
# 设置简体中文
cctrans lang zh-Hans

# 使用逐行翻译
cctrans mode line

# 同时显示英文和中文
cctrans display append

# 关闭只显示中文模式
cctrans only off

# 不翻译用户输入
cctrans input off

# 不翻译交互式选择框
cctrans dialog off

# 翻译引擎先选择 Google
cctrans backend google

# 开启翻译
cctrans on
```

```bash
# 显示当前配置
cctrans status

# 检查 Hook 和翻译引擎是否正常
cctrans doctor

# 测试英文翻译
cctrans test "Hello, I will analyze your code."

# 打开交互式设置界面
cctrans settings
```
