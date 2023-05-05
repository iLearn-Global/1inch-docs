---
sidebar_label: SDK Overview
sidebar_position: 1
title: SDK Overview
---

# Fusion SDK Overview

The Fusion SDK provides high-level functionality for working with 1inch's Fusion Mode. This guide presents an overview of the Fusion SDK, its methods, types, and real-world examples to help you get started.

## SDK Overview

### Real World Example

Here's an example of how you can use the Fusion SDK to fetch active orders:

```typescript
import { FusionSDK, NetworkEnum } from '@1inch/fusion-sdk';

async function main() {
  const sdk = new FusionSDK({
    url: 'https://fusion.1inch.io',
    network: NetworkEnum.ETHEREUM,
  });

  const orders = await sdk.getActiveOrders({ page: 1, limit: 2 });
}

main();
```

## Creation

You can create a new instance of the Fusion SDK by providing the required parameters:

```typescript
interface FusionSDKConfigParams {
  url: string;
  network: NetworkEnum;
  blockchainProvider?: BlockchainProviderConnector;
  httpProvider?: HttpProviderConnector; // by default, we are using axios
}
```

### Example with Custom HttpProvider

```typescript
import { api } from 'my-api-lib';

class CustomHttpProvider implements HttpProviderConnector {
  get<T>(url: string): Promise<T> {
    return api.get(url);
  }

  post<T>(url: string, data: unknown): Promise<T> {
    return api.post(url, data);
  }
}
```

## Methods

### getActiveOrders

Fetch the list of active orders with optional pagination.

**Arguments:**

- [0] PaginationParams

**Example:**

```typescript
import { FusionSDK, NetworkEnum } from '@1inch/fusion-sdk';
const sdk = new FusionSDK({
  url: 'https://fusion.1inch.io',
  network: NetworkEnum.ETHEREUM,
});
const orders = await sdk.getActiveOrders({ page: 1, limit: 2 });
```

### getOrdersByMaker

Fetch orders for a specific maker address with optional pagination.

**Arguments:**

- [0] params: PaginationParams & { address: string }

**Example:**

```typescript
import { FusionSDK, NetworkEnum } from '@1inch/fusion-sdk';
const sdk = new FusionSDK({
  url: 'https://fusion.1inch.io',
  network: NetworkEnum.ETHEREUM,
});

const orders = await sdk.getOrdersByMaker({
  page: 1,
  limit: 2,
  address: '0xfa80cd9b3becc0b4403b0f421384724f2810775f',
});
```

### getQuote

Get quote details for a token swap based on the input data.

**Arguments:**

- [0] params: QuoteParams

**Example:**

```typescript
import { FusionSDK, NetworkEnum, QuoteParams } from '@1inch/fusion-sdk';
const sdk = new FusionSDK({
  url: 'https://fusion.1inch.io',
  network: NetworkEnum.ETHEREUM,
});

const params = {
  fromTokenAddress: '0x6b175474e89094c44da98b954eedeac495271d0f', // DAI
  toTokenAddress: '0xc02aaa39b223fe8d0a0e5c4f27ead9083c756cc2', // WETH
  amount: '1000000000000000000000', // 1000 DAI
};

const quote = await sdk.getQuote(params);
```

### placeOrder

Place an order in Fusion Mode.

**Arguments:**

- [0] params: OrderParams

**Example:**

```typescript
const makerPrivateKey = '0x123....';
const makerAddress = '0x123....';

const nodeUrl = '....';

const blockchainProvider = new PrivateKeyProviderConnector(
  makerPrivateKey,
  new Web3(nodeUrl)
);

const sdk = new FusionSDK({
  url: 'https://fusion.1inch.io',
  network: 1,
  blockchainProvider,
});

sdk.placeOrder({
  fromTokenAddress: '0xc02aaa39b223fe8d0a0e5c4f27ead9083c756cc2', // WETH
  toTokenAddress: '0xa0b86991c6218b36c1d19d4a2e9eb0ce3606eb48', // USDC
  amount: '50000000000000000', // 0.05 ETH
  walletAddress: makerAddress,
  // fee is an optional field
  fee: {
    takingFeeBps: 100, // 1% as we use bps format, 1% is equal to 100bps
    takingFeeReceiver: '0x0000000000000000000000000000000000000000', // fee receiver address
  },
}).then(console.log);
```

## Types

### PaginationParams

```typescript
type PaginationParams = {
  page?: number; // default is 1
  limit?: number; // default is 2, min is 1, max is 500
};
```

### QuoteParams

```typescript
type QuoteParams = {
  fromTokenAddress: string;
  toTokenAddress: string;
  amount: string;
  permit?: string; // a permit (EIP-2612) call data, user approval sign
  takingFeeBps?: number; // 100 == 1%
};
```

### OrderParams

```typescript
enum PresetEnum {
  fast = 'fast',
  medium = 'medium',
  slow = 'slow',
}

type OrderParams = {
  fromTokenAddress: string;
  toTokenAddress: string;
  amount: string;
  walletAddress: string;
  permit?: string; // a permit (EIP-2612) call data, user approval sign
  receiver?: string; // address
  preset?: PresetEnum;
  nonce?: OrderNonce | string | number; // allows to batch cancel orders. by default: not used
  fee?: TakingFeeInfo;
};

export type TakingFeeInfo = {
  takingFeeBps: number; // 100 == 1%
  takingFeeReceiver: string;
};
```

With this improved documentation, you should have a better understanding of the Fusion SDK and how to use it in your projects. Remember to refer to the official [1inch Fusion SDK repository](https://github.com/1inch/fusion-sdk) for the most up-to-date information and examples.
