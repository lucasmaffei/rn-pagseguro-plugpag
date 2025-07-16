<h1 align="center">
  <img alt="react-native-pagseguro-plugpag" title="react-native-pagseguro-plugpag" style="margin-bottom: 16px" src=".github/images/react-native-pagseguro-plugpag-logo.png" />

React Native Pagseguro Plugpag

</h1>

[README EM VERSÃO INGLÊS](README.md)

React Native Pagseguro Plugpag é uma biblioteca com o intuito de integrar com a biblioteca nativa <a href="https://github.com/pagseguro/pagseguro-sdk-plugpagservicewrapper">PlugPagServiceWrapper</a>, mantida pela <a href="https://github.com/pagseguro">Pagseguro</a>, que possui o intuito de integrar aplicativos Android com terminais smart, como Moderninha Smart (A930), Moderninha Smart 2 (P2), entre outros terminais disponibilizados pela empresa.

## 💻 Pré-requisitos

- NodeJS >= 18.0.0
- React Native >= 0.72
- Expo >= 47 (opcional)

## 🚀 Instalando

Instalando com Yarn:

```sh
yarn add react-native-pagseguro-plugpag
```

Instalando com npm:

```sh
npm install react-native-pagseguro-plugpag
```

### Configuração em React Native

Adicione essa linha no arquivo `/android/build.gradle`

```
buildscript {
  dependencies {
    ...
    classpath 'com.google.gms:google-services:4.3.15'
  }
}
```

e adicione essa dependência no arquivo `/android/app/build.gradle`:

```
dependencies {
    // ... other dependencies
    implementation 'br.com.uol.pagseguro.plugpagservice.wrapper:wrapper:1.30.51'
    ...
}
```

### Configuração em Expo

**_OBS_**: A biblioteca não suporta rodar em **_Expo Go_** por lidar com bibliotecas, já que a biblioteca PlugPag Wrapper foi projetado para uso de device Android da Pagseguro. Com isso, é preciso utilizar **_expo-dev-client_** para expor a pasta android de seu projeto expo.

Adicione em `app.json` ou `app.config.js` o plugin da react-native-pagseguro-plugpag:

```
{
  "expo": {
    "plugins": [
      "react-native-pagseguro-plugpag"
    ]
  }
}
```

E para concluir, execute o pre build do expo para concluir configuração:

```
npx expo prebuild -p android --clean
```

## 📖 Uso

**_initializeAndActivatePinPad_**: inicializa e ativa pin pad.

**_doPayment_**: efetua comunicação e execução de transações financeiras (cartão de débito, cartão de crédito, voucher e PIX).

**_refundPayment_**: efetuar estorno de transações financeiras.

**_print_**: faz impressões personalizadas a partir de um arquivo JPEG/PNG.

**_reprintCustomerReceipt_**: reimprimir comprovante do cliente da última transação.

**_doAbort_**: abortar transação em andamento.

**_readNFCCard_**: ler cartões NFC e retornar UID do cartão.

**_useTransactionPaymentEvent_**: hook de eventos nativos em relação as transações financeiras

### Exemplos de uso

Exemplo para ativação de terminal de pin pad.

**_OBS_**: Para terminais de desenvolvimento, comumente utiliza-se o código `403938`. Caso não funcione, contate o suporte da Pagseguro.

```JS
import { initializeAndActivatePinPad } from 'react-native-pagseguro-plugpag';

handleInitializeAndActivatePinPad() {
  try {
    const data = await initializeAndActivatePinPad('403938');

    if (data.result !== 0) {
      Alert.alert('Erro ao ativar terminal', data.errorMessage);
      return;
    }

    Alert.alert('Terminal ativado com sucesso!');
  } catch (error) {
    console.log(error);
    Alert.alert('Erro ao ativar terminal');
  }
}
```

Exemplo para efetuar transações com cartão de crédito com R$ 25,00:

