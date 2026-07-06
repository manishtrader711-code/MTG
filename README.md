//@version=5
indicator("Nexa Pro", shorttitle="Nexa Pro", overlay=true)

// === Nexa Pattern Signal ===
data = (close[3] > open[3]) == (close[1] > open[1])
dir = close[3] < close[1]

// Signal Arrows
plotshape(data and dir,
     title="BUY",
     style=shape.triangleup,
     location=location.belowbar,
     color=color.green,
     size=size.small)

plotshape(data and not dir,
     title="SELL",
     style=shape.triangledown,
     location=location.abovebar,
     color=color.red,
     size=size.small)

// Labels
if data and dir
    label.new(bar_index, low, "BUY",
         style=label.style_label_up,
         color=color.green,
         textcolor=color.white)

if data and not dir
    label.new(bar_index, high, "SELL",
         style=label.style_label_down,
         color=color.red,
         textcolor=color.white)

// === ATR Trend Filter ===
Periods = input.int(10, "ATR Period")
Multiplier = input.float(3.0, "ATR Multiplier")

src = hl2
atr = ta.atr(Periods)

up = src - (Multiplier * atr)
dn = src + (Multiplier * atr)

up := close[1] > nz(up[1]) ? math.max(up, nz(up[1])) : up
dn := close[1] < nz(dn[1]) ? math.min(dn, nz(dn[1])) : dn

var int trend = 1

trend := trend == -1 and close > dn[1] ? 1 :
         trend == 1 and close < up[1] ? -1 :
         trend

buySignal = trend == 1 and trend[1] == -1
sellSignal = trend == -1 and trend[1] == 1

plotshape(buySignal,
     title="Trend Buy",
     style=shape.arrowup,
     location=location.belowbar,
     color=color.lime,
     size=size.normal)

plotshape(sellSignal,
     title="Trend Sell",
     style=shape.arrowdown,
     location=location.abovebar,
     color=color.red,
     size=size.normal)

// Alerts
alertcondition(buySignal,
     title="Buy Alert",
     message="BUY!")

alertcondition(sellSignal,
     title="Sell Alert",
     message="SELL!")

// === EMA 200 ===
length = input.int(200, title="EMA Period", minval=1)
emaValue = ta.ema(close, length)

plot(emaValue,
     color=color.purple,
     linewidth=2,
     title="EMA 200")
