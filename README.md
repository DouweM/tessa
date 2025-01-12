
# tessa – simple, hassle-free access to price information of financial assets 📉🤓📈

tessa is a Python library to help you **easily search asset identifiers** (e.g.,
tickers) and **retrieve price information** for assets from different sources such as
Yahoo or Coingecko. It takes care of the different APIs, caching, rate limiting, and
other hassles.

tessa provides a **Symbol class that encapsulates nicely the methods relevant for a
symbol**. tessa also provides functionality to **manage collections of symbols**, store
and load them, and extend their functionality.

Finally, tessa makes sure to be nice to the sites being accessed and tries to **prevent
users from being blocked by 429 rate limiting errors** by 1) caching results upon
retrieval and 2) keeping track of request timestamps and waiting appropriate amounts of
time if necessary.

[→ Check out the full documentation. 📖](https://ymyke.github.io/tessa/tessa.html)


## How to use

Here's a longer example that quickly shows all aspects of the library. Refer to
submodules [symbol](tessa/symbol.html), [search](tessa/search.html), and
[price](tessa/price.html) for more information.

- Imports:

```python
from tessa import Symbol, SymbolCollection, search
```

- Create a symbol for MSFT and access some functions:

```python
s1 = Symbol("MSFT")         # will use "yahoo" as the default source
s1.price_latest()           # get latest price
```

- Create another symbol from a bloomberg ticker as it is used by Yahoo Finance:

```python
s2 = Symbol("SREN.SW")
s2.price_point("2022-06-30")    # get price at specific point in time
```

- Create a symbol from the coingecko source with an id as it is used by coingecko:

```python
s3 = Symbol("bitcoin", source="coingecko")
s3.price_graph()            # show price graph
```

- Search for a more crypto ticker on coingecko:

```python
res = search("GAME")        # search and print search result summary
filtered = res.filter(source="coingecko")  # filter results
filtered.p()                # print summary of filtered results
filtered.buckets[0].symbols # review the best bucket in the filtered results
s4 = filtered.buckets[0].symbols[2]   # our symbol is the 3rd in that list
s4.price_history()          # get entire history
```

- Build a collection of several symbols and use the collection to retrieve symbols:

```python
sc = SymbolCollection([s1, s2, s3, s4])
sc.add(Symbol("AAPL"))      # add another one
sc.find_one("SREN").price_graph()
```

- Store and load a symbol collection:

```python
sc.save_yaml("my_symbols.yaml")
sc_new = SymbolCollection()
sc_new.load_yaml("my_symbols.yaml")
```

- Use a different currency preference:

```python
sc.find_one("game").price_latest()  # will return price in USD
Symbol.currency_preference = "CHF"
sc.find_one("game").price_latest()  # will return price in CHF
```

Note that `currency_preference` will only have an effect with sources that support it.
It is supported for Coingecko but not for Yahoo. So you should always verify the
effective currency you receive in the result.


## Data sources

tessa builds on [yfinance](https://pypi.org/project/yfinance/) and
[pycoingecko](https://github.com/man-c/pycoingecko) and offers **a simplified and
unified interface**. 

Why these two sources? Yahoo Finance (via yfinance) is fast and offers an extensive
database that also contains many non-US markets. Coingecko (via pycoingecko) offers
great access to crypto prices. While Yahoo Finance also offers crypto information,
pycoingecko has the advantage that you can have the prices quoted in many more currency
preferences (a function that is also exposed via tessa).

More sources can be added in the future. Let me know in the
[issues](https://github.com/ymyke/tessa/issues) of you have a particular request.


## Main submodules

- [symbol](tessa/symbol.html): working with symbols and symbol collections.
- [search](tessa/search.html): searching the different sources.
- [price](tessa/price.html): accessing price functions directly instead of via the
  `Symbol` class.
- [sources](tessa/sources.html): if you'd like to add additional sources to the library.


## How to install

`pip install tessa`


## Prerequisites

See `pyproject.toml`. Major prerequisites are the `yfinance` and `pycoingecko` packages
to access finance information as well as the `beautifulsoup4` package to do some
scraping for searching on Yahoo Finance.


## Repository

https://github.com/ymyke/tessa


## Future Work

This is an initial version. There are a number of ideas on how to extend. Please leave
your suggestions and comments in the [Issues
section](https://github.com/ymyke/tessa/issues).


## On terminology

I'm using symbol instead of ticker because a ticker is mainly used for stock on stock
markets, whereas tessa is inteded to be used for any kind of financial assets, e.g. also
crypto.


## Other noteworthy libraries

- [strela](https://github.com/ymyke/strela): A python package for financial alerts.
- [pypme](https://github.com/ymyke/pypme): A Python package for PME (Public Market
  Equivalent) calculation.


## On investpy as a data source

Tessa used to use the [investpy package](https://github.com/alvarobartt/investpy) as the
main source of information until mid 2022 until investing.com introduced Cloudflare,
which broke access by investpy. 😖 It is currently unclear if investpy will be available
again in the future. [You can follow the developments in issue
600.](https://github.com/alvarobartt/investpy/issues/600) The old tessa/investpy code is
still available in the [add-symbols-based-on-investpy
branch](https://github.com/ymyke/tessa/tree/add-symbols-based-on-investpy).
