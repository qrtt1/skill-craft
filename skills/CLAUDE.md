# Skills 目錄結構規則

每個 plugin 是 `skills/` 下的一個子目錄，結構如下：

```
skills/<plugin-name>/
├── .claude-plugin/
│   └── plugin.json          # plugin 名稱、版號、描述
└── skills/
    └── <skill-name>/
        └── SKILL.md          # skill 定義與流程
```

- 每個 plugin 必須有 `.claude-plugin/plugin.json`
- 每個 skill 必須放在 `skills/<skill-name>/SKILL.md` 路徑下
- 新增 plugin 後，須在 `.claude-plugin/marketplace.json` 中註冊
- skill 的建立與修改使用 `skill-creator` + `reflect-skill-review` 流程
- 版號更新後使用 `reflect-skill-release` 完成 release
- 使用 `reflect-skill-lint` 檢查目錄結構是否符合規範
