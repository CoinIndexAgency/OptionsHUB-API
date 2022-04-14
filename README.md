# Public API

OptionsHUB, v1. Powered by CoinIndex, Peanut Trade and Remme team. 

## Changelog

-  v0.6.2
	-  [PublicAPI] Исправлен механизм подписки на hedge streaming если используеться параметр only_exchange
	-  [PublicAPI] Если указана одна биржа, спот-прайс береться именно ее, вместо композитного
-  **v0.6.1 from 14.10.2021**
	-  История стейтов хедж-движка сохраняеться теперь в RocksDB (минутные снапшоты)
	-  Новая зависимость: rocksdb (https://github.com/Level/rocksdb)
	-  Калькулятор теперь может считать хедж для amount от 1 USD (/deliver/hedge)
	-  Если позиция менее 1-го контракта, он всегда округляеться вверх до 1.
	-  Добавлен эндпоинт /metrics с экспортором метрик в формате Prometheus
	-  Удалена зависимость hedge-сервиса от Redis.
-  **v0.6.0 from 01.10.2021**
	-  Исправление выпадения в фатальную ошибку при запросе некорректного execId для проверки хедж-плана
	-  Интервал переоценки позиций и сохранения планов увеличен до 5 сек. 
	-  Исправлены настройки Redis для репликации, уменьшено потребление памяти для slave (api-сервер)
	-  Исправления в сервисе обработки рыночных данных от Deribit, связанные с переполнением запроса на подписку
	-  В сервисе instrumentMonitor удалена поддержка хранения оригинального описания инструмента (_info = null)
	-  Добавлен универсальный метод sell для закрытия позиции хеджа или отдельных позиций в ручном режиме
	-  Пересчет цены позиции теперь унифицирован с методом sell
	-  /ui метод возвращает теперь полное состояние сервера и рынка (позиции, ордера, сделки, хеджи)
	-  Добавлен метод /hedgeprices, возвращающий текущую переоценку всех позиций открытых хедж-планов
	-  Изменен алгоритм генерации execId в методе getRFQ на нативный из модуля crypto. WARNING! Теперь минимальная версия платформы NodeJS - v15.6.0. Устранен баг с возможной генерацией одинаковых идентификаторов при запросе хедж-планов.
	-  Исправления и улучшения в методе /private/sell если на продажу ставиться позиция, в которой нет купленных активов (а только открытые ордера).
	-  Исправление в API рассчета хедж-позиции, добавление параметра округления количества контрактов. Оптимизация очистки внутреннего кеша рассчитанных хедж-планов
	-  В трейдинг-API добавлен метод /private/state/backup который возвращает общее состояние стейта (все хедж-планы, сделки, трейды и т.п.) для сохранения во внешней среде.
	-  В трейдинг-API добавлен ендпоинт для отслеживания приближающихся сеттлментов по позициям
	-  Улучшем механизм сохранения снапшота, теперь сохраняеться каждый снапшот в архивном виде в директории /var/tmp/hedgebackup
-  **v0.5.2 from 09.09.2021**
	-  Добавление хранения между перезагрузками хедж-планов
	-  Добавлен трейдинг-сервер
	-  Документация по трейдинг-апи
	-  Исправлено заполнение блока openOrders при исполнении хеджа
	-  Исправлено несколько критических ошибок, которые вызивали краш и перезагрузку сервера
-  **v0.4.2 from 2021** 
	- В методе delivery/hedge убран блок расчета отдельно хеджа IL
	- В методе delivery/hedge добавлен блок calculator для использования в калькуляторе peanut trading
-  **v0.4.1 from 23.04.2021** 
	- Добавлен модуль агрегации портфелей между биржами и аккаунтами
	- Альфа-версия портфоли с биржи OKex
	- В API добавлен метод account/portfolio/aggregated, возвращающий суммарный портфель среди всех бирж
	- Добавлен служебный метод version, возвращающий актуальную версию API-сервера
	- Добавлена поддержка курсов с Uniswap (v2) через API от CoinGecko - ETH/USDT, ETH/USDC и ETH/DAI
	- Добавлен расчет hedgeCost в методе delivery/hedge - показывает, сколько % от начальной суммы  стоит хедж позиции
	- Метод delivery/hedge и rfq поддерживают параметры only_exchange и exclude_exchange
	- Поддаржка параметра contractttl, задает в секундах время актуальности рассчитанного хедж-плана. Дефолт теперь 4 часа, максимальное время - 24 часа - 1 секунда. 
	- Метод delivery/hedge поддерживает параметр предполагаемой годовой доходности (APR) пула и расчитывает доход пользователя для заданной суммы (в % доходности на каждый срок хеджа и в USD)
-  **v0.4.0 from 13.04.2021**
	- Метод rfq получил опцию only_exchange=code, позволяет задать только конкретную биржу, где расчитывать позицию.
	- Добавлен метод preparedplans (в будущем будет приватным), позволяет просмотреть все подготовленные планы исполнения ордеров, для которых срок валидности еще не истек
	- Теперь все планы исполнения возвращаються с уникальным идентификатором (execId) с которым они сохраняються на заданное время и готовы к передаче в трейдинг
	- По умолчанию, планы исполнения валидны в течении 30 сек.
	- Добавлен метод account/execute, позволяющий отправить подготовленный план исполнения ордера в трейдинг. 
	- Добавлена альфа-версия модуля трейдинга для биржи Deribit
	- Добавлен метод account/portfolio для отслеживания своего портфеля (в будущем формат может быть скорректирован для унификации поддержки других бирж, пока только Deribit)
	- Исправлено падение сервера при вызове метода расчета delivery/blackswanhedge
	- Добавлено отслеживание позиций по конкретным инструментам в методе account/portfolio (пока формат только Deribit)
	- Исправлена неправильная работа с сесионным идентификаторами (в случае работы с несколькими аккаунтами сразу)
	- Исправлены утечки памяти в сервисе instrumenterMonitor (отслеживание листинг/делистинг контрактов). Очень драматично было - с 2Гб памяти сократилось до 145Мб обычного 
	- Трейдинг-модуль поддерживает executionPlan-ы на продажу (только в режиме Reduce-only, тоесть, продажа ранее купленной позиции)
	- Модуль портфолио отслеживает теперь и позиции по инструментам и открытые ордера
	- Исправлена проблема с отсутствием агрегированной котировки для некоторых опционов если первой шла котировка от Bit.com 
-  **v0.3.1 from 30.03.2021**
	- Добавлен параметр hedgerate в методе delivery/hedge
	- Добавлен метод delivery/blackswanhedge для расчета хеджирования от крупных колебаний цены вверх и вниз (больше чем 50%)
-  **v0.3 from 27.03.2021**
	- Доступна дев-версия интерфейса (REST & WS)
	- Исправлена работа с датами експирации опционов (и все функции, зависящие от них)
	- Добавлен параметр covered в метод delivery/hedge, задающий % от общих потерь, который будет захеджирован
	- Улучшения в логике расчета хедж-позиции и executionPlan, в ряде граничных случаев, улучшающих стоимость хеджа.
	- Исправления в методах deliverydates/estimate и strike/estimate, которые ранее могли возвращать неоптимальные данные
	- Исправления в сервисе tickerAggregator, которые ранее пропускал некоторые опционные серии
	- Исправления в сервиса работы с данными от бирж Deribit, Delta и OKEx
	- Эксперементальная поддержка бинарного протокола (MessagePack) через WebSocket (подключение с параметром ?binarymode=true)
	- Добавлена опция поиска ближайшего страйка (direction=adj) вне зависимости от направления цены.

## Введение

Публичное API доступно без авторизации любому пользователю. 

Ограничение на количество запросов: 100 запросов в секунду. 

Система доступна по HTTPS и WebSocket (WebSocket Secure). 

На текущий момент доступно две платформы: 

- **Production** (REST) - https://api.optionshub.online/ 
- **Production** (WebSocket) - wss://api.optionshub.online/options-ws
- ***Next release** (dev, REST) - https://devapi.optionshub.online/*
- ***Next release** (dev, WebSocket,) - wss://devapi.optionshub.online/options-ws*

Важно! Стабильность работы и совместимость между платформами НЕ ГАРАНТИРУЕТСЯ. 

Production сервис настроен на автоматический перезапуск при возникновении критических ошибок, поэтому если вы получили HTTP ошибку или соединение WebSocket разорвано, просто повторите подключение, желательно через 5 секунд (сервис перезагружается через 1 секунду, однако стартовая инициализация может занять некоторое время). 

Для HTTP-REST интерфейса поддерживается HTTP 1.1 и HTTP 2.0, ответ сервера сжимается gzip-ом (если клиент заявил о поддержке). Для режима WebSocket поддерживается расширение permessage-deflate, версия протокола - 13. 

Все сообщения (в обоих протоколах) пересылаются в формате JSON. 

#### Стандартный формат ответа сервера (успешный ответ, REST):

```javascript
{
	status: "ok",
	data: {
        //any response data
    }
}
```

#### Ответ сервера в случае ошибки (REST):

```javascript
{
	code: 10042,	//Integer code of error (currently has no standart for this)
	status: "error",	//always "error" 
	message: "Unknown method"	//Message, described whats are goined wrong
}
```

Для HTTP-REST протокола параметры задаются через HTTP-параметры (GET-метод).

Для WebSocket-а форматы аналогичны, с несколькими дополнениями.

#### WS Request

```javascript
{	
	method: "subscribe", //Method, only supporting get|subscribe|unsubscribe 
	version: "v1",	//Protocol version, always "v1" at now
	actions: [	//Array of individual commands: [<command>, {Params object}]
			['heartbeat'],
			['orderbookraw', {symbol:'BITCOM:ETH-19MAR21-1880-C',raw:true}],
			['strike', {strike:2000, expired:true}]
	]
}
```

#### WS Response

```javascript
{
	status: "ok", //Status of execution, ok|error. For error, in addition, we adding code and message fields.
	id: 2, 	//Integer, sequence counter of command execution
	channel: "strike", //executed command or data channel
	data: {
		//any response data
	}
}
```

Ответ WebSocket-а в случае ошибки аналогичен REST-протоколу. Если ошибка незначительная, соединение остается открытым, если продолжить работу невозможно, соединение будет принудительно закрыто на стороне сервера. 



#### Current limitation

- Поддержка только целых контрактов (не поддерживается покупка части контрактов, например, 0.1)
- Только ETH как актив в основе контрактов
- Поддерживаемые биржи:
  - Deribit
  - OKEx
  - Huobi DM - отключена, опционы не транслируют по API
  - Delta.Exchange
  - Bit.com
  - FTX USA (ex LedgerX) - в процессе 
  - PowerTrade - в процессе 
  - Alternative Exchange - пока нет паблик API

Option symbol spec

​	Example: ETH-14SEP21-2400-C

## REST Endpoints

### version

Возвращает актуальную версию API-сервера (версия сервера + хеш версии). Для dev-версии изменяется хеш, продакшин версия полностью (релиз и хеш).

#### Request

​	https://api.optionshub.online/api/v1/version 

#### Params

​	Нет параметров

#### Response

```javascript
{
	status: "ok",
	data: {
		version: "v0.4.1-a738be44"
	}
}
```



### time

Возвращает текущее серверное время в формате ISO. Таймзона - UTC (GMT+0).

#### Request

​	https://api.optionshub.online/api/v1/time 

#### Params

​	Нет параметров

#### Response

```javascript
{
	status: "ok",
	data: {
		time: "2021-03-15T19:28:39.506Z"
	}
}
```



### heartbeat

Базовая проверка соединения и работоспособности сервера.

#### Request

​	https://api.optionshub.online/api/v1/heartbeat 

#### Params

​	Нет параметров

#### Response

```javascript
{
	status: "ok",
	data: { }
}
```



### status

Возвращает расширенный статус всех сервисов

#### Request

​	https://api.optionshub.online/api/v1/status 

#### Params

​	Нет параметров

#### Response

```javascript
{
	status: "ok",
	data: {
		maindb: "online",	//Status of main storage
		pubsub: "online",	//Status of internal mq sub-system
		time: "2021-03-15T19:34:57.770Z",	//Current server time in ISO format
		instrumenterMonitor: "online",
		marketdataDeribit: "online",	//Deribit connector
		marketdataOKEx: "online",	//OKEx connector
		marketdataDelta: "online",	//Delta Exchange connector
		marketdataHBDM: "online",	//Huobi DM connector
		orderbookAggregator: "online",	//Orderbook aggregator service 
		marketdataBitcom: "online",	//Bit.com connector
		indexAggregator: "online",	//Spot Indices aggregator service
		apiServer: "online",	//API service
		markpriceAggregator: "online",	//Mark price aggregator
		tickerAggregator: "online"	//Ticker market data aggregator
    }
}
```



### deliverydates

Возращает доступные даты экспирации (в случае, если на разных биржах время разное, возвращается самое ранее время).

#### Request

​	https://api.optionshub.online/api/v1/deliverydates 

#### Params

- **?asset** - базовый актив (e.g. https://api.optionshub.online/api/v1/deliverydates?asset=ETH)
- **?expired = true|false** - возвращать ли истекшие даты или только будущие.

#### Response

```javascript
{
    status: "ok",
    data: {
        "ETH": {
            "25JUN21": [
                [ "2021-06-25T08:00:00.000Z" ]
            ],
            "02APR21": [
                [ "2021-04-02T08:00:00.000Z" ]
            ],
            "28MAY21": [
                [ "2021-05-28T08:00:00.000Z" ]
            ],
            "24SEP21": [
                [ "2021-09-24T08:00:00.000Z" ]
            ],
            "17MAR21": [
                [ "2021-03-17T08:00:00.000Z" ]
            ]
        }
    }
}
```



### deliverydates/estimate

Возвращает ближайшую возможную дату (от сегодня) для указанного периода. Важно! Отсчет периода начинается от 08:00 текущего дня.

#### Request

​	https://api.optionshub.online/api/v1/deliverydates/estimate?asset=ETH&period=1m

#### Params

- **?asset** - базовый актив (e.g. ETH)
- **?period=(Nd)** где N - целое число, d - сокращение от периода: **d** - день, **w** - неделя, **m** - месяц, **y** - год. 
- **?count=1...N** - сколько результатов вернуть (по умолчанию - 1)

#### Response

```javascript
{
	status: "ok",
	data: {
		fromDate: "2021-03-15T08:00:00.000Z",	//Initial date
		futureDeliveryDate: "2021-06-15T08:00:00.000Z",	//Estimated future date
		estimatedDates: [	//Estimate delivery date of options
		    {
			deliveryDate: "25JUN21",	//Date code
			deliveryDateFull: "2021-06-25T08:00:00.000Z",	//Real date of delivery, ISO format
			durationHour: 2448	//Duration to date, in hours
		    },
		    {
			deliveryDate: "24SEP21",
			deliveryDateFull: "2021-09-24T08:00:00.000Z",
			durationHour: 4632
		    }
		]
	}
}
```



### strike

Возвращает доступные на бирже/биржах опционные страйки

#### Request

​	https://api.optionshub.online/api/v1/strike?strike=1200&expired=false

#### Params

- **?strike** - цена исполнения опциона (целое число)
- **?date** - код даты, например **19MAR21** (или ALL, если отсутствует)
- **?exchange** - код биржи, если нужн результаты с конкретной площадки (если, не указано - учитываются все площадки)
- **?expired=true|false** - учитываем ли прошлые даты, по умолчанию - false (только будущие)

#### Response

```javascript
{
    status: "ok",
    data: {
        "24SEP21": 1200,	//Object, key - standart date code, value - strike price
        "25JUN21": 1200,
        "28MAY21": 1200,
        "19MAR21": 1200,
        "26MAR21": 1200
    }
}
```

Если указана нерелевантная цена страйк (такая, которой пока нет ни на одной бирже), возвращается пустой ответ. Страйк округляется до ближайшего целого числа.



### strike/estimate

Позволяет найти ближайшую цену страйка к указанной цене (произвольной).

#### Request

​	https://api.optionshub.online/api/v1/strike/estimate?price=1877&direction=up&date=30APR21&count=3 

#### Params

- **?price** - ориентировочная цена (целое или число с плавающей точкой, будет округлено до ближайшего целого)
- **?direction=up|down|equal|adj** - поиск ближайшего биржевого страйка  - большего (up), меньшего (down), равного (equal) или лучшего (adj)
- **?date** - код даты, для которой идет поиск 
- **?count** - количество возвращаемых цен (по-умолчанию 1).

#### Response

```javascript
{
    status: "ok",
    data: {
        "30APR21": [	//date
            1880,	//minimal strike
            1900,	//next strike
            1920	//next strike
        ]
    }
}
```



### exchange

Возвращает общую информацю о биржах, с которыми мы работает (или будем работать) 

#### Request

​	https://api.optionshub.online/api/v1/exchange

#### Params

​	Нет параметров

#### Response	

```javascript
{
    status: "ok",
    data: {
        "deribit": {
            code: "deribit",	//Code of this exchange
            name: "Deribit",	//Full name
            info: "Deribit: The Institutional Grade Crypto Derivatives Platform. After several years of development, Deribit was launched in June 2016. John Jansen, the original founder, teamed up with Marius Jansen and our current CTO and started Deribit as a Bitcoin Futures and Options trading platform. At Deribit, we believe in the future of cryptocurrencies. It is our goal to continue to be at the forefront of the cryptocurrency derivatives market. We envision a future where cryptocurrencies are used by everyone and traded by millions. We are ready for the future, and so is our system.",
            url: "https://deribit.com/",	//Web-site URL
            logo: "https://contractmarketcap.com/images/deribit_exchange.png",	//Logo
            country: "Panama",	
            kyc: "Required",
            type: "cex",
            tradableAssets: {
                ETH: [
                    "futures",
                    "options"
                ],
                BTC: [
                    "futures",
                    "options"
                ]
         	},
        	isTradable: false	//Connect now with trading functions
        },
        "bitcom": {},
        "delta": {},
        "hbdm": {},
        "okex": {},
        "opyn": {}
    }
}
```



### contractmap

Возвращает какноничную форму (symbol) опциона и список соответствующих идентификаторов на конкретных биржах. Важно - исключая код биржи, дальше идет идентификатор, специфический для конкретной биржи.

#### Request

​	https://api.optionshub.online/api/v1/contractmap

#### Params

​	Нет параметров

#### Response

```javascript
{
    status: "ok",
    data: {
        "ETH-17MAR21-2000-P": [	//Canonical option code
       	 	"DERIBIT:ETH-17MAR21-2000-P",	//Code related for exchange: Deribit
        	"BITCOM:ETH-17MAR21-2000-P"		//Code at Bit.com exchange
        ],
        "ETH-05MAR21-1120-C": [
        	"DERIBIT:ETH-5MAR21-1120-C",
        	"BITCOM:ETH-5MAR21-1120-C"
        ],
        "ETH-05MAR21-1360-C": [
        	"OKEX:ETH-USD-210305-1360-C",
        	"BITCOM:ETH-5MAR21-1360-C",
        	"DERIBIT:ETH-5MAR21-1360-C"
        ],
        "ETH-28FEB21-1760-C": [
            "DERIBIT:ETH-28FEB21-1760-C",
            "OKEX:ETH-USD-210228-1760-C",
            "BITCOM:ETH-28FEB21-1760-C"
        ]
	}
}
```



### contract

Возвращает описание контракта. Позволяет отобрать контракты по параметрам (одному или нескольких). Если не указывать параметры, будут возвращены описания всех доступных контрактов.

#### Request

​	https://api.optionshub.online/api/v1/contract 

#### Params

- **?expired = true|false** - показывать или нет истекшие контракты.
- **?raw = true|false** - возвращать ли данные с биржи ("сырые" описания контрактов, как их отдает специфичное биржевое апи)
- **?exchange = DEREBIT|OKEX** - учитывать данные только с одной биржи по коду
- **?strike = N** - только инструменты с указанным страйком (целое число)
- **?type = put|call** - тип опциона
- **?asset = ETH** - базовый актив (сейчас поддерживается только ETH)
- **?quoted = USD|USDT** - валюта в которой котируется опцион (базовый индекс опциона)
- **?price = ETH|USD|USDT** - валюта, в которой торгуется опцион
- **?settled = ETH|USDT** - валюта исполнения опциона
- **?size = 1|N** - размер одного контракта (в базовой валюте, например, 1 (1ETH), 0.1 (0.1 ETH))
- **?symbol** - поиск по тикеру (полный код с указанием биржи)
- **?code** - поиск по биржевому идентификатору (*Depricated*)
- **?date = '12MAR21'** - поиск инструментов на указанную дату

#### Response

```javascript
{
    status: "ok",
    data: {
        "DERIBIT:ETH-17MAR21-2000-P": {	//Uniq symbol
            exchange: "deribit",	//Exchange code
            symbol: "DERIBIT:ETH-17MAR21-2000-P",	//Symbol
            id: "ETH-17MAR21-2000-P",	//Exchange-related ID
            code: "ETH-17MAR21-2000",	//Depricated
            tick_size: 0.0005,	//Price tick
            taker_fee: 0.03,	//Taker fee
            maker_fee: 0.03,	//Maker fee
            strike: 2000,	//Strike price
            base_currency: "ETH",
            quote_currency: "USD",
            price_currency: "ETH",
            option_type: "put",
            settlement_type: "inverse",
            settlement_currency: "ETH",
            settlement_fee: 0,
            min_trade_amount: 1,	//Minimal trade amount, in contract
            is_active: true,	//Is a contract currently active or no
            expiration_date: "17MAR21",
            expiration_timestamp: 1615968000000,
            creation_timestamp: 1615795202000,
            contract_size: 1,	//Size of one contract, in base asset
            underlying_index: ".ETH-USD",	//Symbol of underlaying index
            block_trade_size: 500,	//If available, minimal order amount of block trading, in base asset
            block_trade_fee: 0,
            fee_as: "prc",	//Fee unit - prc: percent of trade amount, USDT - fixed fee in USDT per one traded contract
            _info: null	//Raw exchnage-related info
        },
        "OKEX:ETH-30APR21-1560-P": {
            exchange: "okex",
            symbol: "OKEX:ETH-30APR21-1560-P",
            id: "ETH-USD-210430-1560-P",
            code: "ETH-30APR21-1560",
            tick_size: 0.0005,
            taker_fee: 0.03,
            maker_fee: 0.02,
            strike: 1560,
            base_currency: "ETH",
            quote_currency: "USDT",
            price_currency: "ETH",
            option_type: "put",
            settlement_type: "inverse",
            settlement_currency: "ETH",
            settlement_fee: 0.02,
            min_trade_amount: 1,
            is_active: true,
            expiration_date: "30APR21",
            expiration_timestamp: 1619769600000,
            creation_timestamp: 1613118611940,
            contract_size: 1,
            underlying_index: ".ETH-USD",
            block_trade_size: 0,
            block_trade_fee: 0,
            fee_as: "prc",
            _info: null
        }
    }
}
```



### spotindex

Возвращает описание и значение текущих индексов

#### Request

​	https://api.optionshub.online/api/v1/spotindex

#### Params

- **?symbol** - код индекса, например, BITCOM:ETH-USD
- **?currency** = USD|USDT - валюта котирования индекса
- **?asset = ETH** - базовый актив, сейчас только ETH
- **?raw = true|false** возвращать ли описание индекса с биржи (по-умолчанию, false)

#### Response

```javascript
{
    status: "ok",
    data: {
        "BITCOM:ETH-USD": {		//Index symbol
            exchange: "bitcom",	//Exchange code
            id: "ETH_USD",	//Exchange-related id
            index_symbol: "BITCOM:ETH-USD",	//Uniq index symbol
            index_base: "ETH",	//Base asset
            index_currency: "USD",	//Currency of index
            index_timestamp: 1615847115580,	//Latest update
            index_price: 1789.1,	//Index value
            _info: null	//Raw exchange responce, if provided
        },
        "OKEX:ETH-USD": {
            exchange: "okex",
            id: "ETH-USD",
            index_symbol: "OKEX:ETH-USD",
            index_base: "ETH",
            index_currency: "USD",
            index_timestamp: 1615847115395,
            index_price: 1789.042,
            _info: null
    	}
    }
}
```

Примечание: тикер **OHUB** (или код биржи - optionshub) используется для агрегированных индексов. 



### orderbook

Возвращает агрегированную и приведенную к единому формату книгу заявок (ордербук)

#### Request

​	https://api.optionshub.online/api/v1/orderbook?symbol=ETH-12MAR21-1800-C

#### Params

- **?symbol** - универсальный идентификатор инструмента, с префиксом биржи или без него
- **?raw = true|false** - возвращает также индивидуальные ордербуки бирж

#### Response

```javascript
{
	status: "ok",
    data: {
    	"ETH-12MAR21-1800-C": {
    		symbol: "ETH-12MAR21-1800-C",
    		ind: {
    			"deribit": {
    				bid: [ ],
               			ask: [
               				[0.006, 2],	//Array of [<price>, <amount, contracts>]
                			[0.01, 150],
                    			[0.019, 1],
                			[0.03, 1]
   				],
    				ts: 1615535096361,
    				id: "ETH-12MAR21-1800-C",
    				spot: 1794.39,
    				exchange: "deribit",
    				cpu: "ETH",
    				cvu: 1,
				fee: {
				    taker: 0.03,
				    maker: 0.03,
				    settl: 0.015,
				    type: "prc"
				}
			}
		    delta: {},	//Other exchange orderbook
		    hbdm: {},	//Other exchange orderbook
		    okex: {}	//Other exchange orderbook
		},
   		agg: {
    		symbol: "ETH-12MAR21-1800-C",
    		bid: [
                    [
                    0.000028,	//Price, in ETH
                    27864,		//Amount of contracts
                    278.64,		//Amount, ETH
                    "delta",	//Exchange
                    "adj"		//Type - raw (no convertation) or adj (adjusted by our side)
                    ]
   				 ],
    		ask: [
                    [
                    0.000282,
                    27864,
                    278.64,
                    "delta",
                    "adj"
                    ],
                    [
                    0.006,
                    2,
                    2,
                    "deribit",
                    "raw"
                    ]
                  ],
   			info: {},
            bbid: [
            	0.000028,
            	278.64,
            	1
           	],
            bask: [
            	0.000282,
            	278.64,
            	1
            ],
            avgspot: 1778.3566,
            updated: 1615553842866,
            ttp: 0.049489
          },
          spot: {	//Individual exchange spot prices
                deribit: 1794.39,
                delta: 1773.965,
                hbdm: 1775.8133,
                okex: 1769.258,
                agg: 1778.3566
          },
          upd: 4	//Updater counter
    	}
    }
}
```

**Примечание**: Известен баг с отсутствием поля symbol для опционов ранее чем 16 марта 2021 г. Второй баг связан с тем, что после обновления ордербука не удаляется уже отсутствующий блок инфо.



### orderbook/composite

Возвращает только агрегированный ордербук. 

#### Request

​	https://api.optionshub.online/api/v1/orderbook/composite?symbol=ETH-12MAR21-1800-C

#### Params

- **?symbol** - универсальный идентификатор инструмента, с префиксом биржи или без него
- **?raw = true|false** - возвращает также индивидуальные ордербуки бирж

#### Response

```javascript
{
    status: "ok",
    data: {
        "ETH-12MAR21-1800-C": {
        	symbol: "ETH-12MAR21-1800-C",
        	bid: [	//Array of bid quotes, can be empty, ordered by price
                [
                0.000028,	//Price (in ETH)
                27864,		//Amount, in contracts
                278.64,		//Amount, adjusted to ETH
                "delta",	//exchange code
                "adj"	//Type of amount (re-calc if contract size not aligned to 1 cont == 1 ETH or quoted in USDT)
                ]
        	],
        ask: [	//Array of ask quotes, can be empty, ordered by price
            [
                0.000282,
                27864,
                278.64,
                "delta",
                "adj"
            ],
            [
                0.006,
                2,
                2,
                "deribit",
                "raw"
            ]
        ],
        info: {		//Addition info about quotes per exchange
            deribit: {
                id: "ETH-12MAR21-1800-C",	//Exchange-related id
                spot: 1794.39,	//Current spot price index (used if need to re-calc)
                cpu: "ETH",		//Contract value in
                cvu: 1,		//Contract size
                fee: {	//Fee structure
                    taker: 0.03,	//Taker fee
                    maker: 0.03,	//Maker fee
                    settl: 0.015,	//Fee for settlment (In-the-money option)
                    type: "prc"		//Fee calc type - percent or fixed
                }
            },
            delta: {
                id: "C-ETH-1800-120321",
                spot: 1773.965,
                cpu: "USDT",
                cvu: 0.01,	//Size of one contract, e.g. 0.01ETH
                fee: {
                    taker: 0.05,
                    maker: 0.05,
                    settl: 0.05,
                    type: "prc"
                }
            },
            hbdm: {
                id: "ETH-USDT-210312-C-1800",
                spot: 1775.8133,
                cpu: "USDT",
                cvu: 0.01,
                fee: {
                    taker: 0.00125,
                    maker: 0.0005,
                    settl: 0.0005,
                    type: "USDT"	//Fixed fee per contract
                }
            }
        },
        bbid: [		//Best bid (can be aggregate), always adjusted
        	0.000028,	//Best price, ETH
        	278.64,		//Amount, ETH
        	1		//How much exchange aggregated in best
        ],
        bask: [
        	0.000282,
        	278.64,
       		1
        ],
        avgspot: 1778.3566,	//Average spot price, over this book
        updated: 1615553842866,	//Book updated
        ttp: 0.049489	//System field, time to processing this book, in milliseconds.
        }
    }
}
```



### orderbook/raw

Возвращает ордербук с конкретной биржи (теоретически, работает быстрее, так как считывает данные напрямую из хранилища, не ожидая агрегации). Обратите внимание, данные не пересчитаны, как в агрегированном варианте.

#### Request

​	https://api.optionshub.online/api/v1/orderbook/raw?symbol=DELTA:ETH-12MAR21-1800-C

#### Params

- **?symbol** - тикер в универсальном формате с указанием биржи, например DELTA:ETH-12MAR21-1800-C

#### Response

```javascript
{
    status: "ok",
    data: {
        "DELTA:ETH-12MAR21-1800-C": {
            bid: [	//Raw orderbook from exchange
                [0.05, 27864]	//[<price>, amount of contract]
            ],
            ask: [
                [0.5, 27864]
            ],
            ts: 1615550399740,	//Updated timestamp
            id: "C-ETH-1800-120321",	//Exchange id
            spot: 1773.965,	//Current spot index from exchange
            exchange: "delta"	//Exchange code
        }
    }
}
```



### markprice

Возвращает цену маркировки (теоретическая справедливая цена опционного контракта) вместе с другими опционными метриками. Обратите внимание - размер ответа может быть очень большим, если в качестве symbol задана только часть кода инструмента.

#### Request

​	https://api.optionshub.online/api/v1/markprice?symbol=OKEX:ETH-30APR21-1560-P

#### Params

- **?symbol** - идентификатор инструмента, с указанием биржи. Можно опускать отдельные части, тогда будут все варианты. Например: OKEX:ETH-30APR21-1560 - вернет оба опциона, P (Put) и C (Call) и т.п. Минимально достаточно: <БИРЖА>:<АКТИВ>.
- **?raw=true|false** - показать ответ от биржи оригинальный

#### Response

```javascript
{
    status: "ok",
    data: {
        "OKEX:ETH-30APR21-1560-P": {
            exchange: "okex",	//Exchange code
            symbol: "OKEX:ETH-30APR21-1560-P",	//Uniq symbol
            id: "ETH-USD-210430-1560-P",	//Exchange-related id
            mark_timestamp: 1615883704049,	//Timestamp of mark price
            mark_price: 0.08279994,	//Current mark price (in ETH)
            iv: 1.1705,	// Implied volatility (daily)
            underlaying_symbol: "ETH-USD",	//Index symbol
            mark_price_usd: 149.845,	//Mark price, re-calc to USD
            delta: -0.2646017872,	//Delta greek for option
            vega: 2.0770390767,		//Vega greek of option
            theta: -2.341272867,	//Theta greek
            gamma: 0.0004397091,	//Gamma greek
            rho: null,		//Rho greek
            _info: null		//Raw exchange result, if provided
        },
        "OKEX:ETH-30APR21-1560-C": {
            exchange: "okex",
            symbol: "OKEX:ETH-30APR21-1560-C",
            id: "ETH-USD-210430-1560-C",
            mark_timestamp: 1615883704049,
            mark_price: 0.24244293,
            iv: 1.1705,
            underlaying_symbol: "ETH-USD",
            mark_price_usd: 438.755,
            delta: 0.7353982128,
            vega: 2.0770390767,
            theta: -3.2004064775,
            gamma: 0.0004397091,
            rho: null,
            _info: null
        }
    }
}
```

More about option greeks:

- https://www.investopedia.com/terms/g/greeks.asp
- https://optionclue.com/trading/options-trading/podrazumevaemaya-volatilnost/
- https://optionwatcher.livejournal.com/206228.html
- https://afdanalyse.ru/publ/investicionnyj_analiz/teorija/modeli_cenoobrazovanija_opcionov/27-1-0-376
- https://utmagazine.ru/posts/11662-modeli-cenoobrazovaniya-opcionov

**Важно**! На текущий момент мы не расчитываем собственные параметры опционов (кроме mark_price_usd), поэтому если один или несколько параметров недоступны, значит биржа их не расчитывает и/или не отдает. 

**Важно**! Несмотря на то, что все биржи декларируют использование одной модели - Black–Scholes, однако идентичное воспроизведение вычислений затруднено.



### markprice/composite

Возвращает последнюю, минимальную и максимальную цены маркировки среди всех источников (бирж). На текущий момент параметры опционов не пересчитываются, если отсутствуют на бирже.

#### Request

​	https://api.optionshub.online/api/v1/markprice/composite?symbol=ETH-30APR21-1560-P&raw=true

#### Params

- **?symbol** - идентификатор опциона (без кода биржи, часть параметров можно опустить)
- **?raw = true|false** - возвращать или нет отдельные цены (или только последнюю, минимальную и максимальную)

#### Response

```javascript
{
    status: "ok",
    data: {
        "ETH-30APR21-1560-P": {
            ind: {	//Individual mark price
                okex: {
                    exchange: "okex",
                    symbol: "OKEX:ETH-30APR21-1560-P",
                    id: "ETH-USD-210430-1560-P",
                    mark_timestamp: 1615885106049,
                    mark_price: 0.08391542,
                    iv: 1.1697,
                    underlaying_symbol: "ETH-USD",
                    mark_price_usd: 151.428,
                    delta: -0.2673563555,
                    vega: 2.081604977,
                    theta: -2.3524020767,
                    gamma: 0.0004436827,
                    rho: null,
                    _info: null
                }
            },
            agg: {	//Aggregate (latest)
                exchange: "optionshub",	//rewrite by our exchange
                symbol: "OHUB:ETH-30APR21-1560-P",	//Symbol
                mark_price: 0.08391542,	//Mark price, ETH
                mark_price_usd: 151.428,	//Mark price, USD
                spot_index: 1804.536,	//Current aggregate spot price
                mark_iv: 1.1697,	//Implied volatility
                mark_timestamp: 1615885106049 //Timestamp
            },
            best: {
                min: {	//Minimal mark price over the market
                    exchange: "okex",
                    symbol: "OKEX:ETH-30APR21-1560-P",
                    mark_price: 0.08391542,
                    mark_price_usd: 151.428,
                    spot_index: 1804.536,
                    mark_iv: 1.1697,
                    mark_timestamp: 1615885106049
                },
                max: {	//maximal mark price over the market
                    exchange: "okex",
                    symbol: "OKEX:ETH-30APR21-1560-P",
                    mark_price: 0.08391542,
                    mark_price_usd: 151.428,
                    spot_index: 1804.536,
                    mark_iv: 1.1697,
                    mark_timestamp: 1615885106049
                }
        	},
        	info: { },
        	updated: 1615885106049,
        	ttp: 196316
        }
    }
}
```

**Важно**: на сейчас мы не агрегируем котировку, а используем последнюю среди всех рынков. Опционные греки также не расчитываются в текущей версии, волатильность (IV) используется, как определена на бирже. В будущем мы добавим расчет показателей, используя собственную модель ценообразования.



### ticker

Возвращает market summary по инструменту (или набору инструментов). 

#### Request

​	https://api.optionshub.online/api/v1/ticker?symbol=ETH-25JUN21-1120-P&raw=false 

#### Params

- **?symbol** - код инструмента (или его части). Если не указана биржа (или указать бируж OHUB) - то возвращается лучшая (последняя) цена, если указать конкретную биржу - возвращают только информацию с этой биржи. 
- **?raw = true|false** - добавляет информацию с биржи 

#### Response

```javascript
{
    status: "ok",
    data: {
        "OHUB:ETH-25JUN21-1120-P": {
            exchange: "optionshub",
            symbol: "OHUB:ETH-25JUN21-1120-P",
            id: "OKEX:ETH-25JUN21-1120-P",	//Uniq code from exchange
            code: "ETH-25JUN21-1120-P",
            ticker_timestamp: 1615886485031,	//Timestamp
            best_bid: 0.0495,	//Best market bid, ETH
            best_ask: 0.057,	//Best ask, ETH 
            best_bid_size: 505,	//Amount, in contract
            best_ask_size: 500,
            ask_iv: 1.2695,	//Ask Implied volatility
            bid_iv: 1.1987, //Bid Implied volatility
            last_price: 0.052, //Price of last trade, ETH
            last_size: null,	//Size of the last trade (if provided)
            open24h: 0.052,		//Open price, 24h
            high24h: 0.052,		//High price, 24h
            low24h: 0.052,		//Low price, 24h
            price_change24h: 0,	//Price change from open, %
            volume24h: 0,	//Total trading volume, in contracts, 24h
            open_interest: 9,	//Open Interest, in contract
            min_price: 0.0005,	
            max_price: 0.1495,
            underlying_name: "ETH-USD",
            underlying_price: null,	//Possible bug, current latest index price
            estimated_delivery_price: 0,	//Estimate price of delivery
            mark_price: 0.05375389,	//Current mark price
            iv: 1.2388,	//Implied volatility by mark price
            delta: -0.1228524948,	//Delta greek (by mark price)
            vega: 1.9116723323,	//Vega greek (by mark price)
            theta: -0.9552035518,	//Theta greek (by mark price)
            gamma: 0.0001710731,	//Gamma greek (by mark price)
            rho: null,	//Rho greek (by mark price)
            _info: null	//Optional, raw exchange response
        }
    }
}
```

**Важно**: если указан частичный символ (например, ETH-25JUN21) и особенно raw=true, размер ответа может быть большим (up to 1Mb)



### bbo

Возвращает лучшую цену (top of book), приведенную к общему формату. Можно указывать частичный код для получения лучших цен по всем инструментам. Учитывайте, частота обновления сейчас ~ 100ms.

#### Request

​	https://api.optionshub.online/api/v1/bbo?symbol=ETH-25JUN21-1120-P

#### Params

- **?symbol** - код инструмента или его часть.

#### **Response**

```javascript
{
    status: "ok",
    data: {
        "ETH-25JUN21-1120-P": {
            exchange: "optionshub",		//Always optionhub, top-of-book possible be aggregated
            symbol: "ETH-25JUN21-1120-P",	//Symbol
            bestBid: [	//Best Bid
            	0.048,	//Best price
            	500,	//Best bid amount, ETH
            	1	//Count of book orders, aggregated in
            ],
            bestAsk: [
                0.0495,
                190,
                1
            ],
            avgspot: 1813.5811,	//Average spot price, USD
            updated: 1615887415487
        }
    }
}
```



### rfq

Request for quote - запрос на лучшую котировку и план исполнения ордера

#### Request

​	https://api.optionshub.online/api/v1/rfq?symbol=ETH-25JUN21-1120-P&amount=5

#### Params

- **?symbol** - полный код инструмента (без биржи, частичный символ не допускается)
- **?amount=N** - количество (ETH) для покупки/продажи. Сейчас поддержка только целый чисел (сумм). По -умолчанию, 1 (1ETH)
- **?slippage=0.1...100** с шагом 0.1. % проскальзывания цены, который допускается при исполнении заявки.
- ***?volume=N** - сумма ордера, на которую покупается/продается (Не реализовано сейчас).
- **?clientid=X** - идентификатор клиента, произвольная строка, до 64 символов ASCII.
- **?risk** - использование преднастроенных профилей риск-менеджмента.  (Не реализовано сейчас).
- **?exclude_exchange=code1,code2** - список кодов бирж (bitcom, delta, deribit etc.) котировки которых исключаются из плана
- **?only_exchange=code1,code2** - список кодов бирж (bitcom, delta, deribit etc.) только котировки которых включаються в план
- **?side=bid|ask|all** - сторона сделки  (Не реализовано сейчас).
- **?allocation=N** - максимальный процент аллокации на одну биржу  (Не реализовано сейчас).
- **?raw=true|false** - показывать ли детальные данные исполнения
- **?unclosedaction** - что делать с незаполненным обьемом (Не реализовано сейчас).
- **?orderttl=N** - время актуальности рассчитаного плана исполнения ордера, в секундах. Дефолт: 4 часа (4 * 3600). Максимальное значение - 24 часа - 1 секунда, минимальное - 1 сек.

#### Response

```javascript
{
    status: "ok",
    data: {
        bid: {
            executionPlan: [	//Orders plan to execute order by best price and risk
                {
                    exchange: "deribit",	//Exchange
                    id: "ETH-25JUN21-1120-P",	//Exchange-related id of contract
                    direction: "sell",	//Sell or Buy order
                    order: "limit",	//Order type (limit or market). Always limit for closed amount
                    price: 0.046,	//price (in ETH)
                    amount: 100,	//Amount in order (in ETH)
                    total: 4.6,		//Total size (in ETH)
                    fee: 0.00138,	//Exchnage fee (in ETH)
                    feeUSD: 2.494778,	//Exchnage fee, recalc to USD
                    spot: 1807.81,	//Current spot index price
                    contracts: 100,	//Order size, in contract
                    balance: 0,	//Minimal total balance, needed for execution
                    timestamp: 1615889147963
                }
            ],
            requestedAmount: 100,	//Requested amount of order (in ETH)
            closedAmount: 100,		//How much we closed
            unclosedAmount: 0,		//How much we can't close at market
            totalAmount: 100,		//Total closed amount in orders
            totalVol: 4.6,			//Total payment (in ETH)
            totalFee: 0.00138,		//Total fee (in ETH)	
            totalFeeUSD: 2.494778,	//Total fee, recalc to USD
            totalPriceMean: 0.046,	//Mean resulted price of closing orders
            priceLimit: 0.04554,	//Deadline price (based on best price and slippage param)
            serviceTradingFee: 0.1,	//Addition service fee for trading operation (%)
            serviceDeliveryFee: 0,	//Addition service delivery fee (only for buy-side)
            totalServiceFee: 0.0046, //Total addition service fee (in ETH)
            _info: null				//Extended (debug) info, if provided
         },
         ask: {
            executionPlan: [
                {
                    exchange: "deribit",
                    id: "ETH-25JUN21-1120-P",
                    direction: "buy",
                    order: "limit",
                    price: 0.05,
                    amount: 100,
                    total: 5,
                    fee: 0.0022500000000000003,
                    feeUSD: 4.067573,
                    spot: 1807.81,
                    contracts: 0,
                    balance: 5.00225,
                    timestamp: 1615889147963
                }
            ],
            requestedAmount: 100,
            closedAmount: 100,
            unclosedAmount: 0,
            totalAmount: 100,
            totalVol: 5,
            totalFee: 0.00225,
            totalFeeUSD: 4.067573,
            totalPriceMean: 0.05,
            priceLimit: 0.0505,
            serviceTradingFee: 0.1,
            serviceDeliveryFee: 0.25,
            totalServiceFee: 0.0175,
            _info: null
        },
        info: null,
        updated: 1615889147963,
        updated_date: "2021-03-16T10:05:47.963Z",
        rawBook: null,	//Debug info, raw orderbook for calculation best execution plan
        ttp: 1.776	//System, time to calculate RFQ, in milliseconds
    }
}
```

Текущие ограничения: не расчитывается баланс для продажи (с учетом маржинальных требований).



### delivery/hedge

Calculation of estimate hedge position (full cover and only IL). Метод получает начальную цену инвестиции (если не указана, текущая агрегированная цена ETH-USD), параметры пула и предполагаемую будущую цену падения стоимости актива. На базе этих параметров вычисляются потери (IL и полная потеря) и находится количество опционных контрактов, которое необходимо купить для покрытия потерь. 

#### Request

​	https://api.optionshub.online/api/v1/delivery/hedge?initrate=1000&currentrate=200&amount=80000&

#### Params

- **?initrate** - начальная цена актива, например, 1000 (ETH/USD = 1000 USD per 1ETH)
- **?currentrate** - ориентировочная цена (на сейчас или в будущем)
- **?amount=N** - сумма в валюте (USD), которая идет в пул. Целое число (от 1)
- **?strikechange=N** - допустимый % отклонения страйк-цены при поиске хедж-а от текущей рыночной. Дефол - 30%. 
- **?slippage=N** - допустимое отклонение цены опциона (в %, с шагом в 0.1). Дефолт - 5%
- **?pool=Type** - тип пула (пропорция разделения). Сейчас поддерживается только один: '50/50' (задан по дефолту)
- **?covered=N** - в %, какую часть от userTotalLoss хеджировать. Целое число, от 1 до 100. Дефолт - 100.
- **?hedgerate=N** - задает ориентир для хедж позиции (strike). Если hedgrate != initrate это означает, что пользователь согласен с некоторым убытком. По сути это ориентир цены, до которой может опуститься курс прежде, чем начнет действовать страховка. Если не задано, цена равна initrate. Важно, для корректного рассчета должно выполняться условие: initrate > currentrate > hedgerate 
- **?clientid=X** - задает абстрактный идентификатор клиента, для которого ведеться расчет хедж-позиции. Если не задан, каждый раз генерируеться уникальный. Формат - произвольная строка до 64-х символов длиной, только ASCII символы (a-zA-z0-1).
- **?apr=N** - в %, задает ориентир по годовой доходности пула (APR, annual Pool Rate или annual ROI). Здаеться в % с точностью до 0.01. Дефолт задаеться в 58.65% (ориентир для пула ETH-USDT Uniswap v2 согласно https://www.liquidityfolio.com/)
- **?exclude_exchange=ex1,ex2** и **?only_exchange=ex1,ex2** - задает биржи которые исключаються из расчета плана исполнения хеджа  или наоборот, используються. Если не задано - рассчет идет по всем подключенным площадкам.
- **?orderttl=N** - время актуальности рассчитаного плана исполнения ордера, в секундах. Дефолт: 4 часа (4 * 3600). Максимальное значение - 24 часа - 1 секунда, минимальное - 1 сек.
- **?contractsround=up|down|mid** - задает округление количества контрактов до ближайшего целого числа.

#### Response

```javascript
{
    status: "ok",
    data: {
        priceRatio: 0.887021,	//ration, current price vs initial price
        ILpercent: 0.1794,		//Impermanent loss of pool
        userInitialAmount: 80000,	//Initial amount, invested into the pool, in abstract USD
        pool: "50/50",	//Type of the pool
        userAssetAmount: 21.052632,	//Share of the assets (e.g. ETH)
        userCurrencyAmount: 40000,	//Share of the currency (e.g. USD)
        ILcurrency: 135.413,	//Impermanent loss in currency
        userAmountAfter: 75345.4301828701,	//Total user balance after
        userTotalLoss: 4654.57,	//Total user loss (IL + rate loss)
        idealFullHedgeNeededContractsRaw: 21.68,	//Estimate, how much contracts we need to cover full loss
        idealILHedgeNeededContractsRaw: 0.63,	//Estimate, how much contracts we need to cover only IL  loss
        idealFullHedgeNeededContracts: 22,	//Real amount of the contracts
        idealILHedgeNeededContracts: 1,	//Real amount of the contracts
        fullHedgeCoveringPrc: 50,	//How much, in %, we covered by hedge
	fullHedgeCovering: 2327.28,	//How much covered, in USD
        hedgeFullPLan: {
        	30APR21: {	//Date to hedge
                durationDay: 42,	//How many days to hedge cover the loss
                estimateRoi: 53,	// Estimate annual ROI, in %
		estimateRoiUSD: 5300, // Estimated annual ROI in USD, based at initial amount and duration day
                estimateStrike: 1920,	//Estemate strike price for option
                hedgeContractsRaw: 19.84,	//Adjasted, how much contracts we need to buy
                hedgeContracts: 20,	//Real count of contracts to buy
                currentPrice: 1685.34,	//Current market spot of underlaying asset 
                paymentPerContract: 234.66,	//Payout (in USD) per contract, if current price will be in settlment
                paymentTotal: 4693.2,	//Total payout of position
            	hedge: {	
        		executionPlan: [	//Detailed execution plan, see RFQ method spec. one or more orders to execute
                        {
                            exchange: "deribit",
                            id: "ETH-30APR21-1920-P",
                            direction: "buy",
                            order: "limit",
                            price: 0.1745,
                            amount: 18,
                            total: 3.141,
                            fee: 0.00141345,
                            feeUSD: 2.534118,
                            spot: 1792.86,
                            contracts: 0,
                            balance: 3.142413,	//How much currency we need at exchange balance to close an order (ETH)
                            timestamp: 1616071404551
                        },
                        {
                            exchange: "deribit",
                            id: "ETH-30APR21-1920-P",
                            direction: "buy",
                            order: "limit",
                            price: 0.175,
                            amount: 2,
                            total: 0.35,
                            fee: 0.0001575,
                            feeUSD: 0.282375,
                            spot: 1792.86,
                            contracts: 0,
                            balance: 0.350157,
                            timestamp: 1616071404551
                        }
                    ],
                    requestedAmount: 20,
                    closedAmount: 20,
                    unclosedAmount: 0,
                    totalAmount: 20,
                    totalVol: 3.491,	//Total amount of ETH, that's we need to buy hedge position
                    totalVolUSD: 6259.094, //Total amount in USD   
                    hedgeCost: 3.2631, //Cost of hedge, in % from initial amount
                    totalFee: 0.001571,
                    totalFeeUSD: 2.816493,
                    totalPriceMean: 0.17455,
                    priceLimit: 0.183225,
                    serviceTradingFee: 0.1, //%
                    serviceDeliveryFee: 0.25, //%
                    totalServiceFee: 0.012219, //Total service fee, not included to totalVol
                    _info: null,
                    updated: 1616071404551,
                    updated_date: "2021-03-18T12:43:24.551Z",
                    ttp: 5.693
       			}
        	},
            24SEP21: {},	//Other dates, thats available to create hedge position immediatly now
            25JUN21: {},
            31DEC21: {},
            09APR21: {},
            02APR21: {},
            26MAR21: {}
        },
        hedgeILPLan: {	//Hedge plan only for cover IL loss
            30APR21: {}
            24SEP21: {},
            25JUN21: {},
            31DEC21: {},
            09APR21: {},
            02APR21: {},
            26MAR21: {}
        }
    }
}
```

**Важно**: при подписке на метод обновления будут поступать с частотой 1 раз в секунду, а не 100ms как обновление стакана.

**Примечание**: 

- https://finematics.com/impermanent-loss-explained/
- https://pintail.medium.com/uniswap-a-good-deal-for-liquidity-providers-104c0b6816f2 (calc based on this article)
- https://defiyield.info/advanced-impermanent-loss-calculator
- https://defiyield-info.medium.com/why-impermanent-loss-calculators-are-wrong-and-how-to-avoid-incorrect-assessment-of-the-money-waste-d349607706fc



### delivery/blackswanhedge [Deprecated]

Метод пробует оценить хеджирование от сильной рыночной волатильности, рассчитывая ее за фиксированный период в 90 дней. **Важно!** Термин "волатильность" здесь не в классическом понимании (отклонение от среднеквадратичного значения) а просто ценовые рамки (min / max / avg) за период.

В текущей реализации метод возвращает данные по минимальной, средней и максимальной цене за период в 90d по данным CryptoCompare, но использует для расчета коридор цен +50% и -50% от текущей спот цены.

#### Request

​	https://api.optionshub.online/api/v1/delivery/blackswanhedge?amount=10000 

#### Params

- **?amount=N** - для какой суммы (USD) считать позицию
- **?strikechange=N** (%) максимальное отклонение страйк-цен при поиске позиции
- **?slippage=N** - слиппедж (проскальзывание) цен, %. Дефолт - 5% с точностью до 0.1%.
- **?raw=true|false** - возвращать ли оригинальную информацию для отладки
- **?orderttl=N** - время актуальности рассчитаного плана исполнения ордера, в секундах. Дефолт: 4 часа (4 * 3600). Максимальное значение - 24 часа - 1 секунда, минимальное - 1 сек.

#### Response

```javascript
{
    status: "ok",
    data: {
        userInitialAmount: 10000,
        actualRate: 1842.569,	//Current spot rate
        userAssetAmount: 5.417552,	//ow much in asset (ETH)
        hvol: {	//Historical data
            symbol: "ETH-USD",
            exchange: "cryptocompare",
            min: 716.71,	//Min price
            max: 2038.52,	//Max
            avg: 1502.94,	//Avg price
            downVol: 52,	//In %, how much drop down
            maxVol: 35,		//In %, how much up
            period: "90d",
            _info: null
        },
        minPriceToHedge: 921.28,	//Min price to find hedge position down risk
        maxPriceToHedge: 2763.85,	//Max price, after than we hadge
        estimateHedgePeriod: "90d",	//Minimal period to find
        realHedgeDatesRaw: [	//Possible dates to hadge
            {
                deliveryDate: "31DEC21",	//Warn: bug with duplicate date
                deliveryDateFull: "2021-12-31T12:00:00.000Z",
                durationDays: 276
            },
            {
                deliveryDate: "31DEC21",
                deliveryDateFull: "2021-12-31T08:00:00.000Z",
                durationDays: 276
            },
            {
                deliveryDate: "24SEP21",
                deliveryDateFull: "2021-09-24T08:00:00.000Z",
                durationDays: 178
            },
            {
                deliveryDate: "25MAR22",
                deliveryDateFull: "2022-03-25T08:00:00.000Z",
                durationDays: 360
            }
        ],
        realHedgeDate: {	//Real possible hedge
            24SEP21: {
            	deliveryDate: "24SEP21",
            	deliveryDateFull: "2021-09-24T08:00:00.000Z",
            	durationDays: 178,
           	putStrike: 960,	//real PUT option strike
            	callStrike: 2800,	//real CALL option strike
            	userAssetAmount: 6,	//Contracts we need
            	putHedge: {
            		executionPlan: [
                        {
                            exchange: "deribit",
                            id: "ETH-24SEP21-960-P",
                            direction: "buy",
                            order: "limit",
                            price: 0.0355,
                            amount: 3,
                            total: 0.1065,
                            fee: 0.000047925,
                            feeUSD: 0.088205,
                            spot: 1840.48,
                            contracts: 0,
                            balance: 0.106548,
                            timestamp: 1617104372696
                        }
                	],
                    requestedAmount: 3,
                    closedAmount: 3,
                    unclosedAmount: 0,
                    totalAmount: 3,
                    totalVol: 0.1065,
                    totalFee: 0.000048,
                    totalFeeUSD: 0.088205,
                    totalPriceMean: 0.0355,
                    priceLimit: 0.044375,
                    serviceTradingFee: 0.1,
                    serviceDeliveryFee: 0.25,
                    totalServiceFee: 0.000373,
                    _info: null,
                    updated: 1617104372696,
                    updated_date: "2021-03-30T11:39:32.696Z",
                    ttp: 1.628
                },
                totalCostETH: 0.1065,	//Total cost of hedge in ETH
                totalCostUSD: 196.23	//Total cost of hedge in USD
            }
        }
    }
}
```



### checkplan

Проверяет соответствие ранее рассчитанного плана выполнения ордера и текущего рынка. Возвращает отклонение и новый план.

#### Request

​	https://api.optionshub.online/api/v1/checkplan?execid= 

#### Params

- ​	?execid=X - уникальный идентификатор плана выполнения для проверки


#### Response

```javascript
{
	status: "ok",
	data: {
		
	}
}
```



## Trading service [PRIVATE]

HTTPS Endpoint: https://api2.optionshub.online/v2/

**Важно!** Сейчас трейдинг настроен на работу с тестовым аккаунтом одной биржи - Deribit, на тесте доступен  начальный баланс 100ETH. 

Трейдин не требует никакой дополнительной авторизации, поэтому ситуация, что сейчас он публично доступен - лишь тест. **НЕ использовать трейдинг сервер в публичном доступе!** 

Исполнение хедж-ордеров поддерживаеться (в текущей версии) только для биржи Deribit (exchange: deribit)



### public/status

Проверка статуса работы сервиса (включая проверку статуса биржи)

#### Request

​	https://api2.optionshub.online/v2/public/status

#### Params

​	-

#### Response

```javascript
{
    "status": "ok",
    "data": {
        "status": "ok",
        "updated": 1631094888038
    }
}
```



### private/balance

​	Возвращает текущий биржевой баланс аккаунта (свободные средства и залоченые в открытых позициях)

#### Request 

​	https://api2.optionshub.online/v2/private/balance

#### Response

```javascript
{
    "status": "ok",
    "data": {
        "depositCurrency": "ETH",	//валюта депозита, сейчас только ETH
        "total": 100,	//всего баланс аккаунта
        "marginUsed": 0,	//использовано (залочено) в марже открытых позиций
        "marginFree": 100,	//доступно для использования в открытых позициях
        "free": 100,	//свободные средства аккаунта (доступные для вывода)
        "updatedAt": "2021-09-08T09:59:42.129Z"
    }
}
```



### private/position

​	Отображает текущие открытые позиции по инструментам

#### Request

​		https://api2.optionshub.online/v2/private/position



Response

```javascript
{
    "status": "ok",
    "data": {
        "ETH-31DEC21-680-P": {	//Инструмент
            "vega": "0.01101",
            "total_profit_loss": "-0.335984622",
            "theta": "-0.0039",
            "size": "3.0",	//Обьем в контрактах
            "settlement_price": "0.000005",
            "realized_profit_loss": "0.0",
            "open_orders_margin": "0.0",
            "mark_price": "0.000005",
            "maintenance_margin": "0.0",
            "kind": "option",
            "instrument_name": "ETH-31DEC21-680-P",
            "initial_margin": "0.0",
            "index_price": "3508.87",
            "gamma": "0.0",
            "floating_profit_loss_usd": "-1165.9",
            "floating_profit_loss": "-0.335984622",
            "direction": "buy",
            "delta": "-0.00014",
            "average_price_usd": "388.65",
            "average_price": "0.112"
        }
    }
}
```



### private/orders/opened

​	Текущие открытые ордера (заявки) по инструментам. Для облегчения дебага и анализа, формат ответа (описание ордера) идентично описанию биржевого апи

#### Request

​	https://api2.optionshub.online/v2/private/orders/opened

#### Response

```javascript
{
    "status": "ok",
    "data": [
        {
            "info": {
                "web": true,	//Индикатор, откуда был ордер, web - руками на сайте биржи, api - программно. Мы НЕ учитываем web ордера, но они отображаються в списке трейдов и ордеров.
                "time_in_force": "good_til_cancelled",
                "replaced": false,
                "reduce_only": false,
                "profit_loss": "0.0",
                "price": "0.3315",	// цена в ETH
                "post_only": false,
                "order_type": "limit",
                "order_state": "open",	//статус ордера
                "order_id": "ETH-1502589192",	//уникальный идентификатор биржи
                "max_show": "1.0",
                "last_update_timestamp": "1631184077519",
                "label": "",	//ВАЖНО! в случае ордеров хедж-системы, здесь будет задан execId, чтобы попасть в учет позиции
                "is_liquidation": false,
                "instrument_name": "ETH-24JUN22-3000-C", //Код инструмента
                "filled_amount": "0.0", //Какая часть ордера уже выполнена
                "direction": "buy", //Покупка или продажа
                "creation_timestamp": "1631184077519",
                "commission": "0.0",
                "average_price": "0.0",
                "api": false, // индикатор, указывает что ордер был размещен через апи
                "amount": "1.0" //обьем ордера в контрактах (для ETH - всегда целое число)
            },
            "id": "ETH-1502589192",
            "timestamp": 1631184077519,
            "datetime": "2021-09-09T10:41:17.519Z",
            "symbol": "ETH-24JUN22-3000-C",
            "type": "limit",
            "timeInForce": "GTC",
            "postOnly": false,
            "side": "buy",
            "price": 0.3315,
            "amount": 1,
            "cost": 0,
            "average": 0,
            "filled": 0,
            "remaining": 1,
            "status": "open",
            "fee": {
                "cost": 0,
                "currency": "ETH"
            },
            "fees": [
                {
                    "cost": 0,
                    "currency": "ETH"
                }
            ]
        }
    ]
}
```





### private/trades

​	Возвращает историю выполненных ордеров (последние 100)

#### Request

​	https://api2.optionshub.online/v2/private/trades

#### Response

```javascript
{
    "status": "ok",
    "data": [
        {
            "id": "ETH-11058772",	//Идентификатор конкретного трейда
            "info": {
                "underlying_price": "3473.68",
                "trade_seq": "13",
                "trade_id": "ETH-11058772",
                "timestamp": "1631185160102",
                "tick_direction": "1",
                "state": "filled",	//статус трейда - полностью выполнен
                "self_trade": false,
                "reduce_only": false,
                "profit_loss": "0.0",
                "price": "0.112",
                "post_only": false,
                "order_type": "limit",
                "order_id": "ETH-1502605949",
                "matching_id": null,
                "mark_price": "0.000006",
                "liquidity": "T",
                "iv": "445.85",
                "instrument_name": "ETH-31DEC21-680-P",
                "index_price": "3470.09",
                "fee_currency": "ETH",
                "fee": "0.00029",
                "direction": "buy",
                "amount": "1.0"
            },
            "timestamp": 1631185160102,
            "datetime": "2021-09-09T10:59:20.102Z",
            "symbol": "ETH-31DEC21-680-P",	//Инструмент
            "order": "ETH-1502605949",	//Идентификатор ордера, в рамках которого был совершен трейд
            "type": "limit",
            "side": "buy",
            "takerOrMaker": "taker",
            "price": 0.112,	//цена
            "amount": 1,	//количество
            "cost": 0.112,
            "fee": {	//комиссия за сделку
                "cost": 0.00029,
                "currency": "ETH"
            }
        },
        {
            "id": "ETH-11058771",
            "info": {
                "underlying_price": "3473.68",
                "trade_seq": "12",
                "trade_id": "ETH-11058771",
                "timestamp": "1631185160102",
                "tick_direction": "1",
                "state": "filled",
                "self_trade": false,
                "reduce_only": false,
                "profit_loss": "0.0",
                "price": "0.112",
                "post_only": false,
                "order_type": "limit",
                "order_id": "ETH-1502605949",
                "matching_id": null,
                "mark_price": "0.000006",
                "liquidity": "T",
                "iv": "445.85",
                "instrument_name": "ETH-31DEC21-680-P",
                "index_price": "3470.09",
                "fee_currency": "ETH",
                "fee": "0.00029",
                "direction": "buy",
                "amount": "1.0"
            },
            "timestamp": 1631185160102,
            "datetime": "2021-09-09T10:59:20.102Z",
            "symbol": "ETH-31DEC21-680-P",
            "order": "ETH-1502605949",	//ИД ордера, это второй трейд в рамках одного ордера
            "type": "limit",
            "side": "buy",
            "takerOrMaker": "taker",
            "price": 0.112,
            "amount": 1,
            "cost": 0.112,
            "fee": {
                "cost": 0.00029,
                "currency": "ETH"
            }
        },
        ...
    ]
}
```



### public/realprice [TBD]

​	Переоценка указанного инструмента и объема. Аналогично /rfq в публичном API. 



### private/hedgestatus

​	Возвращает полное описание текущего хеджа и статуса отдельных операций (включая ордера, как исполненные, так и открытые, трейды и исходный хедж).

​	Важно! Ответ содержит структуру аналогичную возвращемую методом execute, но она обновляеться, когда сделки совершаються и т.п.

#### Params

- ?execid - идентификатор хедж-плана

#### Request

​		https://api2.optionshub.online/v2/private/hedgestatus?execid=<5b8e2ebd-a9d8-4c6a-b6b7-f7bb2de9b5e3>

#### Response

```javascript
{
    "status": "ok",
    "data": {
        "execId": "5b8e2ebd-a9d8-4c6a-b6b7-f7bb2de9b5e3",
        "timestamp": 1631187418387,
        "exchange": "deribit",
        "hedge": {
            "executionPlan": [
                {
                    "exchange": "deribit",
                    "id": "ETH-31DEC21-1400-P",
                    "direction": "buy",
                    "order": "limit",
                    "price": 0.0005,
                    "amount": 3,
                    "total": 0.0015,
                    "fee": 0.00047,
                    "feeUSD": 1.217087,
                    "spot": 2590.65,
                    "contracts": 0,
                    "balance": 0,
                    "timestamp": 1627905600136
                }
            ],
            "requestedAmount": 3,
            "closedAmount": 3,
            "unclosedAmount": 0,
            "totalAmount": 3,
            "totalVol": 0.0005,
            "totalFee": 0.00047,
            "totalFeeUSD": 1.217087,
            "totalPriceMean": 0.261,
            "priceLimit": 0.001,
            "serviceTradingFee": 0.1,
            "serviceDeliveryFee": 0.25,
            "totalServiceFee": 0.003654,
            "execId": "5b8e2ebd-a9d8-4c6a-b6b7-f7bb2de9b5e3",
            "clientId": "5b8e2ebd-a9d8-4c6a-b6b7-f7bb2de9b5e2",
            "_info": null
        },
        "openOrders": {
            "ETH-1502640913": {
                "web": false,
                "time_in_force": "good_til_cancelled",
                "replaced": false,
                "reduce_only": false,
                "profit_loss": "0.0",
                "price": "0.0005",
                "post_only": false,
                "order_type": "limit",
                "order_state": "open",
                "order_id": "ETH-1502640913",
                "max_show": "3.0",
                "last_update_timestamp": "1631187435477",
                "label": "5b8e2ebd-a9d8-4c6a-b6b7-f7bb2de9b5e3",
                "is_liquidation": false,
                "instrument_name": "ETH-31DEC21-1400-P",
                "filled_amount": "0.0",
                "direction": "buy",
                "creation_timestamp": "1631187435477",
                "commission": "0.0",
                "average_price": "0.0",
                "api": true,
                "amount": "3.0"
            }
        },
        "orders": {
            "ETH-1502640913": {
                "web": false,
                "time_in_force": "good_til_cancelled",
                "replaced": false,
                "reduce_only": false,
                "profit_loss": "0.0",
                "price": "0.0005",
                "post_only": false,
                "order_type": "limit",
                "order_state": "open",
                "order_id": "ETH-1502640913",
                "max_show": "3.0",
                "last_update_timestamp": "1631187435477",
                "label": "5b8e2ebd-a9d8-4c6a-b6b7-f7bb2de9b5e3",
                "is_liquidation": false,
                "instrument_name": "ETH-31DEC21-1400-P",
                "filled_amount": "0.0",
                "direction": "buy",
                "creation_timestamp": "1631187435477",
                "commission": "0.0",
                "average_price": "0.0",
                "api": true,
                "amount": "3.0",
                "trades": []
            }
        },
        "trades": {}, //массив трейдов ассоциированный с этим хеджем
        "positions": {	//Текущая оценка позиции (конкретных опционов), важно: БЕЗ учетов комиссий
            "ETH-24SEP21-1400-P": {	
                "symbol": "ETH-24SEP21-1400-P",	//инструмент
                "amount": 1,	//количество контрактов
                "baseCost": 0.8455, //средняя цена покупки (по трейдам)
                "currCost": 0.8455, //текущая оценка позиции (текущая цена * amount)
                "currPrice": 0.8455, //текущая цена (переоценка по market/last/mark price)
                "recalcAt": "2021-09-17T09:07:16.013Z" //дата переоценки
            }
        },
        "currentPrice": null, //Deprecated
        "settlment": null	//Not implemented now
    }
}
```



### private/toggletrading

​	Включение/отключение дальнейших торговых операций (не затрагивая текущие открытые ордера). 

​	**Важно!** После старта сервиса (или рестарта), по дефолту возможность трейдинга ВЫКЛЮЧЕНА

#### Request

​		https://api2.optionshub.online/v2/private/toggletrading

#### Response

```javascript
{
    "status": "ok",
    "data": {
        "tradestatus": true	//текущий статус торгов (true: торги РАЗРЕШЕНЫ, false: торги ЗАПРЕЩЕНЫ)
    }
}
```



### private/execute

​	Принимает в теле POST-запроса объект hedge-plan-а, сформированный ранее /delivery/hedge и после проверок пытается выполнить его на бирже. 

​	**Важно!** Сейчас поддерживаеться только биржа Deribit! 

​	**Важно!** Метод не использует дополнительную авторизацию, поэтому при наличии средств будет выполнен любой отправленный хедж-план, если формально он правильный. 

​	**Важно!** На сейчас размер одного хеджа ограничен 100 ETH. Сделки на большую сумму будут отклонены.

​	**Важно!** **Метод синхронный!** В случае несоответствия формата или других пре-трейд проверок, он сразу вернет ошибку. Если все проверки внутри хедж-сервиса прошли, он дополнительно проверяет статус биржи и доступность торгов. если биржа доступна, сервис заносит информацию о новом выполнении во внутренний сторадж, вызивает синхронную запись на диск для надежности, и только после сохранения отправляет ордер (ордера) на биржу. Сервис синхронно ожидает от биржи ответ - сообщение про ошибку или информацию про ордер и статус его выполнения. В большинстве случаев, ответ будет содержать сразу биржевой ордер и трейды, то есть ордер будет выполнен в рамках одного запроса. Лишь ПОСЛЕ получения ответа от биржи, запрос вернет результат пользователю (ордер и, возможно, трейды).  Поэтому задержка в ответе до 10 секунд вполне нормально и штатно (особенно если работа с test-контуром биржи Deribit)

#### Params

​	В теле POST запроса ожидаеться JSON-обьект, сформированный сервисом /delivery/hedge или актуализированный через checkplan

```javascript
{
    "execId": "c0808273-5078-4db1-bf4d-4ce5af2cc9bf",
    "executionPlan": [
       {
           "exchange": "deribit",
           "id": "ETH-24JUN22-3000-C",
           "direction": "sell",
           "order": "limit",
           "price": 0.308,
           "amount": 1,
           "total": 0.308,
           "fee": 0.000092,
           "feeUSD": 0.304124,
           "spot": 3291.39,
           "contracts": 1,
           "balance": 0,
           "timestamp": 1629115190389
        }
      ],
      "requestedAmount": 1,
      "closedAmount": 1,
      "unclosedAmount": 0,
      "totalAmount": 1,
      "totalVol": 0.308,
      "totalFee": 0.000092,
      "totalFeeUSD": 0.304124,
      "totalPriceMean": 0.308,
      "priceLimit": 0.2926,
      "serviceTradingFee": 0.1,
      "serviceDeliveryFee": 0,
      "totalServiceFee": 0.000308,
      "clientId": "845405d5-892e-415f-9684-31933c41cf73",
      "_info": null
}
```



#### Response

##### Errors

```
{
    "status": "error",
    "message": "Trading stopped immediately",
    "data": {}
}
```



```javascript
{
    "status": "error",
    "message": "Invalid data field: requestedAmount (must be > 0 and  < 100",
    "data": {}
}
```



```javascript
{
    "status": "error",
    "message": "Invalid symbol id: ETH-20AUG21-1400-P", //мы пробуем исполнить хедж с опционом который уже закрыт
    "data": {}
}
```



##### OK response 

```javascript
{
    "status": "ok",
    "data": {
        "execId": "5b8e2ebd-a9d8-4c6a-b6b7-f7bb2de9b5e3",	//Идентификатор хеджа
        "timestamp": 1631187418387,
        "exchange": "deribit",	//идентификатор биржи где была исполнена сделка
        "hedge": {	//начальные параметры хеджа
            "executionPlan": [
                {
                    "exchange": "deribit",
                    "id": "ETH-31DEC21-1400-P",
                    "direction": "buy",
                    "order": "limit",
                    "price": 0.0005,
                    "amount": 3,
                    "total": 0.0015,
                    "fee": 0.00047,
                    "feeUSD": 1.217087,
                    "spot": 2590.65,
                    "contracts": 0,
                    "balance": 0,
                    "timestamp": 1627905600136
                }
            ],
            "requestedAmount": 3,
            "closedAmount": 3,
            "unclosedAmount": 0,
            "totalAmount": 3,
            "totalVol": 0.0005,
            "totalFee": 0.00047,
            "totalFeeUSD": 1.217087,
            "totalPriceMean": 0.261,
            "priceLimit": 0.001,
            "serviceTradingFee": 0.1,
            "serviceDeliveryFee": 0.25,
            "totalServiceFee": 0.003654,
            "execId": "5b8e2ebd-a9d8-4c6a-b6b7-f7bb2de9b5e3",
            "clientId": "5b8e2ebd-a9d8-4c6a-b6b7-f7bb2de9b5e2",
            "_info": null
        },
        "openOrders": {	//Массив открытых ордеров по этому хеджу
            "ETH-1502640913": {
                "web": false,
                "time_in_force": "good_til_cancelled",
                "replaced": false,
                "reduce_only": false,
                "profit_loss": "0.0",
                "price": "0.0005",
                "post_only": false,
                "order_type": "limit",
                "order_state": "open",
                "order_id": "ETH-1502640913",
                "max_show": "3.0",
                "last_update_timestamp": "1631187435477",
                "label": "5b8e2ebd-a9d8-4c6a-b6b7-f7bb2de9b5e3",
                "is_liquidation": false,
                "instrument_name": "ETH-31DEC21-1400-P",
                "filled_amount": "0.0",
                "direction": "buy",
                "creation_timestamp": "1631187435477",
                "commission": "0.0",
                "average_price": "0.0",
                "api": true,
                "amount": "3.0",
                "trades": []
            }
        }, 
        "orders": { //Массив всех ордеров по сделке (и открытых и выполненных)
            "ETH-1502640913": {
                "web": false,
                "time_in_force": "good_til_cancelled",
                "replaced": false,
                "reduce_only": false,
                "profit_loss": "0.0",
                "price": "0.0005",
                "post_only": false,
                "order_type": "limit",
                "order_state": "open",
                "order_id": "ETH-1502640913",
                "max_show": "3.0",
                "last_update_timestamp": "1631187435477",
                "label": "5b8e2ebd-a9d8-4c6a-b6b7-f7bb2de9b5e3",
                "is_liquidation": false,
                "instrument_name": "ETH-31DEC21-1400-P",
                "filled_amount": "0.0",
                "direction": "buy",
                "creation_timestamp": "1631187435477",
                "commission": "0.0",
                "average_price": "0.0",
                "api": true,
                "amount": "3.0",
                "trades": []
            }
        },
        "trades": {},  //Выполненные сделки (трейды) по хеджу
        "positions": {}, // Позиция (купленные активы)
        "currentPrice": null,
        "settlment": null
    }
}
```



#### **Issues**

​	В текущей версии сервис запоминает все hedge-планы, которые прошли входной контроль и были отправлены на биржу, даже если биржа отвергла сделку, повторно отправить план с таким же идентификатором hedgeId нельзя.



### /private/sell

​	Универcальный метод закрытия хедж-позиции или индивидуальных позиций в ручном режиме. 

​	**Важно!** Закрытие позиций в ручном режиме нарушает связность системы! Используйте на свой страх и риск! Корректная работа возможно только с позициями, открытыми вручную или не относящимися к одному из хедж-планов.

​	**Важно!** Для обеспечения безопасности, все ордера на продажу выполняються ТОЛЬКО с флагом reduce_only - это значит, что продажа возможна ТОЛЬКО ранее купленных активов (и в таком режиме ордер не лочит маржу на аккаунте). Этот параметр нельзя установить, он захардкожен в коде.



#### Request

​	https://api2.optionshub.online/v2/private/sell

#### Params

- **?execid** - указатель на хедж-план, который следует продать. Если указан ид, то остальные параметры игнорируються. 
- ?symbol - биржевой код инструмента, который будет продан, например, ETH-24SEP21-600-C 
- ?amount=<int> - количество контрактов к продаже. Минимально 1, максимум равен текущей позиции  по инструменту (общей)
- ?price=<float> - вручную заданная цена продажи (в ETH). Может быть отклонена биржей как выходящей за пределы отклонения от mark price.
- **?closeopened**=1|0 - указывает, нужно ли отменять текущие открытые ордера по этому инструменту. Если задан  execid - будут отменены только ордера привязанные к плану, иначе - ВСЕ ордера по этому инструменту. Единственный параметр который НЕ игнорируеться при execid
- ?sellall=1|0 - указывает, продавать ли всю позицию, или только указанный amount. Игнорируеться, если задан execid



#### Примеры

​	https://api2.optionshub.online/v2/private/sell?execid=AAA&closeopened=0

​	Закрывает хедж-позицию AAAA но НЕ отменяет текущие открытые ордера

​	https://api2.optionshub.online/v2/private/sell?symbol=ETH-24SEP21-600-C&amount=3&price=0.012

​	Продать 3 опциона ETH-24SEP21-600-C ценой 0.012 ETH за контракт. 

​	https://api2.optionshub.online/v2/private/sell?symbol=ETH-24SEP21-600-C&amount=3

​	Продать 3 опциона ETH-24SEP21-600-C, цену определить автоматически. 

#### Sell price discovery

​	Мы используем следующий алгоритм определения цены:

1. Цена указываеться вручную (параметр price)
2. Лучшая цена покупки на момент сделки (Best Bid price)
3. Последняя цена сделки (Last trade price)
4. Текущая цена оценки (mark price)

​	

#### Response

```json
{
    "status": "ok",
    "data": {
        "realReturnedAmount": 0,	//Сумма, которая вернулась после операции в пул свободных средств аккаунта (ETH)
        "estimateReturnedAmount": 0.224, //Грубое предположение о том, сколько еще мы можем получить (без учета комиссий)
        "orders": [ //Массив ордеров, которые реализуют продажу
            {
                "info": {
                    "web": false,
                    "time_in_force": "good_til_cancelled",
                    "replaced": false,
                    "reduce_only": true,
                    "profit_loss": "0.0",
                    "price": "0.112",
                    "post_only": false,
                    "order_type": "limit",
                    "order_state": "open",
                    "order_id": "ETH-1513802978",
                    "max_show": "2.0",
                    "last_update_timestamp": "1631868062013",
                    "label": "hedge/manual",
                    "is_liquidation": false,
                    "instrument_name": "ETH-31DEC21-680-P",
                    "filled_amount": "0.0",
                    "direction": "sell",
                    "creation_timestamp": "1631868062013",
                    "commission": "0.0",
                    "average_price": "0.0",
                    "api": true,
                    "amount": "2.0",
                    "trades": []
                },
                "id": "ETH-1513802978",
                "timestamp": 1631868062013,
                "datetime": "2021-09-17T08:41:02.013Z",
                "symbol": "ETH-31DEC21-680-P",
                "type": "limit",
                "timeInForce": "GTC",
                "postOnly": false,
                "side": "sell",
                "price": 0.112,
                "amount": 2,
                "cost": 0,
                "average": 0,
                "filled": 0,	//Исполненный обьем
                "remaining": 2,	//Обьем, которые осталось исполнить
                "status": "open",	//Статус исполнения ордера
                "fee": {
                    "cost": 0,
                    "currency": "ETH"
                },
                "trades": [],	//если сделка была по рынку, то здесь будут трейды, которые УЖЕ исполнены
                "fees": [
                    {
                        "cost": 0,
                        "currency": "ETH"
                    }
                ]
            }
        ]
    }
}
```



realReturnedAmount содержит реально полученные средства (ETH), которые уже на счету (балансе). Это - или маржа, залоченная в открытых ордерах, которые были закрыты и/или средства по сделкам, если ордер был сразу исполнен, полностью или частично. 



### private/ui [DEPRECATED]

Служебный метод, возвращающий всю информацию сразу одним запросом про весь стейт. Отдельные поля имеют идентичную структуру как и соответствующие им ендпоинты.

**Важно!** Несмотря на то, что стейт храниться в памяти сервиса и не нагружает БД, метод требовательный к памяти и каналу. Рекомендуем частоту опроса этого ендпоинта 1 - 2 раза в минуту. 

#### Request

​	https://api2.optionshub.online/v2/private/ui

#### Response

```json
{
    "status": "ok",
    "data": {
        "updatedAt": "2021-09-20T08:19:17.273Z",
        "tradestatus": true,	//Статус трейдинга
        "position": {},	//Позиции по опционам
        "balance": {},	//Биржевой баланс
        "openorders": [], //Открытые ордера
        "index": {}, // Текущий индекс (ETH-USD)
        "hedge": {} // все текущие хеджы
    }
}        
```



### private/hedgeprices

​	Возвращает общую оценку каждого хедж-плана (открытых позиций по этому плану) на текущий момент, согласно рынку. По сути, это эстимейт (верхняя планка, без учета открытых ордеров и коммисий), сколько ETH можно получить, реализуя эту позицию.

#### Params

- ​	execid=<ID> - опционально задает конкретный ид плана, которых оцениваем

#### Request

​	https://api2.optionshub.online/v2/private/hedgeprices

#### Response

```json
{
    "status": "ok",
    "data": {
        "5b8e2ebd-a9d8-4c6a-b6b7-f7bb2de9b5e2": {	//hedge id
            "baseCost": 0,	
            "currCost": 0,
            "assets": [],
            "recalcAt": "2021-09-22T10:56:44.183Z"
        },
        "5b8e2ebd-a9d8-4c6a-b6b7-f7bb2de9b5e3": {	//hedge id
            "baseCost": 0.8455,	//Total base cost (how much we pay)
            "currCost": 0.8455,	//Total current estimate for cost
            "assets": [	//List of assets
                "ETH-24SEP21-600-C"
            ],
            "recalcAt": "2021-09-22T10:56:44.183Z"	//ISO date
        }
    }
}
```



### private/hedgestoclose

​	Возвращает список hedgeId планов, которые нужно закрыть (продать). Внутреняя логика использует время до экспирации (сейчас это 3 дня), в будущем добавиться другие стратегии. 

#### Request

​	https://api2.optionshub.online/v2/private/hedgestoclose

#### Response	

```json
{
    "status": "ok",
    "data": [
        "5b8e2ebd-a9d8-4c6a-b6b7-f7bb2de9b5e2",
        "5b8e2ebd-a9d8-4c6a-b6b7-f7bb2de9b5e3"
    ]
}
```



### private/state/backup

​	Служебный метод, возвращает глобальный стейт всех планов. Используеться для бекапа во внешнюю систему. В случае полного краха системы содержимое рузультата надо записать в файл стейта - /tmp/optionshub.hedgestore.snapshot и запустить сервис.

#### Request

​	https://api2.optionshub.online/v2/private/state/backup

#### Response

```json
{
    //большой JSON - стейт всего
}
```



## WebSocket

Для работы доступно три метода:

- **get** - однократное получение результата (аналог REST-запроса)
- **subscribe** - постоянное получение обновлений. Первым идет снапшот текущего состояния, в дальнейшем - обновленный вариант. Важно - Incremental updates не поддерживаются сейчас.
- **unsubscribe** - прекращает подписку. Никакого ответа не предусмотрено (или стандартный пустой ответ).

В рамках одного запроса можно совершить только один метод, но в качестве параметра можно передать множество отдельных команд. Все они будут выполнены в той же очередности, как указаны в запросе. Ответы будут индивидуально на каждый запрос. 

Частота обновлений (команда subscribe) зависит от метода. 

#### Limitation

Размер одного запроса (списка команд) желательно ограничить ~ 100Kb

Максимальный размер ответа сервера (одного ответа) - 2 Mb. 

Параметры всех запросов идентичны REST-методам

В ряде методов для подписки, вместо параметра symbol допускается использовать '*' как паттерн. Однако будьте внимательны, это может очень нагрузить клиент и сервер, так как количество обновлений и размер данных может быть очень большим (теоретически, до нескольких сотен сообщений в секунду). 

WS интерфейс НЕ поддерживает трейдинг (API2 private)
