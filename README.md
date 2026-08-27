# proxy-rules

Reusable proxy rulesets, app-agnostic. Each ruleset is published in two formats
so different proxy apps can reference it.

## Rulesets

### `kimi-direct` — Kimi / Moonshot AI must bypass the proxy

Covers: `kimi.com`, `kimi.moonshot.cn`, `moonshot.cn` (incl. `api.moonshot.cn`,
`platform.moonshot.cn`), `moonshot.ai` (incl. `api.moonshot.ai`,
`platform.moonshot.ai`), `moonshotai.cn`.

| File | Format | For |
|:-----|:-------|:----|
| `rulesets/kimi-direct.yaml` | mihomo/Clash rule-provider, `behavior: domain` | mihomo, Clash Verge, Clash Meta clients, FlClash, mihomo party |
| `rulesets/kimi-direct.list` | Classical `DOMAIN-SUFFIX,...` lines | Clash `behavior: classical` providers, Surge-compatible apps |

## Referencing from a proxy app

### If this repo is PUBLIC — remote URL (auto-updating)

Raw URLs:

```
https://raw.githubusercontent.com/mynameisi/proxy-rules/master/rulesets/kimi-direct.yaml
https://raw.githubusercontent.com/mynameisi/proxy-rules/master/rulesets/kimi-direct.list
```

mihomo config example:

```yaml
rule-providers:
  kimi-direct:
    type: http
    behavior: domain
    format: yaml
    url: https://raw.githubusercontent.com/mynameisi/proxy-rules/master/rulesets/kimi-direct.yaml
    interval: 86400

rules:
  - RULE-SET,kimi-direct,DIRECT
  # ... rest of your rules
```

### If this repo is PRIVATE — local file (clone first)

GitHub raw URLs of private repos require auth tokens that proxy apps can't
handle, so clone the repo on each machine and reference the local file:

```bash
gh repo clone mynameisi/proxy-rules ~/proxy-rules   # or: git clone git@github.com:mynameisi/proxy-rules.git
```

```yaml
rule-providers:
  kimi-direct:
    type: file
    behavior: domain
    format: yaml
    path: ~/proxy-rules/rulesets/kimi-direct.yaml   # mihomo: path is relative to the config dir ($HOME/.config/mihomo); use an absolute path or copy the file in

rules:
  - RULE-SET,kimi-direct,DIRECT
```

Run `git -C ~/proxy-rules pull` occasionally to pick up changes.

### Clash Verge (GUI)

Profiles → your profile → **Rules** enhancement → prepend:

```yaml
prepend:
  - RULE-SET,kimi-direct,DIRECT
```

with the rule-provider added via the profile's **Merge** enhancement
(`rule-providers:` block as above).

## Notes

- Rulesets contain only public domain names — no credentials or subscription
  data — so making this repo public exposes nothing sensitive.
