# 香港 A03 前置 → Taxi 9929

在测试订阅中使用本分支的
`ACL4SSR_Online_Full_Google_Custom_Failover.ini` 作为转换模板。
输入仍是现有 Sub-Store 组合订阅，必须包含：

- `🇭🇰 香港A03 | IEPL`
- `VPS-Taxi-LA-9929-5y1mzbjqqb`

输入使用单条 HTTP(S) Sub-Store 订阅地址，不使用多个 URL 的拼接串，
也不使用转换后完整配置的地址。节点来源仍由原 provider 从 Sub-Store 更新，
原订阅的导出应使用 Mihomo/ClashMeta 格式。

此测试使用 `api.asailor.org` 生成的 provider 配置和配套 OpenClash 覆写模块。
该在线转换器会重建整个 `proxy-providers`，覆盖基础模板声明的 provider，
所以仅替换 INI 不足以创建这条链路。

在独立测试订阅中使用本分支 INI，将测试配置命名为
`chain-hk-a03-taxi-test.yaml`。在 OpenClash 运行状态页顶部
“Overwrite Module”中导入本分支的 `Clash/overwrite/Chain_HK_A03_Taxi.conf`。
模块默认匹配 `/etc/openclash/config/chain-hk-a03-taxi-test.yaml`，
如果测试配置另取名称，需在模块信息中选择对应的完整配置路径。
模块新建时默认关闭；启用并重启 OpenClash 才会应用。
切换或重启会影响当前连接，应由使用者安排测试时间。

## 面板中选择

`链式前置·香港A03` 选择香港 A03。
`⛓ 香港A03 → Taxi9929` 从独立 provider 选择带 `⛓` 前缀的 Taxi。

只让 AI 规则走链式时，在 `💬 Ai平台` 中选择
`⛓ 香港A03 → Taxi9929`。
要让跟随 `🚀 节点选择` 的流量走链式，在该组中选择同一个链式出口。
IP 检测网站须走这个出口才能用来验证出口位置。

## 节点如何复用

普通节点仍按原模板生成。覆写模块在转换完成后，复制前置组使用的 provider 定义，
保留其原订阅地址与请求头，给新 provider 设置独立的缓存文件，
只筛选 Taxi，在该 provider 范围内设置：

```yaml
override:
  dialer-proxy: 链式前置·香港A03
  additional-prefix: "⛓ "
```

这样原 Taxi 保持原来的连接方式，链式 Taxi 使用香港 A03 前置。
Sub-Store 无需新增普通节点或更改 UUID、服务器、Reality 参数。

## 本次验证

2026-11-17，在现有 Mihomo 内核的独立实例上验证了 provider 覆写和分组解析，
实例只监听回环地址，没有更改 OpenClash 运行配置。

- 香港 A03 单独访问 Cloudflare trace 成功，出口为 HK。
- 美西 A 单独访问成功，出口为 US。
- 香港 A03 → Taxi：落地握手报 `REALITY authentication failed`。
- 美西 A → 美西 B：落地握手报 `REALITY authentication failed`。
- 本地订阅转换器已验证 provider 引用能生成对应 YAML。
- 在线转换器实测会覆盖基础模板声明的 provider，配套覆写模块因此在转换后添加链式 provider。
- 配套模块已通过路由器实际 OpenClash 解析器检查；应用后原 provider、节点、分组及规则不变，重复应用结果不变。
- 应用模块后的完整生成配置通过了当前 Mihomo 内核的 `-t` 检查。

链式出口的业务连通性未通过；需要继续核对落地服务与客户端的
Reality 握手条件。增加选择组不能消除这个错误。
