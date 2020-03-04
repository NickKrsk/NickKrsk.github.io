---
layout: post
title:  " JS: Продвинутое тестирование, заметки"
date:   2020-02-20 12:55:20 +0700
categories: programming
image: 
---

Метчер jest для отлова исключений:

```
test('boom!', () => {
  expect(() => {
    functionWithException(0);
  }).toThrow();
})
```

Главная особенность этого матчера в том, что он принимает на вход функцию, которая вызывается внутри. Благодаря этому, он может самостоятельно отследить появление исключения. Этот код не содержит неявного состояния и лишних проверок, он делает ровно то что нужно делать и не требует от нас слишком много.

Иногда важно не просто поймать исключение, но и убедиться в том, что это ожидаемое исключение. Сделать это можно передав в матчер toThrow() строку, которая должна присутствовать в сообщении исключения.

```
test('boom!', () => {
  expect(() => {
    functionWithException(0);
  }).toThrow('divide by zero');
})
```

Фикстуры:

```
const { promises: fs } = require('fs');
const path = require('path');
const getFunction = require('../functions');

const toHtmlList = getFunction();

// BEGIN (write your solution here)
const formats = ['csv', 'json', 'yml'];
const getFixturePath = (name) => path.join(__dirname, '..', '__fixtures__', name);

let expected;

beforeAll(async () => {
  expected = await fs.readFile(getFixturePath('result.html'), 'utf-8');
});

test.each(formats)('%s', async (format) => {
  const filePath = getFixturePath(`list.${format}`);
  const actual = await toHtmlList(filePath);
  expect(actual).toEqual(expected.trim());
});
// END
```

# Побочные эффекты

Библиотека для генерации данных: [Faker](https://github.com/marak/Faker.js/)

Любое взаимодействие с внешней средой не детерминировано.

Библиотека для создания виртуальной ФС: [mock-fs](https://github.com/tschaub/mock-fs)

# Инверсия зависимостей

[Инверсия зависимостей](https://zen.yandex.ru/media/id/5b1d8642c71a92a91b9ea0ce/vnedrenie-zavisimostei-chto-eto-takoe-i-kak-rabotaet-5d5684d1a06eaf00ac3ad8cd)

Программу можно изменить так, чтобы она вызывала функцию sendEmail не напрямую, а принимала её как параметр:

```
import sendEmail from './emailSender.js';

// Ставим значение по умолчанию, чтобы не пришлось постоянно указывать функцию
const registerUser = (params, send = sendEmail) => {
  const user = new User(params);
  if (user.save()) {
    send('registration', { user });
    return true;
  }
  return false;
}
```

Тест:
```
const fakeSendEmail = (...args) => {
  /* Например письмо можно вывести в лог для удобства отладки */
};

test('registerUser', () => {
  const id = registerUser({ name: 'Mike' }, fakeSendEmail);
  const user = User.find(id)
  expect(user).toHaveProperty('name', 'Mike');
});
```

# Манкипатчинг

[nock](https://github.com/nock/nock) - библиотека для перехвата сетевых запросов. Эта библиотека работает так. Она берет стандартный модуль Node.js http и заменяет внутри него некоторые методы, которые используются разными библиотеками для выполнения HTTP-запросов. Причём не просто заменяет, а позволяет гибко настраивать то, какие запросы и как перехватывать, какие возвращать данные, заголовки и коды ответа.

```
import nock from nock;
import { getPrivateForkNames } from '../src.js'; 

test('getPrivateForkNames', async () => {
  nock(/api\.github\.com/)
    .log(console.log) // для удобства отладки можно удалить, если не нужно
    // get – для GET-запросов, post – для POST-запросов и так далее
    .get(/\/orgs\/hexlet\/repos/)
    .reply(200, [{ fork: true, name: 'one' }, { fork: false, name: 'second' }]);

  const names = await getPrivateForkNames('hexlet');
  expect(names).toEqual(['one']);
});
```

У Nock есть метод, запрещающий любые HTTP-запросы из кода: nock.disableNetConnect(). Рекомендуется вызывать его в начале файла с тестами для предотвращения случайных запросов. 


[Кассеты для Axios](https://github.com/nettofarah/axios-vcr)

# Моки и стабы

Стаб устраняет побочный эффект, чтобы не мешать проверке результата работы кода, например, возврату данных из функции. Мок фокусируется на том, как конкретно работает код, что он делает внутри. При этом чисто технически мок и стаб создаются одинаково, за исключением того, что на мок вешают ожидания, проверяющие вызовы. Это приводит к путанице, потому что часто моками называют стабы. С этим ничего уже не поделать, но для себя всегда пытайтесь понять, о чём идет речь. Это важно, от этого зависит фокус тестов.