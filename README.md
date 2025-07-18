<h1 align="center">
  <img alt="react-native-pagseguro-plugpag" title="react-native-pagseguro-plugpag" style="margin-bottom: 16px" src=".github/images/react-native-pagseguro-plugpag-logo.png" />

React Native Pagseguro Plugpag

</h1>

[README PORTUGUESE-BR VERSION](README-PORTUGUESE-BR.md)

React Native Pagseguro Plugpag is a library aimed at integrating with the native library <a href="https://github.com/pagseguro/pagseguro-sdk-plugpagservicewrapper">PlugPagServiceWrapper</a>, maintained by <a href="https://github.com/pagseguro">Pagseguro</a>. The library's purpose is to integrate Android applications with smart terminals, such as Moderninha Smart (A930), Moderninha Smart 2 (P2), and other terminals provided by the company.

## 💻 Prerequisites

- NodeJS >= 18.0.0
- React Native >= 0.72
- Expo >= 47 (optional)

## 🚀 Installation

Installing with Yarn:

```sh
yarn add react-native-pagseguro-plugpag
```

Installing with npm:

```sh
npm install react-native-pagseguro-plugpag
```

### Configuration in React Native

Add this line to the file `/android/build.gradle`:

```
buildscript {
  dependencies {
    ...
    classpath 'com.google.gms:google-services:4.3.15'
  }
}
```

and add this dependency to the file `/android/app/build.gradle`:

```
dependencies {
    // ... other dependencies
    implementation 'br.com.uol.pagseguro.plugpagservice.wrapper:wrapper:1.30.51'
    ...
}
```

### Configuration in Expo

**_NOTE_**: The library does not support running on **_Expo Go_** due to its handling of libraries. The PlugPag Wrapper library is designed for use with Pagseguro's Android devices. Therefore, you need to use **_expo-dev-client_** to expose the android folder of your Expo project.

Add the react-native-pagseguro-plugpag plugin to `app.json` or `app.config.js`:

```
{
  "expo": {
    "plugins": [
      "react-native-pagseguro-plugpag"
    ]
  }
}
```

And to finalize, execute the prebuild step of expo to complete the configuration:

```
npx expo prebuild -p android --clean
```

## 📖 Usage

**_initializeAndActivatePinPad_**: initializes and activates the pin pad.

**_doPayment_**: performs communication and execution of financial transactions (debit card, credit card, voucher, and PIX).

**_refundPayment_**: performs refunds of financial transactions.

**_print_**: does customized printing from a JPEG/PNG file.

**_reprintCustomerReceipt_**: reprint customer receipt from the last transaction.

**_doAbort_**: abort transaction in progress.

**_readNFCCard_**: read NFC cards and return card UID.

**_useTransactionPaymentEvent_**: hook for native events related to financial transactions.

### Usage Examples

Example for activating a pin pad terminal.

**_NOTE_**: For development terminals, the code `403938` is commonly used. If it doesn't work, contact Pagseguro support.

```JS
import { initializeAndActivatePinPad } from 'react-native-pagseguro-plugpag';

async function handleInitializeAndActivatePinPad() {
  try {
    const data = await initializeAndActivatePinPad('403938');

    if (data.result !== 0) {
      Alert.alert('Error activating terminal', data.errorMessage);
      return;
    }

    Alert.alert('Terminal activated successfully!');
  } catch (error) {
    console.log(error);
    Alert.alert('Error activating terminal');
  }
}
```

Example for making credit card transactions with R$ 25.00:

```js
import { plugPag, doPayment } from 'react-native-pagseguro-plugpag';

async function handleDoPaymentCreditType() {
  try {
    const data = await doPayment({
      amount: 2500, // Amount to be paid in cents
      type: plugPag.paymentTypes.CREDIT, // Payment type option
      printReceipt: true, // Print or not from the establishment
      installments: 1, // Number of installments
      installmentType: plugPag.installmentTypes.BUYER_INSTALLMENT, // In case of installments, define whether the fee will be charged to the buyer or the seller
      userReference: 'test', // External code to identify the transaction in the future.
    });

    Alert.alert('Transaction completed successfully');
  } catch (error) {
    console.log(error);
    setIsModalVisible(false);

    Alert.alert('Error completing transaction');
  }
}
```

Example for making debit card transactions with R$ 25.00:

```js
import { plugPag, doPayment } from 'react-native-pagseguro-plugpag';

async function handleDoPaymentDebitType() {
  try {
    const data = await doPayment({
      amount: 2500, // Amount to be paid in cents
      type: plugPag.paymentTypes.DEBIT, // Payment type option
      printReceipt: true, // Print or not from the establishment
      installments: 1, // Number of installments
      installmentType: plugPag.installmentTypes.BUYER_INSTALLMENT, // In case of installments, define whether the fee will be charged to the buyer or the seller
      userReference: 'test', // External code to identify the transaction in the future.
    });

    Alert.alert('Transaction completed successfully');
  } catch (error) {
    console.log(error);
    setIsModalVisible(false);

    Alert.alert('Error completing transaction');
  }
}
```

Example for refunding a transaction:

```JS
async function handleRefundLastTransaction() {
  try {
    const response = await refundPayment({
      transactionCode: '123dwqwd5465sdas',
      transactionId: '78911qweqwdw7de44dd7qweqwed7d1qwe',
      printReceipt: true,
    });

    if (response.result !== 0) {
      Alert.alert('Refund', 'An error occurred while processing the refund');
      return;
    }

    Alert.alert('Refund completed successfully');
  } catch (error) {
    console.log(error);

    setIsModalVisible(false);
    Alert.alert('Refund', 'An error occurred while processing the refund');
  }
}
```

Example for reprinting customer receipt:

```JS
import { reprintCustomerReceipt } from 'react-native-pagseguro-plugpag';

async function handleReprintCustomerReceipt() {
  try {
    await reprintCustomerReceipt();
    Alert.alert('Receipt reprinted successfully');
  } catch (error) {
    console.log(error);
    Alert.alert('Error reprinting receipt');
  }
}
```

Example for aborting transaction in progress:

```JS
import { doAbort } from 'react-native-pagseguro-plugpag';

async function handleAbortTransaction() {
  try {
    const response = await doAbort();

    if (response.result) {
      Alert.alert('Transaction aborted successfully');
    } else {
      Alert.alert('Could not abort transaction');
    }
  } catch (error) {
    console.log(error);
    Alert.alert('Error aborting transaction');
  }
}
```

Example for reading NFC card:

```JS
import { readNFCCard } from 'react-native-pagseguro-plugpag';

async function handleReadNFCCard() {
  try {
    const response = await readNFCCard();
    Alert.alert('NFC Card read', `UID: ${response.uid}`);
  } catch (error) {
    console.log(error);
    Alert.alert('Error reading NFC card');
  }
}
```

For more examples, see the demo app in this <a href="https://github.com/brunodsazevedo/pagseguro-plugpag-demo">repository</a>.

## Contributing

See the [contributing guide](CONTRIBUTING.md) to learn how to contribute to the repository and the development flow.

## License

[MIT](LICENSE)

---

Made with [create-react-native-library](https://github.com/callstack/react-native-builder-bob)
