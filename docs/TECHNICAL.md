# Solflare для macOS — техническая документация

> Лендинг проекта: [`../README.md`](../README.md). Здесь — полное описание устройства, экранов, авторизации и файлов.

Нативное desktop-приложение криптокошелька Solana на **SwiftUI**. Серверной части нет: работает **локальный self-custodial бэкенд**: ключи выводятся из seed-фразы на вашем Mac, данные берутся из публичных источников.

- Окно фиксированного размера: контент **1200 × 800** + полоса заголовка 20 pt (итого 1200 × 820). Изменение размера, zoom и полноэкранный режим отключены.
- Платформа: macOS 14+, Xcode 16, Swift 5 (язык), без внешних зависимостей, кроме локального пакета `SolflareCore`.
- Bundle id: `com.example.solflare-wallet`. Включён App Sandbox, разрешён только исходящий сетевой трафик (`Solflare.entitlements`).

> Подробный план, статус этапов, ограничения и чек-лист приёмки — в [`PLAN.md`](../PLAN.md).

---

## Системные требования

| Что | Минимум |
|---|---|
| macOS | **14.0 Sonoma** (в Xcode: Target Solflare → General → Minimum Deployments = 14.0; в `Info.plist` — `LSMinimumSystemVersion = 14.0`; у пакета `SolflareCore` — `.macOS(.v14)`) |
| Mac | MacBook Air / MacBook Pro / Mac mini — 2018 и новее; iMac — 2019 и новее (iMac Pro — 2017); Mac Pro — 2019 и новее; Mac Studio — 2022 и новее. Apple Silicon и Intel поддерживаются (Release — universal) |
| Экран | эффективное разрешение от 1440 × 900: окно фиксированное 1200 × 820; при постоянно видимом Dock на 13″ нижний край может быть обрезан — включите автоскрытие Dock |
| Авторизация | пароль учётной записи macOS (Touch ID необязателен) |
| Сеть | интернет для балансов, рынков и отправки |
| Сборка проекта | Xcode 16+ (проверено на 16.2) |

## Содержание

