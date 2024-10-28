### Техническое задание на разработку. Бэкенд

### Входные параметры

| Параметр       | Тип параметра | Обязательность | Описание                                |
|----------------|---------------|----------------|-----------------------------------------|
| entityId       | uuid          | +/-            | Идентификатор сущности|

### Авторизация

**Клиент:**
- Данную сущность может просмотреть только автор сущности.

### Техническая реализация

**Проверка прав доступа:**
- Для данного `entityId`, `tbl_entity.tbl_user_id_author` должно быть равно текущему авторизованному ID пользователя в системе.

### Передача данных при инициализации

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


Таблица атрибутов
Атрибут, уровень 1	Уровень 2…	Тип	Название атрибута	Формирование на бэкенде	Обязательность
productInfo		object	Информация о продукте		
product_name	string	Имя продукта		
article	string	Артикул		
rating	float	Рейтинг		
price	integer	Цена		
clientInfo		object	Информация клиента		
firstName	string	Имя		
lastName	string	Фамилия		
middleName	string	Отчество		
contactInfo		object	Контактная информация		
email	string	Электронная почта		
phone	string	Телефон		
deliveryOfGoods		object	Доставка товаров		
inStore	boolean	В магазине		
deliveryToAddress	object	Доставка по адресу		
address	Адрес		
price	Цена		
paymentMethod		object	Способ оплаты		
SBP	boolean	Система быстрых платежей		
SBERPay	boolean	СберПэй		
card	boolean	Оплата картой		
cashOnDelivery	boolean	Оплата наличными		

### REST-запросы
REST-запросы
Название	URL	Тип метода	Проверка авторизации	Действия на бэкенде	Query, path parameters	Body	Responses
Внесение данных	/api/orders	POST	Базовая авторизация	Создание нового заказа с данными из JSON	id: Uuid, обязательный, ИД сущности	JSON с данными	200 OK: {“message”: “Заказ успешно создан”} 400 Bad Request: {“message”: “Неверные данные”}
Оплата	/api/orders/{id}/payment	POST	Базовая авторизация	Проверка данных и проведение оплаты	id: Uuid, обязательный, ИД сущности	JSON с данными оплаты	200 OK: {“message”: “Оплата успешно проведена”} 404 Not Found: {“message”: “Заказ не найден”}
Удаление заказа	/api/orders/{id}	DELETE	Базовая авторизация	Удаление заказа по ИД	id: Uuid, обязательный, ИД сущности	-	200 OK: {“message”: “Заказ успешно удален”} 404 Not Found: {“message”: “Заказ не найден”}