```js
import { plugPag, doPayment } from 'react-native-pagseguro-plugpag';

handleDoPaymentCreditType() {
  try {
    const data: PaymentTransactionResponseProps = await doPayment({
      amount: 2500, // Valor a ser pago em centavos
      type: plugPag.paymentTypes.CREDIT, // Opção de tipo de pagamento
      printReceipt: true, // Imprime ou não via do estabelecimento
      installments: 1, // Números de parcelas
      installmentType: plugPag.installmentTypes.BUYER_INSTALLMENT, // Nos casos de parcelamentos, define se a taxa será cobrada pelo comprador ou pelo vendedor
      userReference: 'test', // Código externo caso queira identificar transação no futuro.
    });

    Alert.alert('Transação concluída com sucesso');
  } catch (error) {
    console.log(error);
    setIsModalVisible(false);

    Alert.alert('Erro ao concluir transação');
  }
}
```

Exemplo para efetuar transações com cartão de débito com R$ 25,00:

```js
import { plugPag, doPayment } from 'react-native-pagseguro-plugpag';

handleDoPaymentDebitType() {
  try {
    const data = await doPayment({
      amount: 2500, // Valor a ser pago em centavos
      type: plugPag.paymentTypes.DEBIT, // Opção de tipo de pagamento
      printReceipt: true, // Imprime ou não via do estabelecimento.
      installments: 1, // Números de parcelas
      installmentType: plugPag.installmentTypes.BUYER_INSTALLMENT, // Nos casos de parcelamentos, define se a taxa será cobrada pelo comprador ou pelo vendedor
      userReference: 'test', // Código externo caso queira identificar transação no futuro.
    });

    Alert.alert('Transação concluída com sucesso');
  } catch (error) {
    console.log(error);
    setIsModalVisible(false);

    Alert.alert('Erro ao concluir transação');
  }
}
```

Exemplo para efetuar estorno de transação:

```JS
handleRefundLastTransaction() {
  try {
    const response = await refundPayment({
      transactionCode: 123dwqwd5465sdas,
      transactionId: 78911qweqwdw7de44dd7qweqwed7d1qwe,
      printReceipt: true,
    });

    if (response.result !== 0) {
      Alert.alert('Estorno', 'Ocorreu um erro ao efetuar estorno');
      return;
    }

    Alert.alert('Estorno efetuado com sucesso');
  } catch (error) {
    console.log(error);

    setIsModalVisible(false);
    Alert.alert('Estorno', 'Ocorreu um erro ao efetuar estorno');
  }
}
```

Exemplo para reimprimir comprovante do cliente:

```JS
import { reprintCustomerReceipt } from 'react-native-pagseguro-plugpag';

handleReprintCustomerReceipt() {
  try {
    await reprintCustomerReceipt();
    Alert.alert('Comprovante reimpresso com sucesso');
  } catch (error) {
    console.log(error);
    Alert.alert('Erro ao reimprimir comprovante');
  }
}
```

Exemplo para abortar transação em andamento:

```JS
import { doAbort } from 'react-native-pagseguro-plugpag';

handleAbortTransaction() {
  try {
    const response = await doAbort();

    if (response.result) {
      Alert.alert('Transação abortada com sucesso');
    } else {
      Alert.alert('Não foi possível abortar a transação');
    }
  } catch (error) {
    console.log(error);
    Alert.alert('Erro ao abortar transação');
  }
}
```

Exemplo para ler cartão NFC:

```JS
import { readNFCCard } from 'react-native-pagseguro-plugpag';

handleReadNFCCard() {
  try {
    const response = await readNFCCard();
    Alert.alert('Cartão NFC lido', `UID: ${response.uid}`);
  } catch (error) {
    console.log(error);
    Alert.alert('Erro ao ler cartão NFC');
  }
}
```

Para mais exemplos, veja o app demo nesse <a href="https://github.com/brunodsazevedo/pagseguro-plugpag-demo">repositório</a>

## Contributing

Veja [contributing guide](CONTRIBUTING.md) para aprender como contribuir para o repositório e o fluxo de desenvolvimento.

## License

[MIT](LICENSE)

---

Feito com [create-react-native-library](https://github.com/callstack/react-native-builder-bob)
