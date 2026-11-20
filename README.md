# SMI Market Snapshot v1.8

手机端 Binance 市场快照工具，自动识别普通 USDⓈ-M 永续、TradFi 永续或现货交易对。仅使用公开市场数据，不需要 API Key，不连接账户，不下单。

## v1.8 主动买卖数据升级

新增 5m、15m、1h 三个周期最近一根 K 线的主动买卖成交统计，页面展示：
- 主动买入成交额（报价资产）
- 主动卖出成交额（报价资产）
- 主动买卖成交额比（买入额 / 卖出额）
- 主动买入占比、主动卖出占比

快照命名结构 `taker_flow.periods[周期]` 同时提供：
- `buy_volume_base` / `sell_volume_base`：基础资产数量
- `buy_volume_quote` / `sell_volume_quote`：报价资产金额
- `buy_sell_ratio_quote`：主动买入额 / 主动卖出额
- `buy_share_quote` / `sell_share_quote`：主动买入额、主动卖出额占该周期总成交额的比例
- `bar_open_time` / `bar_close_time` / `is_closed`：该统计对应K线的时间和是否收盘
- `method`：数据来源口径

计算口径：Binance K线字段 `taker_buy_base_asset_volume` 和 `taker_buy_quote_asset_volume` 是主动买入量/额；主动卖出量/额分别用该K线总成交量/额减去主动买入量/额计算。此处是该周期最近一根K线的主动成交统计，不是整个24小时累计，也不是订单簿挂单量。最后一根K线未收盘时数据为实时累积值。

合约市场额外保留 Binance `/futures/data/takerlongshortRatio` 的 15m 独立聚合结果作为 `exchange_aggregate_15m`，用于交叉参考；其买卖量单位不在此处擅自换算为报价资产金额。

## 市场识别

1. 查询 USDⓈ-M `/fapi/v1/exchangeInfo`。
2. `contractType=PERPETUAL` 或 `TRADIFI_PERPETUAL` 且 `status=TRADING` 时，按合约采集。
3. TradFi 永续K线使用 `/fapi/v1/continuousKlines?contractType=TRADIFI_PERPETUAL`。
4. 未匹配可交易合约时再查询 Spot `/api/v3/exchangeInfo`，仅接受可交易现货。
5. 不把网络故障误判为交易对不存在，不把现货伪装成合约。

## 数据范围

### USDⓈ-M Futures / TradFi Futures
- 24h ticker、标记/指数价格、资金费率、结算时间
- OI及历史OI变化（接口不支持时为 null）
- 盘口、主动买卖成交、全市场及大户多空比（不可用时为 null）
- 多周期K线、EMA12/26、Wilder RSI14、MACD/Signal/Histogram

### Spot
- 24h ticker、最新成交价、盘口
- 多周期K线、EMA12/26、Wilder RSI14、MACD/Signal/Histogram
- 5m、15m、1h 主动买卖成交统计
- OI、资金费率、合约多空比、标记价格、指数价格等合约专属字段不适用，使用 null。

## 快照结构

快照版本 `v: 6`，构建标识 `build: "1.8.0"`。`ind` 使用命名对象；`taker_flow` 使用按周期命名对象，不使用位置数组。

## API

- USDⓈ-M Futures: `https://fapi.binance.com`
- Spot: `https://api.binance.com`

## 部署

将压缩包内 5 个文件直接覆盖 GitHub Pages 发布目录中的同名文件，等待部署完成后清理手机浏览器该站点缓存并重新打开。成功后 JSON 应包含 `"build":"1.8.0"`。
