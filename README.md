# SMI Market Intelligence Snapshot V2.0

手机端 Binance 多维市场快照。支持 USDⓈ-M 普通永续、TradFi 永续和 Spot；仅使用公开市场数据，不需 API Key，不连接账户，不下单。

## V2.0 Core Snapshot
- 默认多周期：15m、1h、4h、1d；可选5m、1w。
- 指标独立使用最多500根已收盘K线计算；导出K线默认每周期8根，二者互不影响。
- SMA20/50/100/200；EMA12/20/26/50/100/200；价格偏离、均线排列、SMA/EMA/综合压缩度及压缩分位数。
- ATR14/20、ATR%、ATR历史分位数、ADX14、+DI/-DI。
- Bollinger(20,2)、Bandwidth、Bandwidth分位数、%B。
- RSI6/14、Stoch RSI14、MACD(12,26,9)、ROC9/20、CCI20。
- OI历史变化周期扩展到5m/15m/1h/4h/1d。
- 主动买卖沿用K线taker-buy字段；滚动CVD输出20/50/100根Delta、价格变化和候选背离。
- Trend Compression Scanner输出压缩/突破候选/趋势方向状态及分项评分，不生成未经回测的总分或胜率。

## 关键定义与限制
- 所有技术指标只用已收盘K线；导出的K线可含未收盘最新K线。
- EMA200以400根已收盘K线作为预热完成门槛；每周期输出实际可用根数、预热状态及最近收盘时间。数据不足会明确标记。
- CVD为最近最多100根已收盘K线的滚动主动成交Delta代理，不是跨快照永久累计CVD，也不能识别具体账户身份。
- OI变化是相邻历史采样点变化；由于接口时间粒度不同，不与K线价格变化伪装成严格同步。
- 缺失值使用null；接口不支持、请求失败和样本不足不得用0代替。
- OI参与扫描评分暂为null，避免在数据时间未对齐时生成虚假评分。压缩不代表方向预测，突破候选不代表确认信号。
- TradFi永续K线使用continuousKlines(contractType=TRADIFI_PERPETUAL)，普通永续使用fapi klines，现货使用Spot klines。

## 暂未纳入
多档订单簿深度、VWAP/Anchored VWAP、Volume Profile、清算历史、完整BOS/CHoCH、跨交易所聚合、链上聪明钱，留待V2.1及以后。

## 部署
将压缩包内5个文件直接覆盖GitHub Pages发布目录同名文件，等待部署完成后清理手机浏览器站点缓存并重新打开。成功标识：`"v":7`、`"build":"2.0.0"`。
