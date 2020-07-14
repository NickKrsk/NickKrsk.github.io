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


https://pugjs.org/api/getting-started.html


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