1. [Быстрый старт](#быстрый-старт)
2. [Как устроен проект](#как-устроен-проект)
3. [Страницы и экраны](#страницы-и-экраны)
4. [Авторизация по seed-фразе](#авторизация-по-seed-фразе)
5. [Как приложением управляют изнутри](#как-приложением-управляют-изнутри)
6. [Данные и сеть](#данные-и-сеть)
7. [Отправка средств](#отправка-средств)
8. [Настройки и безопасность](#настройки-и-безопасность)
9. [Какие файлы за что отвечают](#какие-файлы-за-что-отвечают)
10. [Проверка и отладка](#проверка-и-отладка)
11. [Ограничения](#ограничения)

---

## Быстрый старт

```bash
open Solflare.xcodeproj      # схема Solflare → ⌘R
```

Тесты ядра (без Xcode):

```bash
cd Packages/SolflareCore
swift test                                  # офлайн-тесты
SOLFLARE_LIVE=1 swift test                  # плюс живые проверки сети (по запросу)
```

Сборка из командной строки:

```bash
xcodebuild -project Solflare.xcodeproj -scheme Solflare -configuration Debug build
```

Первый запуск: приложение открывает Welcome. Создайте кошелёк или импортируйте существующий; дальше каждый запуск сразу ведёт в Portfolio (фраза лежит в Keychain).

> Если Xcode показывает «Cannot find type … in scope» после добавления файлов в пакет — **File → Packages → Reset Package Caches**, затем **Product → Clean Build Folder**.

---

## Как устроен проект

Два слоя: приложение (UI + состояние) и пакет `SolflareCore` (чистая логика без UI).

```
SolflareMac/
├── Solflare.xcodeproj           проект Xcode (synchronized folder: файлы подхватываются сами)
├── Solflare.entitlements        App Sandbox + network.client
├── Solflare/                    приложение
│   ├── App/                     точка входа, окно, роутер, AppModel
│   ├── DesignSystem/            цвета, шрифты, кнопки, поля, плашки, иконки, анимации
│   ├── Domain/                  модели и форматирование
│   ├── Features/                экраны и модальные окна
│   ├── Services/                сервисы: кошелёк, Keychain, сеть, кэши
│   └── Resources/               Assets.xcassets (в т.ч. AppIcon), шрифты Inter, видео прелоадера
├── Packages/SolflareCore/       бэкенд-библиотека (Swift Package) + тесты
└── Tools/                       вспомогательные скрипты (генерация иконок, эталонные векторы)
```

Поток данных (односторонний):

```
Экран (View)  ──читает──▶  AppModel (@Observable)  ──вызывает──▶  WalletService (протокол)
     ▲                         │  состояние, настройки,                │
     └─────── обновление ──────┘  оверлеи, таймеры                     ├─ LiveWalletService  (настоящий кошелёк)
                                                                       └─ MockWalletService  (демонстрационные данные)
                                                                              │
                                                                       SolflareCore (BIP-39, RPC, транзакции)
```

Ключевые идеи:

- **Экраны ничего не знают о сети.** Они читают `AppModel` и вызывают его методы. Весь бэкенд спрятан за протоколом `WalletService`.
- **Один источник состояния** — `AppModel`: сессия, настройки, данные экранов, оверлеи, блокировка, автообновление.
- **Навигация** — `AppRouter`: текущий маршрут + история (назад/вперёд, ⌘[ и ⌘]).
- **Вёрстка**: абсолютное позиционирование (`.abs(x:y:w:h:)`) с фиксированной геометрией.

---

## Страницы и экраны

### Онбординг (без боковой панели)

| Маршрут | Файл | Что это |
|---|---|---|
| `welcome` | `Features/Onboarding/WelcomeScreen.swift` | Приветствие, «Get started», «I already have a wallet», статистика и карусель справа (`WelcomeCarousel.swift`) |
| `createWallet` | `CreateWalletScreen.swift` | 12 слов новой фразы, Copy, «I saved my recovery phrase» |
| `importWallet` | `ImportWalletScreen.swift` | Ввод 12 или 24 слов, Paste, Continue |
| `allDone` | `AllDoneScreen.swift` | «All done!», согласие с Terms / Privacy, вход в приложение |

Общая оболочка — `OnboardingShell.swift` (левая панель меняется, правая карусель постоянная).

### Прелоадер

`Features/Preloader/PreloaderView.swift` проигрывает видео `Resources/Preloader/solflare-preloader.mp4` (3 с, 1200 × 800, без звука) поверх интерфейса при **каждом запуске** приложения, затем плавно гаснет (0,6 с) и открывает главную страницу (Welcome либо Portfolio / экран блокировки).

- При включённом «Reduce motion» прелоадер пропускается.
- Если видео не найдено или не стартует, приложение открывается через несколько секунд само.
- В Debug для автоматических проверок экранов есть `-debugSkipPreloader 1`.

### Приложение (сайдбар + шапка)

Оболочка — `Features/Shell/AppShell.swift`; сайдбар — `SidebarView.swift` (5 пунктов + кнопка выхода внизу), шапка — `HeaderBar.swift` (заголовок, поиск, Settings, карточка кошелька с Copy, бейдж тестовой сети).

| Маршрут | Файл | Что показывает |
|---|---|---|
| `portfolio` | `Features/Portfolio/PortfolioScreen.swift` | Баланс, изменение за 24 ч, кнопки Deposit и Send, список активов; карточка баланса скрывается через Hide balances |
| `assets` | `Features/Assets/AssetsScreen.swift` | Общая стоимость, таблица токенов, Send / Deposit, пустое состояние |
| `markets` | `Features/Markets/MarketsScreen.swift` | Поиск (по имени, символу или mint), 4 фильтра, сортировка по колонкам, иконки токенов |
| `activity` | `Features/Activity/ActivityScreen.swift` | История транзакций; клик по строке открывает боковую панель с подробностями (`ActivityDetailsModal.swift`) |
| `settings` | `Features/Settings/SettingsScreen.swift` | Вкладки General, Security & Privacy, Transfers, Advanced, About |


### Модальные окна и панели

| Что | Файл | Как открывается |
|---|---|---|
| Receive (QR адреса) | `Overlays/ReceiveModal.swift` | Кнопка Deposit |
| Send: форма → обзор → отправка → результат | `Overlays/SendModal.swift`, `SendAssetPicker.swift`, `App/SendViewModel.swift` | Кнопка Send |
| Terms / Privacy / Licenses | `Overlays/LegalModal.swift` | Settings |
| Log out | `Overlays/LogoutModal.swift` | Settings → Log out |
| Recovery phrase | `Security/RecoveryPhraseModal.swift` | Settings → Show (после Touch ID / пароля) |
| Address book | `Security/AddressBookModal.swift` | Settings → Transfers → Manage |
| Экран блокировки | `Security/LockScreen.swift` | Включённый App lock |
| Транзакция | `Activity/ActivityDetailsModal.swift` | Клик по строке Activity |
| Плашка выбора (select) | `DesignSystem/Dropdown.swift` | Любое поле выбора |

Esc закрывает верхнее из окон; клик по затемнению — тоже.

---

## Авторизация по seed-фразе

В приложении **нет логина, пароля и аккаунта на сервере**. Ваша идентичность — это seed-фраза (мнемоника BIP-39). Из неё детерминированно получаются приватный ключ и адрес. Тот, кто знает фразу, владеет кошельком — поэтому она хранится только в защищённой связке ключей macOS.

### Создание кошелька

1. **Welcome → Get started** → экран `createWallet`.
2. `AppModel.loadRecoveryPhrase()` → `WalletService.generatePhrase(12)` → `Bip39.generate`: 16 байт энтропии из `SecRandomCopyBytes` (системный CSPRNG) + контрольная сумма SHA-256 → 12 слов из английского списка BIP-39.
3. Вы записываете фразу (кнопка Copy очищает буфер обмена через минуту).
4. **I saved my recovery phrase** → `registerWallet()` → `LiveWalletService.store`:
   - слова нормализуются и проверяются (список + контрольная сумма);
   - из них выводится кошелёк (см. ниже);
   - фраза сохраняется в **Keychain**;
   - очищаются старые кэши.
5. `allDone` → **Continue** → `prepareApp()` и переход в Portfolio.

### Импорт существующего кошелька

`importWallet` принимает 12 или 24 слова (поле можно заполнить вставкой). `importWallet(words:)` проходит тот же путь `store`: фраза проверяется, из неё выводится адрес, фраза уходит в Keychain. Неверное слово или контрольная сумма → ошибка «Recovery phrase is not valid», ничего не сохраняется.

### Как из фразы получается адрес

```
12/24 слов ─ PBKDF2-HMAC-SHA512 (2048 итераций, соль "mnemonic") ─▶ seed 64 байта
          ─ SLIP-0010 ed25519, путь m/44'/501'/0'/0'  ─▶ приватный ключ 32 байта
          ─ Ed25519 ─▶ публичный ключ ─ Base58 ─▶ адрес Solana
```

Путь `m/44'/501'/0'/0'` — тот же, что у Solflare и Phantom для первого аккаунта, поэтому адрес совпадает с вашим кошельком в других приложениях. Код: `Crypto/Bip39.swift`, `Crypto/SLIP10.swift`, `Wallet/SolanaWallet.swift`.

### Что хранится и где

| Данные | Где | Замечания |
|---|---|---|
| Seed-фраза | Keychain: `com.example.solflare-wallet.recovery-phrase` / `main-wallet` | `WhenUnlockedThisDeviceOnly`, не синхронизируется с iCloud |
| Приватный ключ | **Нигде** | Выводится из фразы только на время подписи и сразу уничтожается (`SolanaSigner`) |
| Публичный адрес | Память, кэш портфеля | Публичная информация |
| Свой RPC-адрес | Keychain (может содержать ключ доступа) | Для каждой сети отдельно |
| Настройки, адресная книга | UserDefaults (`solflare.*`) | Не секретны |
| Транзакции, иконки | Application Support / Caches | Только публичные данные сети |

### Вход при следующем запуске

`AppDelegate` → `AppModel.bootstrap()` → `LiveWalletService.restoreSession()` читает фразу из Keychain, выводит адрес и сразу открывает Portfolio. Если включён **App lock**, поверх интерфейса показывается экран блокировки: Touch ID или пароль Mac.

### Подтверждение владельца

Чувствительные действия требуют системной проверки (`LocalAuthentication`, Touch ID либо пароль учётной записи):

- подпись и отправка перевода;
- показ recovery phrase;
- смена настройки App lock;
- разблокировка приложения.

### Выход

**Settings → Log out** → подтверждение → `AppModel.signOut()` → `WalletService.logout()`: фраза удаляется из Keychain, стираются кэши транзакций и портфеля, настройки кошелька сбрасываются. Без сохранённой фразы восстановить доступ нельзя.

---

## Как приложением управляют изнутри

### Запуск

`SolflareApp` (точка входа, меню ⌘[ ⌘] ⌘L) → `AppDelegate` создаёт `AppRouter`, `AppModel`, сервис кошелька и окно через `MainWindow.make` (`NSWindow` + `NSHostingView`, кнопки окна выровнены по центру полосы заголовка). В Debug флаг `-debugMock 1` подставляет `MockWalletService`.

### Состояние (`AppModel`)

- **Сессия:** `address`, `hasWallet`, `isBootstrapped`.
- **Данные:** `portfolio`, `coins` (рынки), `activity`; флаги ошибок загрузки.
- **Настройки:** валюта, Hide balances, сеть, свой RPC, автообновление, App lock, лимит подтверждения, адресная книга.
- **Оверлеи:** Receive, Send, Legal, Logout, Address book, Recovery phrase, Activity details, раскрытая плашка.
- **Служебное:** блокировка по бездействию, автообновление, таймер скрытия фразы.

### Навигация (`AppRouter`)

`go(_:)` добавляет шаг в историю, `reset(to:)` — переход без возможности вернуться (вход в приложение, выход), `back()` / `forward()`. Анимации появления блоков и модальных окон заданы в `DesignSystem/Motion.swift`.

### Слои окна (`RootView`)

Порядок снизу вверх: интерфейс (онбординг или `AppShell`) → Receive → Send → Activity details → Legal → Address book → Logout → Recovery phrase → плашка выбора → экран блокировки. Пока открыт модальный слой, фон размывается и отключается.

### Фоновая работа

- **Автообновление** баланса, рынков и ленты (30 с / 1 мин / 5 мин / вручную; кнопка Refresh now).
- **Блокировка по бездействию**; также при сне Mac и блокировке экрана.
- **Throttling** публичного RPC: транзакции подгружаются по одной, результат кэшируется на диск.

---

## Данные и сеть

| Что | Источник |
|---|---|
| Баланс, токен-аккаунты, транзакции, комиссии, отправка | Solana JSON-RPC (публичный узел кластера или ваш) |
| Метаданные токенов, цены, рынок, иконки | Jupiter Tokens API v2 |
| Курсы валют (EUR, GBP) | CoinGecko `exchange_rates` |
| Иконки токенов | Адреса из метаданных токена; для IPFS — запасные шлюзы |

Иконки загружает `Services/RemoteImageStore.swift`: кэш в памяти и на диске, SVG/WebP/PNG, повтор через запасные шлюзы, если `ipfs.io` отвечает 429.

**Сети:** Mainnet, Devnet, Testnet (Settings → General). На тестовых сетях цены и «спам-фильтр» отключены, в шапке виден бейдж. Свой RPC задаётся во вкладке Advanced: адрес только `https://`, перед сохранением проверяются genesis hash и здоровье узла.

---

## Отправка средств

1. Кнопка **Send** (Portfolio или Assets) → форма: актив (плашка с иконками), получатель, сумма (Max).
2. **Review:** `prepareSend` проверяет адрес, баланс, rent-exempt минимум, состояние токен-аккаунта получателя и запрашивает комиссию у сети. Ничего не подписывается.
3. Для крупного перевода (порог в Settings → Transfers) нужно набрать последние 4 символа адреса.
4. **Confirm and send:** проверка владельца (Touch ID / пароль) → повторная проверка по состоянию сети → подпись ключом, выведенным на мгновение → `sendTransaction` → ожидание подтверждения.
5. Результат: Sent / Submitted / Failed, подпись транзакции, ссылка на Solscan.

Поддерживаются SOL и токены программы Token (SPL). Token-2022 показывается, но отправка отключена. Подробности и проверки — в `Transactions/SendPlanner.swift` и разделе 4.1 `PLAN.md`.

---

## Настройки и безопасность

| Вкладка | Что внутри |
|---|---|
| **General** | Currency, Network, Auto-refresh, Refresh now |
| **Security & Privacy** | Hide balances, Hide balances on launch, App lock, Recovery phrase, Terms, Privacy, Log out |
| **Transfers** | Extra confirmation (порог), Only saved addresses, Address book |
| **Advanced** | Custom RPC, Local data (размер кэша и очистка) |
| **About** | Версия, Licenses, Copy diagnostics (без ключей, фразы и полного адреса) |

Дополнительно: ⌘L блокирует кошелёк (если включён App lock); пока на экране фраза, окно исключено из записи экрана.

---

## Какие файлы за что отвечают

### Приложение (`Solflare/`)

| Файл | Ответственность |
|---|---|
| `App/SolflareApp.swift` | Точка входа, команды меню |
| `App/AppDelegate.swift` | Создание модели, сервиса и окна; блокировка при сне |
| `App/MainWindow.swift` | Окно 1200 × 820, выравнивание кнопок заголовка |
| `App/RootView.swift` | Корневой экран, слои модальных окон, Esc, блокировка |
| `App/AppRouter.swift` | Маршруты и история навигации |
| `App/AppModel.swift` | Всё состояние и бизнес-действия интерфейса |
| `App/SendViewModel.swift` | Состояние панели Send |
| `App/DebugLaunch.swift` | Отладочные флаги запуска (только Debug) |
| `Domain/Models.swift` | Токены, портфель, рынки, активность |
| `Domain/Preferences.swift` | Политика блокировки, интервал обновления, лимиты, адресная книга |
| `Domain/SendModels.swift` | Активы и котировки отправки |
| `Domain/Formatting.swift` | Форматирование валют, процентов, адресов |
| `Services/WalletService.swift` | Протокол бэкенда |
| `Services/LiveWalletService.swift` | Настоящий кошелёк: сессия, портфель, рынок, лента, отправка |
| `Services/MockWalletService.swift` | Демонстрационные данные для проверки вёрстки |
| `Services/KeychainVault.swift` | Хранение seed-фразы |
| `Services/NetworkSettings.swift` | Выбранная сеть и свой RPC (Keychain) |
| `Services/SendAuthenticator.swift` | Touch ID / пароль через LocalAuthentication |
| `Services/EffectStore.swift` | Дисковый кэш разобранных транзакций |
| `Services/RemoteImageStore.swift` | Загрузка и кэш иконок токенов |
| `Features/Preloader/PreloaderView.swift` | Видео-прелоадер первого запуска |
| `Services/AmountParserBridge.swift` | Мост к парсеру сумм ядра |
| `DesignSystem/*` | Палитра, Inter, кнопки, поля, плашки, SVG-иконки, анимации |
| `Features/*` | Экраны и модальные окна (таблица выше) |

### Ядро (`Packages/SolflareCore/Sources/SolflareCore/`)

| Папка / файл | Ответственность |
|---|---|
| `Crypto/Bip39.swift`, `Bip39WordList.swift` | Генерация, проверка и seed из мнемоники |
| `Crypto/SLIP10.swift` | Вывод ключа ed25519 по пути |
| `Crypto/Base58.swift`, `Curve25519Field.swift` | Base58, проверка точек кривой (PDA) |
| `Wallet/SolanaWallet.swift` | Публичная часть кошелька (адрес) |
| `Wallet/SolanaSigner.swift` | Подпись, ключ живёт только на время подписи |
| `Wallet/TransactionAnalysis.swift` | Разбор транзакции и классификация для Activity |
| `Network/SolanaRPC.swift` | JSON-RPC клиент, кластеры, проверка своего узла |
| `Network/MarketDataClients.swift` | Jupiter, CoinGecko |
| `Network/HTTPTransport.swift`, `TransactionFetcher.swift` | Транспорт с повторами, троттлинг `getTransaction` |
| `Transactions/*` | Сборка, сериализация и планирование перевода |

### Прочее

| Файл | Ответственность |
|---|---|
| `Tools/solana_oracle.py` | Независимый эталон для тестов криптографии |
| `Packages/SolflareCore/Tests/` | 70 тестов (8 живых, по запросу): криптография, транзакции, сеть |
| `PLAN.md` | План, статус, ограничения |

---

## Проверка и отладка

- Автотесты: `swift test` в `Packages/SolflareCore` (криптография сверена с официальными векторами BIP-39/SLIP-0010 и независимым Python-эталоном; отправка проверена симуляцией и побайтным сравнением с реальными транзакциями mainnet).
- Живые проверки сети включаются переменной `SOLFLARE_LIVE=1`.
- В Debug-сборке работают флаги запуска, например:

```bash
open Solflare.app --args -debugMock 1 -debugRoute markets
open Solflare.app --args -debugMock 1 -debugRoute settings -debugSettingsTab security
open Solflare.app --args -debugMock 1 -debugRoute portfolio -debugOverlay send
```

Полный список аргументов (маршруты, оверлеи, вкладки, сеть, блокировка) — в разделе «Запуск» `PLAN.md`.

> ⚠️ Флаг `-debugImportPhrase` реально записывает фразу в Keychain и **перезаписывает** сохранённый кошелёк. Используйте только публичные тестовые фразы и только на отдельной учётной записи.

---

## Ограничения

- Реальные транзакции в mainnet при разработке не отправлялись (не было средств). Первый перевод сделайте на минимальную сумму.
- Публичный RPC ограничивает частоту запросов: холодная загрузка ленты занимает до ~20 с, дальше берётся кэш. Свой RPC снимает это ограничение.
- Блокировка приложения защищает интерфейс, но не Keychain: любой процесс в вашей разблокированной сессии macOS, получивший доступ к записи, может её прочитать.
- Токены Token-2022 нельзя отправить; приоритетные комиссии (priority fee) и memo не поддерживаются.
- Один аккаунт кошелька (путь `m/44'/501'/0'/0'`); несколько кошельков не реализованы.
