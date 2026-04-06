# Clash Rules Repo Scaffold

这个目录只用于托管可公开的规则文件和脱敏模板，不保存真实使用中的 `clash.yaml`。

## 目录结构

```text
.
├── .gitignore
├── README.md
├── rules/
│   ├── direct.yaml
│   ├── reject.yaml
│   └── proxy.yaml
└── templates/
    └── clash.template.yaml
```

## 当前落地方式

真实使用中的 [clash.yaml](/mnt/c/Users/pluie/Documents/sync/config/clash/clash.yaml) 仍然保留在本地，但已经预留了三份自定义规则文件：

- `custom-reject` -> [rules/reject.yaml](/mnt/c/Users/pluie/Documents/sync/config/clash/rules/reject.yaml)
- `custom-direct` -> [rules/direct.yaml](/mnt/c/Users/pluie/Documents/sync/config/clash/rules/direct.yaml)
- `custom-proxy` -> [rules/proxy.yaml](/mnt/c/Users/pluie/Documents/sync/config/clash/rules/proxy.yaml)

本地先用 `type: file` 直接读取这三份文件，这样现在就能把经常变更的规则从主配置里拆出去，不需要等 GitHub 仓库建完。

## 后续切到 GitHub Raw URL

1. 把当前目录推到你的 GitHub 仓库。
2. 保持 `rules/*.yaml` 路径不变。
3. 把本地 [clash.yaml](/mnt/c/Users/pluie/Documents/sync/config/clash/clash.yaml) 中三个 `custom-*` rule-provider 从 `type: file` 改成 `type: http`。
4. 参考 [templates/clash.template.yaml](/mnt/c/Users/pluie/Documents/sync/config/clash/templates/clash.template.yaml) 中的占位 URL，填入真实仓库地址。

推荐改成下面这种形式：

```yaml
custom-reject:
  type: http
  behavior: classical
  format: yaml
  url: "https://raw.githubusercontent.com/<github-user>/<repo>/<branch>/rules/reject.yaml"
  path: ./ruleset/custom-reject.yaml
  interval: 86400
```

`custom-direct` 和 `custom-proxy` 同理。

## 规则维护约定

- [rules/reject.yaml](/mnt/c/Users/pluie/Documents/sync/config/clash/rules/reject.yaml): 需要强制拦截的域名/关键字/IP 规则
- [rules/direct.yaml](/mnt/c/Users/pluie/Documents/sync/config/clash/rules/direct.yaml): 需要强制直连的规则
- [rules/proxy.yaml](/mnt/c/Users/pluie/Documents/sync/config/clash/rules/proxy.yaml): 需要强制走代理的规则

三份规则文件都使用 `behavior: classical` 对应的 YAML 格式：

```yaml
payload:
  - DOMAIN-SUFFIX,example.com
  - DOMAIN-KEYWORD,tracker
  - IP-CIDR,1.2.3.0/24
```

## 分流原则

- `private` / `lancidr` 直连
- 广告先走自定义拦截，再走 Loyalsoldier `reject`
- Apple / iCloud 默认走代理
- 自定义 `proxy` 在公开 `direct` 规则集之前，便于覆盖误判
- 自定义 `direct` 继续补充本地直连站点
- `GEOIP,CN,DIRECT`
- `MATCH,🚀 节点选择`

## 验证清单

- 国内常见站点命中 `DIRECT`
- Apple / iCloud 命中 `🍎 苹果服务`
- 广告域名命中 `🛑 广告拦截`
- OpenAI / Google / Telegram 命中 `🚀 节点选择`
- 未知站点最终落到 `MATCH,🚀 节点选择`
