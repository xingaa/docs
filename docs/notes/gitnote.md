# 给项目单独设置user.name

在Git中，可以为某个特定项目单独设置`user.name`和`user.email`，而不会影响其他项目的配置。具体步骤如下：

### 1. 进入项目目录

```bash
cd /path/to/your/project
```

### 2. 设置本地的 `user.name` 和 `user.email`

```bash
git config user.name "Your Name"
git config user.email "your.email@example.com"
```

::: tip
全局设置
:::

```bash
git config --global user.name "Your Name"
git config --global user.email "your.email@example.com"

```

### 3. 验证设置

```bash
** 本地项目 **
git config --local --list

** 全局配置 **
git config --global user.name
```

