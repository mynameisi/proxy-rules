# proxy-rules

Reusable proxy rulesets, app-agnostic. Each ruleset is published in two formats
so different proxy apps can reference it.

Rulesets contain only matchers (domains), never a policy. Each app maps the
set to `DIRECT` or its proxy group.

## Rulesets

### `kimi-direct` — Kimi / Moonshot AI must bypass the proxy

Covers: `kimi.com`, `kimi.moonshot.cn`, `moonshot.cn` (incl. `api.moonshot.cn`,
`platform.moonshot.cn`), `moonshot.ai` (incl. `api.moonshot.ai`,
`platform.moonshot.ai`), `moonshotai.cn`.

### `cn-direct` — China / domestic apps must bypass the proxy

Merged from the Surge `WGZ_US` profile: WeChat / WeCom / QQ extras, 人人视频
(`yichengwlkj.com`, `rrmj.plus`, `bwcgee.cn`, `rr.tv`), Feishu, ByteDance,
Ali, Bilibili, JD/Meituan-adjacent hostnames that were listed inline, and
other `.cn` / domestic suffixes. Does **not** duplicate `kimi-direct`.

JingDong / MeiTuan / WeChat long-tail CDNs stay on blackmatrix7 URL rule-sets
in the client config (not vendored here).

### `must-proxy` — must go through the proxy

Google / YouTube, Cursor, LinkedIn, OpenAI, Grok, PayPal, Udacity, etc.
Map this to your proxy group (`Datacenter` on Surge, `PROXY` on Clash).

| File | Format | For |
|:-----|:-------|:----|
| `rulesets/<name>.yaml` | mihomo/Clash rule-provider, `behavior: domain` | mihomo, Clash Verge, Clash Meta, FlClash |
| `rulesets/<name>.list` | Classical `DOMAIN-SUFFIX,...` lines | Clash `behavior: classical`, Surge, Loon |

`.list` may include `DOMAIN-KEYWORD` / `DOMAIN` that `.yaml` omits.

## Referencing from a proxy app

Raw URLs (repo is public):

```
https://raw.githubusercontent.com/mynameisi/proxy-rules/master/rulesets/kimi-direct.yaml
https://raw.githubusercontent.com/mynameisi/proxy-rules/master/rulesets/kimi-direct.list
https://raw.githubusercontent.com/mynameisi/proxy-rules/master/rulesets/cn-direct.yaml
https://raw.githubusercontent.com/mynameisi/proxy-rules/master/rulesets/cn-direct.list
https://raw.githubusercontent.com/mynameisi/proxy-rules/master/rulesets/must-proxy.yaml
https://raw.githubusercontent.com/mynameisi/proxy-rules/master/rulesets/must-proxy.list
```

### mihomo / Clash Verge

```yaml
rule-providers:
  kimi-direct:
    type: http
    behavior: domain
    format: yaml
    url: https://raw.githubusercontent.com/mynameisi/proxy-rules/master/rulesets/kimi-direct.yaml
    interval: 86400
  cn-direct:
    type: http
    behavior: domain
    format: yaml
    url: https://raw.githubusercontent.com/mynameisi/proxy-rules/master/rulesets/cn-direct.yaml
    interval: 86400
  must-proxy:
    type: http
    behavior: domain
    format: yaml
    url: https://raw.githubusercontent.com/mynameisi/proxy-rules/master/rulesets/must-proxy.yaml
    interval: 86400

rules:
  - RULE-SET,kimi-direct,DIRECT
  - RULE-SET,cn-direct,DIRECT
  - RULE-SET,must-proxy,PROXY
  # ... rest of your rules
```

Clash Verge GUI: Profiles → **Merge** (rule-providers) + **Rules** prepend
the three `RULE-SET` lines.

### Surge

```
RULE-SET,https://raw.githubusercontent.com/mynameisi/proxy-rules/master/rulesets/kimi-direct.list,DIRECT,extended-matching
RULE-SET,https://raw.githubusercontent.com/mynameisi/proxy-rules/master/rulesets/cn-direct.list,DIRECT,extended-matching
RULE-SET,https://raw.githubusercontent.com/mynameisi/proxy-rules/master/rulesets/must-proxy.list,Datacenter,extended-matching
```

Put the `must-proxy` line **after** any Google QUIC/IPv6 REJECT rules.

### If you keep a local clone

```bash
gh repo clone mynameisi/proxy-rules ~/proxy-rules
git -C ~/proxy-rules pull
```

mihomo `type: file` paths are relative to the config dir; copy or use an
absolute path.

## Notes

- No credentials or subscription URLs live in this repo.
- Machine-specific Surge rules (PROCESS-NAME, IP-CIDR6, MITM, USER-AGENT)
  stay in the local profile, not here.
