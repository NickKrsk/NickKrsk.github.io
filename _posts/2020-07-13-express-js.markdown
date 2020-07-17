---
layout: post
title:  "Express JS"
date:   2020-07-13 12:55:20 +0700
categories: nodeJS
---

**Тестирование**
Supertest — это библиотека, созданная исключительно с целью проводить функциональное тестирование http интерфейса.

```
// __tests__/app.test.js
import request from 'supertest';
import app from '../src/app.js';

test('request', async () => {
  const res = await request(app).get('/');
  if (res.error) {
    throw error;
  }
  expect(res.status).toBe(200);
});
```

В основе библиотeки supertest лежит другая библиотека - superagent. Именно она позволяет формировать произвольные запросы к серверу.

```
import request from 'superagent';

const res = await request
   .post('/api/pet')
   .type('form')
   .send({ name: 'Manny', species: 'cat' })
   .set('X-API-Key', 'foobar')
   .set('Accept', 'application/json');
```

https://github.com/visionmedia/superagent
https://github.com/visionmedia/supertest

**Логирование**
Правильная отладка — это хорошо настроенное логирование.

В javascript особой популярностью пользуется библиотека morgan.

```
import Express from 'express';
import morgan from 'morgan';

const app = new Express();
const logger = morgan('combined');
app.use(logger);
```
https://github.com/expressjs/morgan

Так сложилось, что 99% самых популярных библиотек на js уже поставляются со встроенным логированием через debug. По сути, нам не очень-то и оставили выбор. На картинке выше как раз видно кусок лога express.
https://github.com/visionmedia/debug#readme



**Интеграция pug с express:**


[Шаблонизатор PUG](https://pugjs.org/api/getting-started.html)


```
// npm install pug
app.set('view engine', 'pug');

app.get('/', (req, res) => {
  const data = { title: 'Hey', message: 'Hello there!' };
  res.render('index', data);
})
// index.pug
html
  head
    title= title
  body
    h1= message
```

**Шаблоны и чистота**

1. Ни в коем случае шаблон не должен порождать побочных эффектов. В шаблоне нельзя писать в базу, изменять данные на диске и вообще любым способом пытаться влиять на окружающую среду. **Шаблон это исключительно чтение.**


2. Шаблоны должны быть декларативны, никакого изменения состояний, другими словами, если вы начинаете внутри шаблона вводить переменные и изменять их, то, по сути, шаблон превращается в полноценный скрипт, который вы программируете. Не допускайте этого.


3. Использовать логику, влияющую на вывод внутри шаблона — это нормально. Если у вас, с точки зрения ui, блок показывается по определённому условию, то вы не сможете этого избежать, единственное о чем нужно помнить, это создавать вовремя правильные абстракции (функции) для избежания дублирования, а так же для выделения бизнес-правил.


**Технологии**

**REST**

Требование REST:

Единообразие интерфейса

Кэширование

Отсутствие состояния

Клиент-серверная архитектура

Слои

Код по требованию (необязательное ограничение)



Дейстие в REST определяется глаголом (GET, POST, DELETE ...)

**JSONAPI**

[JSONAPI](https://jsonapi.org/)



**Middleware**

[Connect](https://github.com/senchalabs/connect)

connect - ядро микрофреймворка express.

Connect представляет из себя механизм, который расширяется функциями, называемыми middleware. Каждый раз, когда мы используем use, очередная middleware добавляется в общую очередь. В конечном счёте получается объект, наполненный мидлварами. Каждый запрос, отправляемый на обработку в connect, проходит через цепочку этих middleware пока не наткнётся на терминальную мидлвару.

В свою очередь каждая мидлвара принимает на вход три параметра: request, response и next. Она может поменять их и в конце должна вызвать next для передачи управления следующей по списку мидлваре. В этом и заключается вся мощь микрофреймворков. Удачный дизайн позволяет легко разбивать систему на модули-мидлвары и расширять за счёт мидлвар, которые, в большом количестве, пишут сторонние разработчики.

```
app.use((req, res, next) => {
  req.newProperty = 'hello from my middleware';
  next();
});

// вызов methodOverride возвращает функцию вида (req, res, next) => ...
app.use(methodOverride('_method'));
В Connect нет ничего кроме метода use добавляющего очередную мидлвару в стек.
```


**Mount middleware**

Самое интересное в Connect, что обработчики конкретных маршрутов — это тоже, всего навсего, мидлвары. Их особенностью является привязка к конкретному маршруту, в отличие от мидлвар, которые выполняются для всех запросов.

```
app.use('/foo', (req, res, next) => {
  // req.url starts with "/foo"
  next();
});

app.use('/bar', (req, res, next) => {
  // req.url starts with "/bar"
  next();
});
```
Такие мидлвары позволяют реализовывать базовый роутинг без привязки к конкретному глаголу http и без поддержки динамических маршрутов. В Express роутинг реализован без привязки к Mount Middlewares.