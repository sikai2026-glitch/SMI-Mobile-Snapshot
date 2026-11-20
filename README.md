# SMI Futures Snapshot v1.3

手机端 Binance USDⓈ-M 永续合约行情快照工具。只使用 U 本位合约公共 API，不回退现货。

## v1.3 更新
- K线导出根数与指标计算窗口分离：每个勾选周期请求最近100根K线，指标用完整100根计算，快照只导出用户选择的6/8/12根。
- RSI 改为 Wilder 平滑算法；EMA12/26、MACD/Signal/Histogram使用100根K线计算，降低短导出窗口导致RSI为null或MACD受初始值影响过大的问题。
- 新增历史OI变化率：通过 `/futures/data/openInterestHist` 分别读取5m、15m、1h最近两个采样点，计算相邻采样点间OI数量变化百分比；同时保留名义价值变化率供内部检查。
- 页面显示 OI 5m/15m/1h变化率；紧凑JSON新增 `oi_pct`。
- 指标数组末尾增加实际计算K线根数，便于核验。

## OI变化口径
`oi_pct` 的5m/15m/1h分别是 Binance 历史OI接口最近两个对应周期采样点之间的变化率，不是严格滚动过去5/15/60分钟的连续实时变化。接口可能对部分合约/周期无数据；此时返回 null，不能当作0。

## API来源
- `/fapi/v1/exchangeInfo`：确认可交易U本位永续合约
- `/fapi/v1/ticker/24hr`、`/fapi/v1/ticker/bookTicker`、`/fapi/v1/premiumIndex`
- `/fapi/v1/openInterest`、`/futures/data/openInterestHist`
- `/fapi/v1/klines`
- `/futures/data/takerlongshortRatio`
- `/futures/data/globalLongShortAccountRatio`
- `/futures/data/topLongShortPositionRatio`
- `/futures/data/topLongShortAccountRatio`

无需API Key，不连接交易账户，不下单。最后一根K线可能未收盘；不同接口采集时刻可能存在秒级差异。
