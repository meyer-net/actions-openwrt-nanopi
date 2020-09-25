## 帮助集锦


```
GitHub Actions 的配置文件叫做 workflow 文件，存放在代码仓库的.github/workflows目录并有一些自己的术语。

workflow 文件采用 YAML 格式，文件名可以任意取，但是后缀名统一为.yml，比如foo.yml。

一个库可以有多个 workflow 文件。GitHub 只要发现.github/workflows目录里面有.yml文件，就会自动运行该文件。

如下为相关的一些概念：

（1）workflow （工作流程）：持续集成一次运行的过程，就是一个 workflow。

（2）job （任务）：一个 workflow 由一个或多个 jobs 构成，含义是一次持续集成的运行，可以完成多个任务。

（3）step（步骤）：每个 job 由多个 step 构成，一步步完成。

（4）action （动作）：每个 step 可以依次执行一个或多个命令（action）。
```

## 释义：'.github/workflows/xxxx.yml'

（1）name：name字段是 workflow 的名称。如果省略该字段，默认为当前 workflow 的文件名。

```
name: GitHub Actions Demo
```

（2）on：on字段指定触发 workflow 的条件，通常是某些事件。

```
on:
  repository_dispatch:
  release:
    types: published
  push:
    branches:
      - master
    paths:
      - 'config/lean/rockchip/r2s-lean-openwrt.config'
  schedule:
    - cron: 0 18 * * *
```
如上，则表示每次release为publish时/master分支中r2s-lean-openwrt.config变动时/每天18:00时。

完整的事件列表，请查看(官方文档)<https://docs.github.com/en/actions/reference/events-that-trigger-workflows>。除了代码库事件，GitHub Actions 也支持外部事件触发，或者定时运行。

（3）Jobs：workflow 文件的主体是jobs字段，表示要执行的一项或多项任务。

```
jobs:
  build:
    runs-on: ubuntu-18.04
    if: github.event.repository.owner.id == github.event.sender.id

    steps:
    - name: Checkout
      uses: actions/checkout@main  #指向分支

    - name: Initialization Environment
      env:
        DEBIAN_FRONTEND: noninteractive
      run: |
        sudo curl ip.sb
```

Jobs字段里面，需要写出每一项任务的job_id，具体名称自定义。job_id里面的name字段是任务的说明。

如上，有一个<Job_id>为build，运行在ubuntu-18.04虚拟环境（必填字段），目前可用虚拟机如下：
```
ubuntu-latest，ubuntu-18.04或ubuntu-16.04
windows-latest，windows-2019或windows-2016
macOS-latest或macOS-10.14
```

并通过{steps}字段（系统）指定当前Job的运行步骤，创建了一个名为{Initialization Environment}（自定义）的步骤，指定了一个环境变量{env}${DEBIAN_FRONTEND}，执行命令{run}：...。

## 其它
生成密钥：https://docs.github.com/en/github/authenticating-to-github/creating-a-personal-access-token