# SMI Market Snapshot v1.7

手机端 Binance 市场快照工具，按交易所实时元数据自动识别普通 USDⓈ-M 永续合约、TradFi 永续合约或现货交易对。适用于加密资产及任何确实在 Binance 对应市场上市、当前可交易的资产；不根据名称推断资产类别。

## 市场识别规则

1. 查询 Binance USDⓈ-M `/fapi/v1/exchangeInfo`。
2. 若交易对存在且 `contractType=PERPETUAL` 或 `TRADIFI_PERPETUAL`、`status=TRADING`，均按 USDⓈ-M Futures 合约处理。
3. `TRADIFI_PERPETUAL`（股票、ETF、商品、外汇等 TradFi 标的）使用 `/fapi/v1/continuousKlines?pair=...&contractType=TRADIFI_PERPETUAL` 获取K线。
4. 两类可交易永续合约均未找到时，独立查询 Binance Spot `/api/v3/exchangeInfo?symbol=...`。
5. 现货交易对必须存在、`status=TRADING` 且允许 SPOT 交易，才使用现货数据。
6. 若 API 返回无效交易对，提示两个市场均未找到；若 API 网络请求失败，则明确标记为查询失败，不把网络错误误判为交易对不存在。
7. 不会把现货行情伪装成合约行情，也不会把股票代码或相似代码自动替换成其他交易对。

## Futures / TradFi Futures 数据

- 24h ticker、标记/指数价格、资金费率、下一次结算时间（以接口实际可用字段为准）
- 当前 OI、OI 历史采样变化（TradFi 某些统计接口可能不提供）
- 盘口、主动买卖量比、全市场及大户多空比（不可用时记录为 null，不阻断快照）
- 多周期 K 线和 EMA12/EMA26、Wilder RSI14、MACD/Signal/Histogram

## Spot 数据

- 24h ticker、最新成交价、盘口
- 多周期 K 线和 EMA12/EMA26、Wilder RSI14、MACD/Signal/Histogram
- 最近一根 15m K 线的主动买入成交额与推算主动卖出成交额比值
- OI、资金费率、合约多空比、标记价格、指数价格等合约专属字段标记为不适用，使用 `null`，不伪造为 0。

## API

- USDⓈ-M Futures: `https://fapi.binance.com`
- Spot: `https://api.binance.com`
- 仅使用公开行情接口，不需要 API Key，不连接账户，不下单。

## 指标与 K 线

指标最多使用最近 100 根 K 线计算；导出 K 线根数可单独选择。指标以命名对象输出：`ema12`、`ema26`、`rsi14`、`macd`、`macd_signal`、`macd_histogram`、`calculation_bars`。最后一根 K 线可能尚未收盘。

## v1.7 改进

- 修复 TradFi 永续识别缺陷：此前只接受 `contractType=PERPETUAL`，会把 `TRADIFI_PERPETUAL`（例如 NVDAUSDT）错误地当成不存在。
- 识别 `TRADIFI_PERPETUAL` 并通过 Binance 官方 `continuousKlines` 接口获取对应周期K线。
- 快照显式输出 `market_type=USDⓈ-M_TRADFI_PERPETUAL` 和 `contract_type=TRADIFI_PERPETUAL`。
- 继续保留普通加密货币永续与现货回退。
- 将“交易对不存在”与“API 网络不可访问”分开报告，避免把网络故障误判成未上市。
- 现货交易对校验增加 SPOT 权限判断（兼容 Binance 不同元数据字段）。
- 不再将所有现货资产直接标记为股票代币候选；资产类别以外部研究为准。
- 页面版本及快照版本更新为 `v: 5`、`build: "1.7.0"`。

## 部署

将压缩包中的 5 个文件直接覆盖 GitHub Pages 发布目录中的同名文件。等待部署完成后，在手机浏览器清理站点缓存并重新打开。成功部署后，复制的 JSON 应包含 `build: "1.7.0"`。
