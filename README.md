
# vkcoin
Враппер для платёжного API VK Coin. https://vk.com/@hs-marchant-api
# Установка
* Скачайте и установите [Python](https://www.python.org/downloads/) версии 3.6 и выше
* Введите следующую команду в командную строку:
```bash
pip install vkcoin
```
* Или, вы можете собрать библиотеку с GitHub:
```bash
pip install git+git://github.com/crinny/vkcoin.git
```
* Вы прекрасны!
# Начало работы
Для начала разработки, необходимо создать в своей папке исполняемый файл с расширением **.py**, например **test.py**. Вы не можете назвать файл vkcoin.py, так как это приведёт к конфликту. Теперь файл нужно открыть и импортировать библиотеку:
```python
import vkcoin
```
Данная библиотека содержит в себе **2 класса**:
- **VKCoinApi** - для работы с API VKCoin-а
- **VKCoinWS** - для получения CallBack сообщения об зачислении коинов

# VKCoinApi
```python
merchant = vkcoin.VKCoinApi(user_id=123456789, key='xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx')
```
|Параметр|Тип|Описание|
|-|-|-|
|user_id|Integer|ID аккаунта ВКонтакте|
|key|String|Ключ для взаимодействия с API|
# Методы
Необязательные параметры при вызове функций выделены _курсивом_.

[`get_payment_url`](https://vk.com/@hs-marchant-api?anchor=ssylka-na-oplatu) - получет ссылку на оплату VK Coin
```python
result = merchant.get_payment_url(amount=10, payload=78922, free_amount=False)
print(result)
```
|Параметр|Тип|Описание|
|-|-|-|
|amount|Float|Количество VK Coin для перевода|
|_payload_|Integer|Число от -2000000000 до 2000000000, вернется в списке транзаций|
|_free_amount_|Boolean|True, чтобы разрешить пользователю изменять сумму перевода|
#
[`get_transactions`](https://vk.com/@hs-marchant-api?anchor=poluchenie-spiska-tranzaktsy) - получает список ваших транзакций
```python
result = merchant.get_transactions(tx=[2])
print(result)
```
|Параметр|Тип|Описание|
|-|-|-|
|tx|List|Массив ID переводов для получения или [1] - 1000 последних транзакций со ссылок на оплату, [2] — 100 последних транзакций на текущий аккаунт|
|_last_tx_|Integer|Если указать номер последней транзакции, то будут возвращены только транзакции после указанной|
#
[`send coins`](https://vk.com/@hs-marchant-api?anchor=perevod) - делает перевод другому пользователю
```python
result = merchant.send_coins(amount, to_id)  
print(result)
```
|Параметр|Тип|Описание|
|-|-|-|
|amount|Float|Сумма перевода|
|to_id|Integer|ID аккаунта, на который будет совершён перевод|
#
[`get_user_balance`](https://vk.com/@hs-marchant-api?anchor=poluchenie-balansa) - возвращает баланс аккаунта
```python
result = merchant.get_user_balance(123456789, 987654321)
print(result)
```
|Тип|Описание|
|-|-|
Integer|ID аккаунтов, баланс которых нужно получить|
#
`get_user_balance` - возвращает ваш баланс
```python
result = merchant.get_my_balance()
print(result)
```

# VKCoinWS _(CallBack)_
**VKCoin** для взаимодействия между клиентом и сервером использует протокол WebSocket.
Данный класс реализован для получения обратных вызовов при входящих транзакциях на аккаунт, доступ к которому может быть предоставлен одним из следующих параметров при создании объекта класса:
```python
callback = vkcoin.VKCoinWS(token, iframe_link)
```
|Параметр|Тип|Описание|
|-|-|-|
|token|String|acces_token вашего аккаунта **\***|
|iframe_link|String|ссылка на iframe сервиса VKCoin **\*\***|

**\*** получение токена - перейдите по [ссылке](https://vk.cc/9f4IXA), нажмите "Разрешить" и скопируйте часть адресной строки после `access_token=` и до `&expires_in` (85 символов)

**\*\*** эту ссылку можно достать в коде страницы vk.com/coin
1.  Переходим на [vk.com/coin](http://vk.com/coin)
2.  Используем сочетание клавиш ```ctrl + U``` для просмотра исходного кода страницы
3. Открываем поиск, вводим `sign=`
4.  Копируем найденную ссылку, содержащую параметр `sign`

После инициализации объекта необходимо зарегистрировать функцию, которая будет обрабатывать входящие платежи. Для этого используется декоратор `handler`
```python
@callback.handler
def your_func(data):
	do_something...
```
При получении обратного вызова - входящей транзакции - в зарегестрированную функцию возвращается объект класса `Entity`, который является абстракцией входящего перевода и содержит следующие параметры:
```python
data.user_id - # ваш ID
data.balance - # баланс вашего аккаунта 
data.user_from - # ID отправителя (инициатор входящей транзакции)
data.amount - # количество полученных коинов
```


# Примеры
### Callback
```python
import vkcoin

callback = vkcoin.VKCoinWS(token='xxxxxxxxxxxxxxxxxxxxxxxxxxxxx') # либо ссылка на iframe

@callback.handler
def with_transfer(data):
    user_id = data.user_id
    my_balance = data.balance
    sender = data.user_from
    amount = data.amount
	
callback.run_ws()  # запускаем веб-сокет - все входящие платежи 
				   # будут возвращаться в функцию with_transfer

```

# Где меня можно найти
Могу ответить на ваши вопросы
* [ВКонтакте Crinny](https://vk.com/crinny)   or  [ВКонтакте Spooti](https://vk.com/edgar_gorobchuk)
* [Telegram Crinny](https://t.me/truecrinny)  or  [Telegram Spooti](https://t.me/spooti)
* [Чат ВКонтакте по VK Coin API](https://vk.me/join/AJQ1d5eSUQ81wnwgfHSRktCi)
