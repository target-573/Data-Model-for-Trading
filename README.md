# `datamodel.py` : Trading System Data Interface

## Overview

`datamodel.py` defines the core data structures used by the Prosperity trading system. It organises market observations, order books, trades, portfolio positions, and trading instructions into structured Python objects.

It provides a common interface between the trading simulator and the strategy, enabling consistent data handling and communication.

**Key principle:** The data model defines how information is represented, while the trading strategy defines how that information is used to make trading decisions.

## 1. Reusability Across Trading Strategies

The same data model can support multiple strategies, provided they use a compatible simulator interface and receive the required market data.

| Trading Strategy | Reusability |
|---|---|
| ORCHIDS cross-exchange arbitrage | Yes |
| Market making | Yes |
| Momentum trading | Yes, if the required price data is available |
| Mean reversion | Yes, if sufficient price history is available |
| Multi-product trading | Yes, if the simulator provides the relevant listings and state |
| Stock trading through a broker API | Not directly; the interface may require adaptation |

## 2. How the Same Data Model Supports Different Strategies

The simulator provides a `TradingState` object containing the information available to the strategy. Different strategies can use the same structures for different purposes.

### Strategy A: Cross-Exchange Arbitrage

- **`OrderDepth`** provides local market bid and ask prices.
- **`ConversionObservation`** provides conversion prices, tariffs, and transportation fees.
- The strategy compares local prices with conversion costs to identify potential arbitrage opportunities.

### Strategy B: Market Making

- **`OrderDepth`** provides the current local order book.
- **`Order`** represents proposed buy and sell orders.
- The strategy determines suitable bid and ask prices based on market conditions and its chosen pricing rules.

### Strategy C: Momentum Trading

- **`TradingState`** provides the available market information.
- **`traderData`** can preserve historical information between invocations.
- The strategy can use available prices and maintained history to calculate indicators, such as moving averages and the Relative Strength Index (RSI).

All three strategies can reuse the same data structures when the required inputs are available and their outputs follow the simulator's expected interface.

## 3. What Needs to Change for Other Strategies?

The existing data model does not automatically calculate technical indicators or implement trading rules. These belong in the strategy code.

For example, a momentum strategy using RSI or moving averages would need to:

1. Obtain sufficient historical price data.
2. Calculate the required technical indicators.
3. Define entry and exit conditions.
4. Generate orders using the `Order` structure.
5. Manage positions and evaluate strategy performance.

If the strategy moves to another trading platform, additional changes may be necessary.

- **Market data:** Adapt how prices, order books, and observations are received.
- **Order execution:** Adapt how orders are represented and submitted.
- **Portfolio management:** Adapt how positions and trade records are accessed.
- **External observations:** Adapt how conversion prices, fees, and other market information are supplied.

## Conclusion

`datamodel.py` is a reusable foundation for strategies operating within a compatible trading environment. Its separation of data representation from trading logic makes it easier to develop, test, and modify different strategies without redesigning the underlying data structures each time.
