# Создаем динамический проект на БЭМ

* [Обзор](#Обзор)
* [Шаблонный репозиторий](#Шаблонный-репозиторий)
* [Простейшее приложение Hello, World](#Простейшее-приложение-hello-world)
* [Файловая структура проекта](#Файловая-структура-проекта)
* [Начало работы](#Начало-работы)
* [Верстка](#Верстка)
* [Устранение неполадок](#Устранение-неполадок)

## Обзор

Этот документ посвящен созданию динамического приложения с использованием [БЭМ-платформы](https://ru.bem.info/platform/).

Мы создадим проект (Social Services Search Robot) для поиска подходящих твитов и видео по ключевому слову.

В работе будем руководствоваться и использовать:

* принципы работы с [CSS по БЭМ](https://ru.bem.info/methodology/css/);
* фреймворк [i-bem.js](https://ru.bem.info/platform/i-bem/);
* шаблонизатор [bem-xjst](https://ru.bem.info/platform/bem-xjst/);
* технологию для описания зависимостей [DEPS](https://ru.bem.info/platform/deps/);
* [YouTube Data API](https://developers.google.com/youtube/v3/docs/search/list);
* [Twitter Search API](https://dev.twitter.com/rest/public/search).

После прочтения вы сможете быстро и с небольшими усилиями разрабатывать собственные веб-приложения, ориентированные на динамические данные.

> **Важно** В документе не будут подробно разбираться вопросы верстки и клиентского JS. Цель документа — показать возможноти шаблонизатора `bem-xjst` при разработке динамических приложений.

### Демонстрация

![Social Services Search Robot](ssr.png)

### Целевая аудитория

Документ предназначен для веб-разработчиков знакомых с:

* HTML;
* CSS;
* JavaScript;
* БЭМ.

## Шаблонный репозиторий

Для создания динамических БЭМ-проектов разработан шаблонный репозиторий [bem-express](https://github.com/bem/bem-express). Он содержит необходимый минимум конфигурационных файлов и позволяет быстро развернуть свой проект.

В `bem-express` по умолчанию подключены основные БЭМ-библиотеки:

* [bem-core](https://github.com/bem/bem-core)
* [bem-components](https://github.com/bem/bem-components)

Для начала работы с шаблонным репозиторием на вашей машине необходимо установить [Node.js 4+](https://nodejs.org).

> **Важно** Пользователям операционной системы Windows необходимо дополнительно установить [Git Bash](https://git-for-windows.github.io).

## Простейшее приложение Hello, World

У программистов есть традиция: начинать программирование с приложения "Hello, World".
Это первое приложение, которое мы создадим. После чего модифицируем его до желаемого **Social Services Search Robot**.

Нам понадобится локальная копия `bem-express`. Её можно сделать любым удобным для вас способом. Мы будем использовать [Git](https://git-scm.com).

> **Примечание** Для пользователей OS X или Linux все команды выполняются в терминале. Пользователям Windows необходимо выполнять все команды в Git Bash. Убедитесь, что Git Bash запущен от имени администратора.

1. Склонируйте репозиторий:

  ```bash
  git clone git@github.com:bem/bem-express.git sssr-project
  ```

2. Перейдите в директорию проекта:

  ```bash
  cd sssr-project
  ```

3. Удалите историю версионирования исходного репозитория:

  ```bash
  rm -rf .git
  ```

4. Инициализируйте собственный Git-репозиторий:

  ```bash
  git init
  ```

5. Устанавите зависимости:

  ```bash
  npm install
  ```

  > **Примечание** Не используйте права суперпользователя (`root`) при установке npm- и bower-зависимостей. Bower-зависимости ставятся при выполнении `npm postinstall` в директории `libs`.

6. Соберите проект и запустите сервер:

  ```bash
  npm run dev
  ```

  > **Примечание** Сборка происходит с помощью [ENB](https://ru.bem.info/toolbox/enb/).

  В результате в командной строке вы увидите следующее сообщение:

  `Server is listening on 3000`

  На вашем компьютере запустился:

  * сервер, который будет выполнять код, отвечающий за обработку динамических данных;
  * [nodemon](https://github.com/remy/nodemon/), который будет следить за изменениями на файловой структуре и перезапускать сервер;
  * [chokidar](https://github.com/paulmillr/chokidar), который будет следить за изменениями в директориях `*.blocks/` и перестраивать структуру проекта;
  * [livereload](https://github.com/napcs/node-livereload), который будет обновлять страницу в браузере.

  > **Примечание** Если порт `3000` уже используется другой программой, его можно переназначить в файле `server/config.js`. Например, на `8000`:
  >
  > ```js
  > defaultPort: 8000,
  > ```

7. Откройте браузер и введите адрес [http://localhost:3000](http://localhost:3000).

  Должна открыться страница со следующим контентом:

  ```text
  Index page content
  footer content
  ```

  > **Примечание** Если вы запускаете свое приложение в Windows, то, скорее всего, вы получите уведомление от Браундмауэра Windows. Отключите опцию *Общественные сети* (Public Network), установите опцию *Частные сети* (Private Network) и разрешите доступ.

8. Откройте файл `server/index.js` и внесите следующие изменения в код начинающего строкой `app.get('/', function(req, res)`:

  ```js
  app.get('/', function(req, res) {
      var hello = 'Hello';                // Инициализировали переменную `hello`
      var world = 'World';                // Инициализировали переменную `world`
      render(req, res, {
          view: 'page-index',
          title: 'Main page',
          meta: {
              description: 'Page description',
              og: {
                  url: 'https://site.com',
                  siteName: 'Site name'
              }
          },
          hello: hello,                   // Передали переменную `hello` в data
          world: world                    // Передали переменную `world` в data
      })
  });
  ```

9. Измените код файла `common.blocks/page-index/page-index.bemtree.js`:

  ```js
  block('page-index').content()(function() {
      var data = this.data;
      return data.hello + ', ' + data.world;
  });
  ```

В результате контент страницы изменится на:

```text
Hello, World
footer content
```

Приложение **Hello, World** готово.

## Файловая структура проекта

Локальная копия проекта имеет следующую файловую структуру:

```files
sssr-project/
    .enb/                 # Конфигурационные файлы для сборщика ENB
    common.blocks/        # Базовые реализации блоков
    desktop.bundles/      # Директории бандлов проекта
    development.blocks/   # Блоки, подключаемые в процессе разработки (не для Production)  
    libs/                 # Библиотеки
    node_modules/         # Пакеты
    server/               # Директория с серверным кодом
    static/               # Корневая директория для раздачи статических файлов
    .borschik             # Конфигурация сборщика файлов Borschik
    .bowerrc              # Конфигурация менеджера пакетов Bower
    README.md             # Текстовое описание проекта
    bower.json            # Список зависимостей для Bower
    nodemon.json          # Конфигурация для пакета Nodemon
    package.json          # Описание проекта для npm
```

### Создание страницы

Исходники страниц размещаются в директории `desktop.bundles`. Все страницы создаются по принципу: одна страница — одна директория.

Чтобы создать страницу (например, `hello`) необходимо:

1. Разместить в `desktop.bundles` директорию `hello`.
2. Добавить в нее файл ([декларацию](../../method/declarations/declarations.ru.md)) `hello.bemdecl.js`.
3. Описать декларацию — составить список блоков, элементов и модификаторов, используемых на странице.

  Пример декларации `hello.bemdecl.js`:

  ```js
  exports.blocks = [
    { name: 'header' },
    { name: 'body' },
    { name: 'footer' }
  ];
  ```

4. Создать в `common.blocks` директории перечисленных блоков:

  ```files
  test-project/
      common.blocks/        # Базовые реализации блоков
          header/           # Директории блока header
          body/             # Директории блока body
          footer/           # Директории блока footer
  ```

5. Описать реализации блоков, например:

  ```files
  test-project/
      common.blocks/               # Базовые реализации блоков
          header/                  # Директории блока header
              header.bemtree.js    # Шаблон блока header
          body/                    # Директории блока body
              body.bemtree.js      # Шаблон блока body
          footer/                  # Директории блока footer
              footer.bemtree.js    # Шаблон блока footer
  ```

  Пример шаблона `header.bemtree.js`:

  ```js
  block('header').content()(function() {
      return [
          'header content'
      ];
  });
  ```

  > **Примечание** Подробнее о [синтаксисе шаблонов](https://ru.bem.info/platform/bem-xjst/templates-syntax/).

6. Описать серверную часть в файле `server/index.js`:

  ```js
  app.get('/hello/', function(req, res) {
      render(req, res, {
          view: 'hello',
          title: 'Hello page',
          meta: {
              description: 'Page description',
              og: {
                  url: 'https://site.com/hello',
                  siteName: 'Site name'
              }
          }
      })
  });
  ```

В результате страница `hello` будет доступна по ссылке: [http://localhost:3000/hello/](http://localhost:3000/hello/).

### Декларация БЭМ-сущностей

Как отмечалось ранее, декларация описывается в `bemdecl.js`-файле и представляет собой список БЭМ-сущностей, используемых на странице.

**Пример**

```js
exports.blocks = [
  { name: 'header' },
  { name: 'body' },
  { name: 'footer' }
];
```

В шаблонном репозитории `bem-express` задекларирован только блок `root`:

**Пример**

```js
exports.blocks = [
    { name: 'root' }
];  
```

Такое различие обусловлено описанием в `bem-express` [зависимостей](https://ru.bem.info/platform/deps/), для блоков, которые не отражены в декларации. Таким образом, блок `root` является некой отправной точкой для сборщика. Далее сборка просходит по зависимостям.

Для более легкого восприятия удобно олицетворять зависимости с глаголом **«ЗНАЕТ»**. Проинспектировав `deps.js`-файлы в локальной копии `bem-express`, можно представить, как по зависимостям собираются нужные для страницы БЭМ-сущности:

![sheme](https://rawgit.com/bem-site/bem-method/godfreyd-bem-in-dynamic/articles/start-with-bem-express/scheme.ru.svg)

Данная схема равносильна такой декларации:

```js
exports.blocks = [
  { name: 'header' },
  { name: 'logo' },
  { name: 'body' },
  { name: 'footer' }
];
```

## load()

* [Структура проекта]()
* [Реализация блоков в технологии BEMTREE]()
* [Реализация блоков в технологии DEPS]()



### Структура проекта

Прежде чем мы начнем писать код, необходимо определить основные структурные блоки проекта.

![Social Search Robot](ssr-main-blocks.png)

Структуру страницы составляют следующие блоки:

1. `page`;
2. `header`;
3. `home`;
4. `result`;
5. `footer`.

### Реализация блоков в технологии BEMTREE


#### Блок `page`

*page.bemtree.js*

```js
block('page').content()(function() {
    return [
        {
            block: 'header'
        },
        {
            block: 'home'
        },
        {
            block: 'result'
        },
        {
            block: 'footer'
        }
    ];
});
```

#### Блок `header`

#### Блок `home`

#### Блок `result`

#### Блок `footer`





### Создание блока


### Переопределение библиотечных блоков

### Создание кастомных блоков


## Устранение неполадок

Если ваш код не работает:

* Ищите опечатки. Помните, что язык JavaScript чувствителен к регистру символов.
* Используйте для выявления проблем отладчик JavaScript, например, отладчик в составе набора инструментов Яндекс.Браузера. Начните поиск ошибок в консоли JavaScript.
* Проверьте все ли зависимости указаны.
* Ознакомьтесь с нашим [примером реализации](#).
* Задавайте вопросы на [форуме](https://ru.bem.info/forum/).




Almost the same as [project-stub](https://github.com/bem/project-stub/) but with [BEMTREE](https://en.bem.info/technology/bemtree/) and [Express](http://expressjs.com/).

[![Build Status](https://travis-ci.org/bem/bem-express.svg?branch=master)](https://travis-ci.org/bem/bem-express)

## Installation

```sh
git clone https://github.com/bem/bem-express.git
cd bem-express
npm i
```

## Development

```sh
npm run dev
```
will run initial `enb make` command and then start the server with `nodemon` which will restart it on any server file update. Also `chokidar` will watch for changes in `*.blocks/**` and rebuild the project automatically. Then livereload will update the page in the browser.

You may also set `NO_LIVERELOAD` env variable to switch livereload off:
```sh
NO_LIVERELOAD=1 npm run dev
```

You may also run rebuild manually with `enb make` or with external watcher (e.g. `npm run watch`). To switch off automatic rebuild use `NO_AUTOMAKE` env variable:
```sh
NO_AUTOMAKE=1 npm run dev
```

## Production

```sh
YENV=production enb make
NODE_ENV=production node server
```

## Templating

Templating starts in `root` block which replaces itself with `page` or any other context (if specified as argument to `render` function).

## Pro tips

Run server in dev mode with `NODE_ENV=development` environment variable (`nodemon` will set it for you).

In dev mode

* Add `?json=1` to URL to see raw data
* Add `?bemjson=1` to URL to see BEMJSON generated with BEMTREE templates.
