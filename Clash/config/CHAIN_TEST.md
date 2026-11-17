# 香港 A03 前置 → Taxi 9929

在测试订阅中使用本分支的
`ACL4SSR_Online_Full_Google_Custom_Failover.ini` 作为转换模板。
输入仍是现有 Sub-Store 组合订阅，必须包含：

- `🇭🇰 香港A03 | IEPL`
- `VPS-Taxi-LA-9929-5y1mzbjqqb`

输入使用单条 HTTP(S) Sub-Store 订阅地址，不使用多个 URL 的拼接串，
也不使用转换后完整配置的地址。Provider 发送 `clash.meta` User-Agent，
从 Sub-Store 获取 Mihomo 节点数据。

转换器须支持模板变量 `request.url` 和 `!!PROVIDER=`。
本分支的 INI 会自动加载同分支的 `GeneralClashConfig_Chain_Test.yml`。

## 面板中选择

`链式前置·香港A03` 选择香港 A03。
`⛓ 香港A03 → Taxi9929` 从独立 provider 选择带 `⛓` 前缀的 Taxi。

只让 AI 规则走链式时，在 `💬 Ai平台` 中选择
`⛓ 香港A03 → Taxi9929`。
要让跟随 `🚀 节点选择` 的流量走链式，在该组中选择同一个链式出口。
IP 检测网站须走这个出口才能用来验证出口位置。

## 节点如何复用

普通节点仍按原模板生成。新增 provider 再读取同一份原订阅，
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
- 本地订阅转换器已验证模板变量和 provider 引用能生成对应 YAML。

链式出口的业务连通性未通过；需要继续核对落地服务与客户端的
Reality 握手条件。增加选择组不能消除这个错误。
