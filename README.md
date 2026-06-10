# @exchangerateapi/sdk

[![Powered by Exchange-RateAPI](https://img.shields.io/badge/Powered%20by-Exchange--RateAPI-blueviolet.svg)](https://exchange-rateapi.com)

[![npm version](https://img.shields.io/npm/v/@exchangerateapi/sdk.svg)](https://www.npmjs.com/package/@exchangerateapi/sdk)
[![license](https://img.shields.io/npm/l/@exchangerateapi/sdk.svg)](https://github.com/Exchange-RateAPI/exchange-rateapi-js/blob/main/LICENSE)
[![TypeScript](https://img.shields.io/badge/TypeScript-first--class-blue.svg)](https://www.npmjs.com/package/@exchangerateapi/sdk)
[![zero dependencies](https://img.shields.io/badge/dependencies-0-brightgreen.svg)](https://www.npmjs.com/package/@exchangerateapi/sdk)
[![npm downloads](https://img.shields.io/npm/dm/@exchangerateapi/sdk.svg)](https://www.npmjs.com/package/@exchangerateapi/sdk)

**The fastest way to access real-time mid-market exchange rates in JavaScript/TypeScript**

Official JavaScript/TypeScript SDK for [Exchange Rate API](https://exchange-rateapi.com). Access real-time and historical mid-market exchange rates for 160+ currencies. Zero dependencies.

## Why Choose This SDK?

- **Zero Dependencies** -- Uses only the built-in `fetch` API. Nothing to audit, nothing to break.
- **TypeScript-First** -- Full type definitions and IDE autocomplete out of the box.
- **ESM + CommonJS** -- Works everywhere: Node.js, Bun, Deno, and bundlers. Ships both ESM and CJS builds.
- **Real-Time Data** -- Rates updated every 60 seconds from Reuters (Refinitiv) and interbank feeds.
- **Mid-Market Rates** -- The true interbank rate -- no hidden spread or markup.
- **160+ Currencies** -- Major, minor, and exotic currency pairs.

## Installation

```bash
npm install @exchangerateapi/sdk
```

## Quick Start

```ts
import { ExchangeRateAPI } from '@exchangerateapi/sdk';

const client = new ExchangeRateAPI({ apiKey: 'era_live_...' });

// Get latest rates
const { rates } = await client.latest({ base: 'USD', symbols: ['EUR', 'GBP', 'JPY'] });
console.log(rates); // { EUR: 0.92, GBP: 0.78, JPY: 149.5 }

// Convert currencies
const result = await client.convert('USD', 'EUR', 1000);
console.log(`$1,000 = EUR ${result.result}`);
```

## Authentication

All API requests require a Bearer token. Get your free API key at [exchange-rateapi.com/register](https://exchange-rateapi.com/register).

```ts
const client = new ExchangeRateAPI({ apiKey: 'era_live_your_key_here' });
```

The SDK sends the key as a `Bearer` token in the `Authorization` header automatically.

You can also override the API key on a per-request basis:

```ts
const data = await client.latest({ apiKey: 'era_live_other_key' });
```

## Configuration

```ts
const client = new ExchangeRateAPI({
  apiKey: 'era_live_...',       // Required. Your API key.
  baseUrl: 'https://exchange-rateapi.com', // Optional. Default shown.
  timeout: 10000,               // Optional. Request timeout in ms. Default: 10000.
});
```

## API Methods

### `latest(options?)`

Get the latest exchange rates.

```ts
// All rates from USD
const data = await client.latest();

// Specific symbols with EUR base
const data = await client.latest({ base: 'EUR', symbols: ['USD', 'GBP', 'JPY'] });

console.log(data.base);  // 'EUR'
console.log(data.date);  // '2026-05-10T12:00:00Z'
console.log(data.rates); // { USD: 1.08, GBP: 0.85, JPY: 162.3 }
```

### `forDate(date, options?)`

Get exchange rates for a specific historical date.

```ts
// Using a string
const data = await client.forDate('2026-01-15');

// Using a Date object
const data = await client.forDate(new Date('2026-01-15'));

// With options
const data = await client.forDate('2026-01-15', {
  base: 'EUR',
  symbols: ['USD', 'GBP'],
});
```

### `timeSeries(startDate, endDate, options?)`

Get exchange rate time series between two dates.

```ts
const data = await client.timeSeries('2026-01-01', '2026-03-31', {
  base: 'USD',
  symbols: ['EUR', 'GBP'],
});

// Access rates by date
console.log(data.rates['2026-01-15']); // { EUR: 0.92, GBP: 0.78 }
```

### `convert(from, to, amount, options?)`

Convert an amount between two currencies.

```ts
// Current rate
const result = await client.convert('USD', 'EUR', 1000);
console.log(`$1,000 = EUR ${result.result}`);

// Historical conversion
const result = await client.convert('USD', 'EUR', 1000, {
  date: '2026-01-15',
});
console.log(`Rate on Jan 15: ${result.rate}`);
```

### `getRate(from, to, amount?)`

Get exchange rate between two currencies.

```ts
const rate = await client.getRate('USD', 'EUR');
console.log(`1 USD = ${rate.rate} EUR`);

// With amount
const rate = await client.getRate('USD', 'EUR', 1000);
console.log(`$1,000 = EUR ${rate.to.amount}`);
```

### `getRates(source, target)`

Get authenticated exchange rates with metadata.

```ts
const rates = await client.getRates('USD', 'EUR');
rates.forEach(r => console.log(`${r.source}/${r.target}: ${r.rate} at ${r.time}`));
```

### `getHistoricalRates(source, target, period?)`

Get historical rates by period. Supported periods: `1d`, `7d`, `30d`, `1y`.

```ts
const history = await client.getHistoricalRates('USD', 'EUR', '30d');
history.rates.forEach(point => {
  console.log(`${point.time}: ${point.rate}`);
});
```

### `symbols()`

Get all supported currency symbols.

```ts
const { symbols } = await client.symbols();
console.log(symbols);
// { USD: 'United States Dollar', EUR: 'Euro', GBP: 'British Pound', ... }

// Build a currency dropdown
const options = Object.entries(symbols).map(([code, name]) => ({ code, name }));
```

## Error Handling

```ts
import { ExchangeRateAPI, ExchangeRateAPIError } from '@exchangerateapi/sdk';

const client = new ExchangeRateAPI({ apiKey: 'era_live_...' });

try {
  const data = await client.latest();
} catch (err) {
  if (err instanceof ExchangeRateAPIError) {
    console.error(`API error: ${err.message} (status: ${err.status})`);
  }
}
```

## CommonJS Usage

```js
const { ExchangeRateAPI } = require('@exchangerateapi/sdk');

const client = new ExchangeRateAPI({ apiKey: 'era_live_...' });
```

## Links

- [API Documentation](https://exchange-rateapi.com/developers)
- [Register (Free)](https://exchange-rateapi.com/register)
- [Dashboard](https://exchange-rateapi.com/profile)
- [Status](https://exchange-rateapi.com/status)
- [GitHub](https://github.com/Exchange-RateAPI/exchange-rateapi-js)

## License

MIT - see [LICENSE](./LICENSE) for details.
