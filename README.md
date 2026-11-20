# SMI Futures Snapshot v1.4

手机端 Binance USDⓈ-M 永续合约行情快照工具。只使用 U 本位合约公共 API，不回退现货。无需 API Key，不连接交易账户，不下单。

## v1.4 更新
- **指标字段显式化**：紧凑 JSON 的 `ind` 从位置数组改为带字段名称的对象，消除 EMA、RSI、MACD 等指标的字段顺序歧义。
- **快照结构版本升级**：`v` 改为 `2`，新增 `build: "1.4.0"`，可直接核验当前部署版本。
- 保留 v1.3 的 100 根 K 线指标计算窗口、Wilder RSI、OI 历史变化率 `oi_pct` 和可选 K 线导出根数。
- 指标计算根数由 `calculation_bars` 标出；API 返回不足 100 根时，按实际数量计算并如实标注。
- Service Worker 缓存键升级为 `smi-mobile-snapshot-v1.4`，触发客户端缓存更新。

## `ind` 字段定义
每个周期（例如 `15m`）均为以下对象：

| 字段 | 含义 |
|---|---|
| `ema12` | 12 周期指数移动平均线 |
| `ema26` | 26 周期指数移动平均线 |
| `rsi14` | 14 周期 RSI，使用 Wilder 平滑 |
| `macd` | EMA12 - EMA26 |
| `macd_signal` | MACD 线的 9 周期 EMA |
| `macd_histogram` | MACD - MACD Signal |
| `calculation_bars` | 实际用于计算的 K 线数量 |

示例：

```json
"ind": {
  "15m": {
    "ema12": 62147.44,
    "ema26": 62231.78,
    "rsi14": 51.5,
    "macd": -84.33,
    "macd_signal": -169.83,
    "macd_histogram": 85.5,
    "calculation_bars": 100
  }
}
```

指标解读边界：单次快照只能说明当前数值与相对位置，不能单独证明刚刚发生金叉/死叉；RSI 70/30 是参考区间而非独立交易信号。最后一根 K 线可能尚未收盘，指标可能随行情变化。

## OI变化口径
`oi_pct` 的 5m/15m/1h 分别是 Binance 历史 OI 接口最近两个对应周期采样点之间的变化率，不是严格滚动过去 5/15/60 分钟的连续实时变化。接口可能对部分合约/周期无数据；此时返回 `null`，不能当作 0。

## 主要 API 来源
- `/fapi/v1/exchangeInfo`：确认可交易 U 本位永续合约
- `/fapi/v1/ticker/24hr`、`/fapi/v1/ticker/bookTicker`、`/fapi/v1/premiumIndex`
- `/fapi/v1/openInterest`、`/futures/data/openInterestHist`
- `/fapi/v1/klines`
- `/futures/data/takerlongshortRatio`
- `/futures/data/globalLongShortAccountRatio`
- `/futures/data/topLongShortPositionRatio`
- `/futures/data/topLongShortAccountRatio`

不同 REST 请求的采集时刻可能存在秒级差异；多空比是账户或持仓统计口径，不代表全市场真实资金净流向。
