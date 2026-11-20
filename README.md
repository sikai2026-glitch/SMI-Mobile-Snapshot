# SMI Futures Snapshot v1.2

手机端 Binance USDⓈ-M 永续合约快照。所有行情数据来自 Binance USDⓈ-M Futures 公共 API，不回退现货。

## v1.2 精简导出
- 默认每周期采集 8 根K线，可选 6 / 12 根。
- 复制时输出单行紧凑 JSON，不再输出逐根K线的长字段对象。
- K线数组字段顺序：UTC月日时分、开、高、低、收、成交额USDT、主动买入额USDT。
- 不导出20档盘口深度；盘口仅保留最优买卖价和价差。
- 保留标记价格、24h涨跌幅/高低/成交额、OI、资金费率、主动买卖比、多空比及 EMA/RSI/MACD。
- 技术指标使用采集到的完整K线窗口计算，导出的K线根数可以更少。

## 部署
将 index.html、manifest.webmanifest、sw.js、icon.svg、README.md 上传到 GitHub 仓库根目录，覆盖旧版文件。GitHub Pages 继续使用 main / root。

无需 API Key，不连接账户，不下单。
