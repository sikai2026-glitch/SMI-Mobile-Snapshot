# SMI Futures Snapshot v1.1

本版本只使用 Binance USDⓈ-M Futures 公共接口（`https://fapi.binance.com`），不使用现货 API，也不会在合约不存在时回退到现货。

## 部署
将 `index.html`、`manifest.webmanifest`、`sw.js`、`icon.svg` 上传到 GitHub 仓库根目录；Settings → Pages → Deploy from a branch → main / root。用生成的 HTTPS 地址在手机浏览器打开，并添加到主屏幕。

## 数据
- `/fapi/v1/exchangeInfo`：验证交易对确实是可交易的 U 本位永续合约
- `/fapi/v1/ticker/24hr`：24小时合约行情
- `/fapi/v1/klines`：合约K线
- `/fapi/v1/ticker/bookTicker`、`/fapi/v1/depth`：合约盘口
- `/fapi/v1/premiumIndex`：标记价、指数价、资金费率
- `/fapi/v1/openInterest`：未平仓量
- `/futures/data/takerlongshortRatio`：主动买卖量比
- `/futures/data/globalLongShortAccountRatio`：全市场多空账户比
- `/futures/data/topLongShortPositionRatio`：大户多空持仓比
- `/futures/data/topLongShortAccountRatio`：大户多空账户比

## 说明
OI基础单位为基础资产数量；估算USDT名义价值=OI数量×标记价格。多空比是账户/持仓统计口径，不等同于资金净流入。最后一根K线可能未收盘。部分统计接口可能对新上市或低流动性交易对没有数据，工具会标注不可用。无需API Key，不连接账户，不下单。
