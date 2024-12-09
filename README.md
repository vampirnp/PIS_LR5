# PIS_LR_5

### Техническое задание на разработку. Бэкенд

## 1. Входные параметры

| Параметр       | Тип параметра | Обязательность | Описание                                |
|----------------|---------------|----------------|-----------------------------------------|
| userId         | uuid          | +              | Идентификатор сущности                  |
| productId      | uuid          | +              | Идентификатор продукта                  |

## 2. Авторизация

a. По бизнесу
Доступ к функции добавления товара в корзину
Просмотр и управление корзиной доступно только владельцу 
Доступ к информации о остатке товара предоставляется всем пользователям

b. Техническая реализация
-- Проверка прав доступа к корзине
SELECT id FROM tbl_User
WHERE user_id = {current_user} 
AND id = {orderId}

-- Проверка статуса
SELECT status FROM tbl_product 
WHERE id = {product_Id} 
AND status = 'ACTIVE'

## 3. Передача данных при инициализации

```json
{
  "productInfo": {
    "product_name": "Проводные наушники HyperX Cloud II KHX-HSCP-RD красный",
    "article": "12h12322",
    "rating": 4.6,
    "price": 13272
  },

  "clientInfo": {
    "firstName": "Орлов",
    "lastName": "Александр",
    "middleName": "Кириллович"
  },

  "contactInfo": {
    "email": "example@mail.ru",
    "phone": "+7 777 777 02 03"
  },

  "deliveryOfGoods": {
    "inStore": {
      "included": true
    },
    "deliveryToAddress": {
      "included": false,
      "address": "г.Клин Ул.полевая д.45",
      "price": 2000
    }
  },

  "paymentMethod": {
    "SBP": {
      "included": false
    },
    "SBERPay": {
      "included": false
    },
    "card": {
      "included": false
    },
    "cashOnDelivery": {
      "included": true
    }
  }
}
```

## 4. Таблица атрибутов

| Атрибут, уровень 1 | Уровень 2 | Тип     | Название атрибута       | Формирование на бэкенде                                                                | Обязательность |
|--------------------|------------|---------|-------------------------|---------------------------------------------------------------------------------------|----------------|
| productId          |            | object  | индетификатор продукта  | select tbl_pruduct_id from tbl_product where id =<product_Id>                         | +              |
| product_name       |            | string  | Имя продукта            | select tbl_product_name from tbl_product where product_name=<str>                     | +              |
| article            |            | integer | Артикул                 | SELECT article FROM tbl_product WHERE product_id = <product_id>                       | +              |
| price              |            | integer | Цена                    | SELECT price FROM tbl_product WHERE product_id = <product_id>	                        | +              |
| clientInfo         |            | object  | Информация клиента      |                                                                                       | +              |
| firstName          |            | string  | Имя                     | SELECT first_name FROM tbl_users WHERE user_id = <user_id>                            | +              |
| lastName           |            | string  | Фамилия                 | SELECT last_name FROM tbl_users WHERE user_id = <user_id>                             | +              |
| middleName         |            | string  | Отчество                | SELECT middle_name FROM tbl_users WHERE user_id = <user_id>                           | -              |
| contactInfo        |            | object  | Контактная информация   | -                                                                                     | +              |
| email              |            | string  | Электронная почта       | SELECT email FROM tbl_users WHERE user_id = <user_id>                                 | +              |
| phone              |            | string  | Телефон                 | SELECT phone FROM tbl_users WHERE user_id = <user_id>                                 | +              |
| deliveryOfGoods    |            | object  | Доставка товаров        |                                                                                       | +              |
| inStore            |            | boolean | В магазине              | SELECT in_store FROM tbl_delivery WHERE order_id = <order_id>                         | +              |
| deliveryToAddress  |            | object  | Доставка по адресу      | -                                                                                     | +              |
| address            |            | string  | Адрес                   | SELECT address FROM tbl_delivery WHERE order_id = <order_id>                          | +              |
| price              |            | integer | Цена                    | SELECT delivery_price FROM tbl_delivery WHERE order_id = <order_id>                   | +              |
| paymentMethod      |            | object  | Способ оплаты           | -                                                                                     | +              |
| SBP                |            | boolean | Система быстрых платежей| SELECT SBP FROM tbl_payment_methods WHERE order_id = <order_id>                       | +              |
| SBERPay            |            | boolean | СберПэй                 | SELECT SBERPay FROM tbl_payment_methods WHERE order_id = <order_id>                   | +              |
| card               |            | boolean | Оплата картой           | SELECT card FROM tbl_payment_methods WHERE order_id = <order_id>	                    | +              |
| cashOnDelivery     |            | boolean | Оплата наличными        | SELECT cash_on_delivery FROM tbl_payment_methods WHERE order_id = <order_id>          | +              |




## 6.  REST-запросы

| Название          | URL          | Тип метода | Проверка авторизации       | Действия на бэкенде                      | Query, path parameters              | Body          | Responses  |
|-------------------|--------------|------------|----------------------------|------------------------------------------|-------------------------------------|---------------|--------------------------------------------|
| Внесение данных   | /api/orders  | POST       | Базовая авторизация        | Создание нового заказа с данными из JSON | id: Uuid, обязательный, ИД сущности | JSON с данными| 200OK:{"message":"Заказ успешно создан"} <br> 400 Bad Request: {"message": "Неверные данные"}|
|                   |              |            |                            |                                          |                                     |               |                                            |
| Оплата            | /api/orders{id}/payment   |POST  | Базовая авторизация | Проверка данных и проведение оплаты      | id: Uuid, обязательный, ИД сущности |JSON с данными оплаты | 200OK:{"message":"Оплата успешно проведена"} <br> 404 Not Found: {"message":"Заказ не найден"}| 
|                   |              |            |                            |                                          |                                     |               |                                            |
|  Удаление заказа  |/api/orders/{id}| DELETE   | Базовая авторизация        | Удаление заказа по ИД                    | id: Uuid, обязательный, ИД сущности |      -        | 200OK:{"message": "Заказ успешно удален" <br> 404 Not Found: {"message":"Заказ не не найден"}|
|                   |              |            |                            |                                          |                                     |               |                                            |


## 7.  Пример JSON для создания заказа

```json
{
  "productInfo": {
    "product_name": "Проводные наушники HyperX Cloud II KHX-HSCP-RD красный",
    "article": "12h12322",
    "rating": 4.6,
    "price": 13272
  },

  "clientInfo": {
    "firstName": "Орлов",
    "lastName": "Александр",
    "middleName": "Кириллович"
  },

  "contactInfo": {
    "email": "example@mail.ru",
    "phone": "+7 777 777 02 03"
  },

  "deliveryOfGoods": {
    "inStore": {
      "included": true
    },
    "deliveryToAddress": {
      "included": false,
      "address": "г.Клин Ул.полевая д.45",
      "price": 2000
    }
  },

  "paymentMethod": {
    "SBP": {
      "included": false
    },
    "SBERPay": {
      "included": false
    },
    "card": {
      "included": false
    },
    "cashOnDelivery": {
      "included": true
    }
  }
}
```

## 8.  Пример JSON для оплаты заказа

```json
{
  "paymentMethod": {
    "SBP": {
      "included": false
    },
    "SBERPay": {
      "included": false
    },
    "card": {
      "included": true,
      "cardNumber": "1234567890123456",
      "cardHolderName": "Иван Иванов",
      "expiryDate": "12/23",
      "cvv": "123"
    },
    "cashOnDelivery": {
      "included": false
    }
  }
}
```


