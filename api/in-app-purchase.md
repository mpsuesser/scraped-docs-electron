---
url: https://www.electronjs.org/docs/latest/api/in-app-purchase
title: "In App Purchase"
description: ""
access_date: 2026-08-07T16:36:10.660Z
current_date: 2026-08-07T16:36:10.660Z
---

History

| Version(s) | Changes |
| --- | --- |
| ```markdown None ``` | ```markdown API ADDED ``` |

> In-app purchases on Mac App Store.

Process: [Main](../glossary.md#main-process)

## Events

The `inAppPurchase` module emits the following events:

### Event: 'transactions-updated'

History

| Version(s) | Changes |
| --- | --- |
| ```markdown None ``` | ```markdown API ADDED ``` |

Returns:

- `event` Event
- `transactions` Transaction\[\] - Array of [Transaction](structures/transaction.md) objects.

Emitted when one or more transactions have been updated.

## Methods

The `inAppPurchase` module has the following methods:

### inAppPurchase.purchaseProduct(productID\[, opts\])

History

| Version(s) | Changes |
| --- | --- |
| ```markdown None ``` | [  This method now returns a Promise instead of using a callback function.  ](../breaking-changes.md#api-changed-callback-based-versions-of-promisified-apis) |
| ```markdown None ``` | Added `username` option to `opts` parameter. |
| ```markdown None ``` | ```markdown API ADDED ``` |

- `productID` string
- `opts` Integer | Object (optional) - If specified as an integer, defines the quantity.
	- `quantity` Integer (optional) - The number of items the user wants to purchase.
		- `username` string (optional) - The string that associates the transaction with a user account on your service (applicationUsername).

Returns `Promise<boolean>` - Returns `true` if the product is valid and added to the payment queue.

You should listen for the `transactions-updated` event as soon as possible and certainly before you call `purchaseProduct`.

### inAppPurchase.getProducts(productIDs)

History

| Version(s) | Changes |
| --- | --- |
| ```markdown None ``` | [  This method now returns a Promise instead of using a callback function.  ](../breaking-changes.md#api-changed-callback-based-versions-of-promisified-apis) |
| ```markdown None ``` | ```markdown API ADDED ``` |

- `productIDs` string\[\] - The identifiers of the products to get.

Returns `Promise<Product[]>` - Resolves with an array of [Product](structures/product.md) objects.

Retrieves the product descriptions.

### inAppPurchase.canMakePayments()

History

| Version(s) | Changes |
| --- | --- |
| ```markdown None ``` | ```markdown API ADDED ``` |

Returns `boolean` - whether a user can make a payment.

### inAppPurchase.restoreCompletedTransactions()

History

| Version(s) | Changes |
| --- | --- |
| ```markdown None ``` | ```markdown API ADDED ``` |

Restores finished transactions. This method can be called either to install purchases on additional devices, or to restore purchases for an application that the user deleted and reinstalled.

[The payment queue](https://developer.apple.com/documentation/storekit/skpaymentqueue?language=objc) delivers a new transaction for each previously completed transaction that can be restored. Each transaction includes a copy of the original transaction.

### inAppPurchase.getReceiptURL()

History

| Version(s) | Changes |
| --- | --- |
| ```markdown None ``` | ```markdown API ADDED ``` |

Returns `string` - the path to the receipt.

### inAppPurchase.finishAllTransactions()

History

| Version(s) | Changes |
| --- | --- |
| ```markdown None ``` | ```markdown API ADDED ``` |

Completes all pending transactions.

### inAppPurchase.finishTransactionByDate(date)

History

| Version(s) | Changes |
| --- | --- |
| ```markdown None ``` | ```markdown API ADDED ``` |

- `date` string - The ISO formatted date of the transaction to finish.

Completes the pending transactions corresponding to the date.
