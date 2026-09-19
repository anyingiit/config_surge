[English](README.md) · **简体中文**

> 英文版是规范版本。本页与 [README.md](README.md) 不一致时，以英文版为准。

<!-- translation-of: README.md sha256:1b3f1f09ea4b01ad -->

<!-- Source: Best-README-Template BLANK_README (Unlicense) — https://github.com/othneildrew/Best-README-Template -->
<a id="readme-top"></a>

# config_surge

记录维护者如何在一台 Mac 上配置本地 Surge 代理客户端——一份按日期记录的配置修改日志，外加一篇网络调研笔记——本仓库既不含应用源代码，也未纳入真正的代理配置文件本身。

[![License](https://img.shields.io/github/license/anyingiit/config_surge)](LICENSE)

[报告问题](https://github.com/anyingiit/config_surge/issues/new?template=bug_report.yml) · [提出需求](https://github.com/anyingiit/config_surge/issues/new?template=feature_request.yml)

<details>
  <summary>目录</summary>
  <ol>
    <li><a href="#about-the-project">关于本项目</a></li>
    <li><a href="#getting-started">开始使用</a></li>
    <li><a href="#usage">用法</a></li>
    <li><a href="#contributing">参与贡献</a></li>
    <li><a href="#license">许可证</a></li>
    <li><a href="#contact">联系方式</a></li>
  </ol>
</details>

## 关于本项目

这个仓库本身并不保存 Surge 代理配置，而是保存维护那份配置的记录。`operation-log.md` 是一份按日期记录的变更日志，记录了对维护者本人 Mac 上、位于本仓库之外的一个 Surge 配置文件所做的修改——例如把某个 OpenAI 规则集切换到 Surge 内置的 Tailscale 出口节点，或者同步某个代理订阅的服务器列表。`openai-tailscale-research-2026-09-07.md` 是一份独立成文的调研笔记，把 OpenAI 官方发布的网络指引与实际使用的规则集逐条对照，每条结论都附有来源。`PROJECT_STATUS.md` 由维护者自己的工作区管理工具生成，并非本次文档编写的产物，它独立得出了相同的结论：这里没有清单文件、没有任何语言的源代码，也没有运行步骤。`.firecrawl/` 目录保存了上述日志条目所引用的第三方规则片段与官方文档快照。

计划中的功能与已知问题，见 [open issues](https://github.com/anyingiit/config_surge/issues)。

## 开始使用

### 环境要求

- Git，用于克隆本仓库。
- 如果想在本地运行 `.pre-commit-config.yaml` 中的钩子集，需要 Python 3.9 及以上版本并执行 `pip install pre-commit`；除此之外不需要任何编译器、解释器或依赖清单文件——本仓库中都不存在这些。

### 安装

```sh
git clone https://github.com/anyingiit/config_surge.git
cd config_surge
pre-commit install  # 可选：让下面的钩子在每次提交前自动运行
```

这里没有构建步骤：克隆下来的内容就是本仓库目前的全部内容。

## 用法

这里没有可以运行的程序。请阅读 `operation-log.md`，了解对仓库之外那份 Surge 配置文件所做修改的按日期记录；阅读 `openai-tailscale-research-2026-09-07.md`，了解 2026-09-07 那次 OpenAI 路由变更背后的调研依据。`.firecrawl/` 保存了这些日志条目所引用的原始规则片段与文档快照，例如：

```
.firecrawl/anthropic-added-rules.txt
```

## 参与贡献

欢迎参与。[CONTRIBUTING.md](CONTRIBUTING.md) 说明如何提交 issue 或 pull request，[CODE_OF_CONDUCT.md](CODE_OF_CONDUCT.md) 说明对所有参与者的行为要求。

请不要在公开的 issue 或 pull request 中报告安全问题。[SECURITY.md](SECURITY.md) 说明了私下报告的方式。

## 许可证

以 MIT 许可证分发。详见 [LICENSE](LICENSE)。

## 联系方式

项目地址：[https://github.com/anyingiit/config_surge](https://github.com/anyingiit/config_surge)

<p align="right">(<a href="#readme-top">back to top</a>)</p>
