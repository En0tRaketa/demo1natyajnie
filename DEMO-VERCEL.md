# Публикация статического демо

`potolok-profi-static-demo.zip` — самостоятельная демо-версия сайта: только HTML, CSS, JS, шрифты и картинки. Она выглядит так же, как рабочий сайт, а калькулятор из 5 шагов считает по тем же правилам. Но в демо:

- нет PHP, базы данных и обращений к серверу или внешним API;
- нет токенов, паролей и настроек рабочего сайта (проверяется при сборке);
- **заявки не сохраняются и не отправляются**: форма честно показывает «Демонстрационный режим: заявка не отправлена»;
- админки нет: `/admin` показывает заглушку «Админка недоступна в демо»;
- сайт закрыт от поисковиков: `<meta name="robots" content="noindex, nofollow">`, `robots.txt` с `Disallow: /` и заголовок `X-Robots-Tag` в `vercel.json` и `_headers`.

```
potolok-profi-static-demo.zip
├── index.html, assets/, images/, favicon.svg
├── robots.txt        Disallow: /
├── vercel.json       заголовки (noindex, CSP) и адреса /privacy, /admin — для Vercel
├── _headers          то же для Netlify и Cloudflare Pages
└── _redirects        адреса /privacy, /admin — для Netlify и Cloudflare Pages
```

## Важно: условия бесплатных тарифов

Демо, которое показывают компаниям, чтобы продать им сайт, — это **коммерческое использование**.

- **Vercel Hobby (бесплатный)** — только для личного некоммерческого использования. Правила Vercel прямо относят к коммерческому использованию «рекламу продажи товара или услуги» и любую финансовую выгоду участников проекта. Для продающего демо нужен **Vercel Pro** (платный). Источники: [Vercel — Hobby plan](https://vercel.com/docs/plans/hobby), [Vercel — Fair use guidelines, Commercial usage](https://vercel.com/docs/limits/fair-use-guidelines) (страницы обновлены 14.09.2026).
- **Netlify Free** — на форуме Netlify отвечали, что бесплатный план можно использовать для коммерческих проектов. Нельзя перепродавать сам хостинг, но можно брать деньги за создание сайта ([ответ на форуме Netlify, 2021](https://answers.netlify.com/t/can-we-use-netlify-free-plan-for-commercial-purposes/41545/2)). Ответ давний — перед публикацией проверьте актуальные условия.
- **Cloudflare Pages** — условия использования проверьте в документации Cloudflare перед публикацией.
- **Самый простой вариант без вопросов к условиям:** положите демо на поддомен вашего же платного хостинга, например `demo.ваш-домен.ru` на REG.RU. Это обычные статические файлы, PHP для них не нужен.

## Вариант 1. Поддомен на REG.RU (рекомендуется)

1. ISPmanager → **Сайты** → **Создать** → `demo.ваш-домен.ru`.
2. Менеджер файлов → папка поддомена → загрузите архив и распакуйте.
3. Чтобы работали адреса `/privacy` и `/admin`, создайте в той же папке `.htaccess`:
   ```apache
   Options -Indexes
   RewriteEngine On
   RewriteRule ^(privacy|admin)/?$ index.html [L]
   Header set X-Robots-Tag "noindex, nofollow"
   ```
4. Выпустите бесплатный SSL (Let's Encrypt) для поддомена.

## Вариант 2. Vercel (тариф Pro для коммерческого показа)

**Через сайт (Git).**
1. Создайте пустой репозиторий на GitHub и загрузите в него **содержимое** архива: `index.html` должен лежать в корне.
2. vercel.com → **Add New… → Project** → импортируйте репозиторий.
3. Настройки сборки:
   - **Framework Preset:** `Other`;
   - **Build Command:** оставьте пустым (или выключите переключатель);
   - **Output Directory:** `.` (корень).
4. **Deploy.** Настройки из `vercel.json` (noindex, CSP, адреса `/privacy`, `/admin`) применятся автоматически.

**Через командную строку** (на своём компьютере, если установлен Node.js):
```bash
npx vercel deploy --prod   # из распакованной папки демо
```

## Вариант 3. Netlify или Cloudflare Pages

- **Netlify:** app.netlify.com → **Add new site → Deploy manually** → перетащите папку с распакованным демо. Файлы `_headers` и `_redirects` учтутся автоматически.
- **Cloudflare Pages:** **Workers & Pages → Create → Pages → Upload assets** → загрузите папку. `_headers` и `_redirects` тоже поддерживаются.

## Проверка после публикации

- [ ] Открывается главная, калькулятор проходит все 5 шагов.
- [ ] Отправка формы показывает жёлтое уведомление о демо-режиме, а не «заявка отправлена».
- [ ] `/privacy` открывается, `/admin` показывает заглушку.
- [ ] В коде страницы есть `noindex, nofollow`, а `/robots.txt` содержит `Disallow: /`.
- [ ] На телефоне нет горизонтальной прокрутки.

## Как пересобрать демо

После изменения дизайна, текстов по умолчанию или картинок соберите демо заново в исходном проекте:
```bash
npm install
npm run build:demo        # → deliverables/potolok-profi-static-demo.zip
```
Демо-данные берутся из `src/config/defaults.ts`, картинки — из `public/images/`: файлы, которых там нет, заменяются иллюстрациями.
