# clash-rules

用于托管可公开的 Mihomo / Clash 自定义规则文件。

这个仓库只保存经常变动的规则集和脱敏模板，不保存真实使用中的 `clash.yaml`，也不保存节点信息。

## 1. 目录结构

```text
.
├── rules/
│   ├── direct.yaml
│   ├── reject.yaml
│   └── proxy.yaml
└── templates/
    └── clash.template.yaml
```

## 2. 规则文件

- `rules/direct.yaml`: 强制直连的自定义规则
- `rules/reject.yaml`: 强制拦截的自定义规则
- `rules/proxy.yaml`: 强制走代理的自定义规则

当前规则文件使用 `behavior: classical` 对应的 YAML 格式：

```yaml
payload:
  - DOMAIN-SUFFIX,example.com
  - DOMAIN-KEYWORD,tracker
  - IP-CIDR,1.2.3.0/24
```

如果你使用的是 `behavior: domain`，则应改用 Clash wildcard 语法，例如：

```yaml
payload:
  - '+.example.com'
```

## 3. 在配置中引用

可在本地 `clash.yaml` / `mihomo.yaml` 中这样引用：

```yaml
rule-providers:
  custom-reject:
    type: http
    behavior: classical
    format: yaml
    url: "https://raw.githubusercontent.com/pluiez/clash-rules/main/rules/reject.yaml"
    path: ./ruleset/custom-reject.yaml
    interval: 86400

  custom-direct:
    type: http
    behavior: classical
    format: yaml
    url: "https://raw.githubusercontent.com/pluiez/clash-rules/main/rules/direct.yaml"
    path: ./ruleset/custom-direct.yaml
    interval: 86400

  custom-proxy:
    type: http
    behavior: classical
    format: yaml
    url: "https://raw.githubusercontent.com/pluiez/clash-rules/main/rules/proxy.yaml"
    path: ./ruleset/custom-proxy.yaml
    interval: 86400
```

对应的 `rules` 示例：

```yaml
rules:
  - RULE-SET,custom-reject,🛑 广告拦截
  - RULE-SET,custom-proxy,🚀 节点选择
  - RULE-SET,custom-direct,DIRECT
```

规则按从上到下的顺序匹配，优先级由上往下递减。

如果你没有单独的广告策略组，也可以直接写成：

```yaml
- RULE-SET,custom-reject,REJECT
```

## 4. 模板

`templates/clash.template.yaml` 提供了一份脱敏模板，用于演示如何把这三个规则集接入本地主配置。

## 5. 说明

- 真实使用中的主配置文件不应提交到仓库
- 节点、订阅、密钥等敏感信息不应提交到仓库
- 这个仓库更适合存放需要频繁调整的增量规则，而不是完整配置

## 6. License

MIT
