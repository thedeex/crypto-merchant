## **Базовая конструкция**

Адрес тестового стенда API: [https://merchant.presale-deex.exchange/v1](https://merchant.presale-deex.exchange/v1)

* Все запросы передаются с заголовком: application/json 
*  Каждый запрос должен содержать заголовок с наименованием: HMAC:значение
* Все передаваемые параметры передаются в тело запроса, в формате JSON. (raw data)


### **Внимание, все цифровые значения цен,курсов, типизированы строкой, во избежании проблем с отображением и округлением.**


## Получение списка доступных монет и курсов. 

Метод позволяет получить все доступные для магазина криптовалюты и их обменный курс.

Используется для страницы оплаты: при выборе поставщика оплаты, предоставляется возможность просмотреть сколько будет стоить тот или иной товар в разных крипто-единицах.

**Запрос**


    метод: 	 POST
    путь:    	/getRates
    тело запроса: {key: {public_key}}

**Коды ошибок:**

### Ошибкой является любой ответ вне кода 200.

Коды ошибок получить возможно:
*   исходя из STATUS кода. ответ.
*   в теле ответа. например. {success:false, errCode: 1}

```
100 - 	**некорректна подпись.**
```

**Ответ**


```
{
  "success": true,
  "result": {
    "BTC": {
	rate_id: 1,
      "rate": "0.0000113",
      "name": "Bitcoin",
      "confirms": "4",
    },
    "LTC": {
	rate_id: 2,
      "rate": "0.023",
      "name": "Litecoin",
      "confirms": "4",
    },
    "ETH": {
	rate_id: 3,
      "rate": "0.023",
      "name": "Ethereum",
      "confirms": "4",
    },
….}
```


## **Бронь заявки (получение депозитного адреса)**

### Запрос используется для подтверждения заявки и получения депозитного адреса.

**Запрос**


    метод: 	 POST
    путь:    	/bindOrder
    тело запроса: {key: {public_key},crypto_currency: {currency}, shop_amount: {amount_ruble}, shop_currency: {shop_currencty}, rate_id:{rate_id} }

**Значения:**


    {shop_currency} - валюта в которой магазин принимает заказ. (рубль, usd,eur.)  (STRING)

    {amount_ruble} - сумма, товара в магазине, согласно цене магазина  (STRING)

     {currency} наименование криптовалюты в котором покупатель желает оплатить заказ. например ETH (STRING)

    {rate_id} - значение полученное с предыдущего шага. (INT)

**Ответ**

```
{success: true,
address: “0x000000000000000abcd”,
expire_after_s: 2000,
operation_id: 124}
```

**Значения:**

параметр **expire_after_s** передается в секундах. отвечает за время, как долго  ожидается оплата на адрес. Если оплата не будет выполнена в пределах это времени, заявка не будет выполнена, т.к будет являться просроченной и курс криптовалют может значительно отличаться.

**operation_id** номер операции, по которой можно отследить статус операции.

**Коды ошибок:**

### Ошибкой является любой ответ вне кода 200.

##### Коды ошибок получить возможно:

*   исходя из STATUS кода. ответ.
*   в теле ответа. например. **{success:false, errCode: 1}**

```
100 - 	**некорректна подпись.**
10 - 	**выбранный курс валют просрочен (последний запрос был более 5 минут назад). Повторите запрос и актуализируйте курс.**
11 - 	**Некорректный курс. Процент изменения последнего выше чем 10% за последнюю минуту**.
200 -	**Невозможно обработать запрос.  Неизвестная ошибка.**
```

## **Получение статуса операции**

**Запрос**


    **метод**: 	 POST
    **путь**:    	/getOrderStatus
    **тело запроса**: {operation_id: {operation_id}}



**Ответ**

```
{success: true,
status: 1}
```

**Значения:**


- status:
```
1 - 	**Ожидается оплата**
2 -	**Операция найдена в сети, ожидается подтверждение**
3 - 	**Операция просрочена**
4 -	**Операция завершена (в этом случае отправляется сообщение на Ваш IPN)**
```

**Коды ошибок:**

### Ошибкой является любой ответ вне кода 200.

#### Коды ошибок получить возможно:
*   исходя из HTTP STATUS кода. ответ.
*   в теле ответа. например. **{success:false, errCode: 1}**

```
100 - 	**некорректна подпись.**
10 - 	**выбранный курс валют просрочен (последний запрос был более 5 минут назад). Повторите запрос и актуализируйте курс.**
11 - 	**Некорректный курс. Процент изменения последнего выше чем 10% за последнюю минуту**.
200 -	**Невозможно обработать запрос.  Неизвестная ошибка.**
```
