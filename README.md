# LITETEAMRUSTGIT

<div align="center">

![Version](https://img.shields.io/badge/version-2.1.0-red?style=for-the-badge&logo=shield)
![Platform](https://img.shields.io/badge/platform-Windows%20x64-blue?style=for-the-badge&logo=windows)
![Language](https://img.shields.io/badge/language-C%2B%2B20-00599C?style=for-the-badge&logo=c%2B%2B)
![License](https://img.shields.io/badge/license-Proprietary-orange?style=for-the-badge)
![Status](https://img.shields.io/badge/status-active-success?style=for-the-badge)

**Многоуровневая античит-система для игры Rust**

*Защита от читеров, инжекторов, отладчиков и других вредоносных инструментов*

[Возможности](#-основные-возможности) • [Архитектура](#-архитектура) • [Защита](#-системы-защиты) • [Сборка](#-сборка)

</div>

---

## 📖 О проекте

**LiteAC** — это продвинутая античит-система, разработанная для защиты игрового процесса в **Rust**. Система работает как DLL, внедряемая в процесс игры через лаунчер `LiteLauncher.exe`, и обеспечивает комплексную защиту от:

- 🎯 **Читов и инжекторов** (Superiority, Recoil и др.)
- 🐛 **Отладчиков** (x64dbg, OllyDbg, WinDbg)
- 🔧 **Хук-библиотек** (MinHook, Microsoft Detours)
- 💉 **Ручного маппинга DLL**
- 🖱️ **Инжектированного ввода** (SendInput, mouse_event)
- 📸 **Программ для скриншотов и стриминга**

---

## ⚡ Основные возможности

<table>
<tr>
<td width="50%">

### 🧠 Защита памяти
- Сканирование на инъекции
- Детект ручного маппинга
- Проверка целостности кода
- Мониторинг изменений памяти
- Поиск PE-заголовков в приватных регионах

</td>
<td width="50%">

### 📚 Валидация DLL
- Белый и чёрный списки
- Мониторинг загрузки модулей
- Уведомления о новых DLL
- Проверка путей и подписей
- Детект скрытых модулей

</td>
</tr>
<tr>
<td width="50%">

### 🐛 Анти-отладка
- Обнаружение отладчиков
- Проверка родительского процесса
- Скрытие потоков от отладчика
- Нейтрализация DbgBreakPoint
- Верификация лаунчера

</td>
<td width="50%">

### 🧵 Мониторинг потоков
- Проверка адресов старта
- Детект подозрительных потоков
- Отслеживание создания потоков
- Проверка принадлежности модулям

</td>
</tr>
<tr>
<td width="50%">

### ⚙️ Мониторинг процессов
- Чёрный список процессов
- Автоматическое завершение
- Детект Cheat Engine, Process Hacker
- Поиск инжекторов

</td>
<td width="50%">

### 🪝 Защита от хуков
- Детект MinHook
- Детект Microsoft Detours
- Поиск инлайн-хуков
- Сканирование трамплинов
- Проверка критических функций ntdll

</td>
</tr>
<tr>
<td width="50%">

### 🖱️ Мониторинг мыши
- Низкоуровневый хук WH_MOUSE_LL
- Детект инжектированного ввода
- Проверка флагов LLMHF_INJECTED
- Защита от макросов

</td>
<td width="50%">

### 🔐 Самозащита
- Анти-дамп защита
- Строгий DACL
- DEP + ASLR
- Скрытие от отладчика
- Порча PE-заголовков в памяти

</td>
</tr>
</table>

---

## 🏗️ Архитектура

```
┌─────────────────────────────────────────────────────────────┐
│                      LiteLauncher.exe                       │
│                    (Точка входа, проверка)                  │
└──────────────────────────┬──────────────────────────────────┘
                           │
                           ▼
┌─────────────────────────────────────────────────────────────┐
│                         LiteAC.dll                          │
│  ┌───────────────────────────────────────────────────────┐  │
│  │                   AntiCheatCore                       │  │
│  │              (Ядро системы, координация)              │  │
│  └───────────────────────────────────────────────────────┘  │
│                           │                                 │
│  ┌────────────────────────┼────────────────────────────┐   │
│  │                        │                            │   │
│  ▼                        ▼                            ▼   │
│  ┌──────────────┐  ┌──────────────┐  ┌──────────────┐     │
│  │ MemoryGuard  │  │  DLLGuard    │  │  AntiDebug   │     │
│  └──────────────┘  └──────────────┘  └──────────────┘     │
│  ┌──────────────┐  ┌──────────────┐  ┌──────────────┐     │
│  │ ThreadWatch  │  │ ProcessWatch │  │  HookShield  │     │
│  └──────────────┘  └──────────────┘  └──────────────┘     │
│  ┌──────────────┐  ┌──────────────┐  ┌──────────────┐     │
│  │ MouseMonitor │  │ SelfProtect  │  │ HookDetector │     │
│  └──────────────┘  └──────────────┘  └──────────────┘     │
│                           │                                 │
│  ┌────────────────────────┼────────────────────────────┐   │
│  │                        │                            │   │
│  ▼                        ▼                            ▼   │
│  ┌──────────────┐  ┌──────────────┐  ┌──────────────┐     │
│  │ HWIDCollect  │  │  Heartbeat   │  │ ScreenCapture│     │
│  └──────────────┘  └──────────────┘  └──────────────┘     │
│  ┌──────────────┐  ┌──────────────┐  ┌──────────────┐     │
│  │ DiscordRPC   │  │ConsentDialog │  │ Deobfuscator │     │
│  └──────────────┘  └──────────────┘  └──────────────┘     │
└─────────────────────────────────────────────────────────────┘
                           │
                           ▼
┌─────────────────────────────────────────────────────────────┐
│                    Discord Webhook / Server                 │
│                   (Отчёты, скриншоты, логи)                 │
└─────────────────────────────────────────────────────────────┘
```

---

## 🛡️ Системы защиты

### 1️⃣ **MemoryProtection** — Защита памяти

<details>
<summary><b>Развернуть список функций</b></summary>

| Функция | Описание |
|---------|----------|
| `ScanMemoryForInjection()` | Сканирует память на регионы с правами `PAGE_EXECUTE_READWRITE` / `PAGE_EXECUTE_WRITECOPY`. Более 150 таких регионов — детект. |
| `ValidateCodeIntegrity()` | Проверяет, что секция `.text` не имеет прав на запись. |
| `MonitorMemoryChanges()` | Периодическая (30 сек) проверка памяти. |
| `DetectManualMapping()` | Поиск PE-заголовков в приватных исполняемых регионах. |
| `ProtectMemoryRegion()` | Установка `PAGE_EXECUTE_READ` на регион. |

</details>

### 2️⃣ **DLLValidator** — Валидация DLL

<details>
<summary><b>Развернуть список функций</b></summary>

| Функция | Описание |
|---------|----------|
| `ScanLoadedModules()` | Проверка всех загруженных модулей на чёрный список и доверенность. |
| `IsModuleBlacklisted()` | BlockList |
| `IsModuleTrusted()` | Проверка доверия по пути (игра, Windows, Steam, NVIDIA, AMD, Razer, Logitech, OBS и др.) и имени (UnityPlayer, mono, Steam API, EAC, VMProtect). |
| `MonitorModuleLoading()` | Мониторинг загрузки модулей (раз в секунду). |
| `AddSuspiciousModule()` | Добавление модуля в список подозрительных. |

</details>

### 3️⃣ **AntiDebug** — Анти-отладка

<details>
<summary><b>Развернуть список функций</b></summary>

| Функция | Описание |
|---------|----------|
| `IsDebuggerPresent()` | Проверка через `IsDebuggerPresent()` и `CheckRemoteDebuggerPresent()`. |
| `AntiDebugThread()` | Бесконечная проверка наличия отладчика. |
| `VerifyLauncher()` | Проверка, что родитель — `LiteLauncher.exe`. |

</details>

### 4️⃣ **ThreadMonitor** — Мониторинг потоков

<details>
<summary><b>Развернуть список функций</b></summary>

| Функция | Описание |
|---------|----------|
| `IsThreadSuspicious()` | Проверка адреса старта потока через `NtQueryInformationThread`. |
| `ScanSuspiciousThreads()` | Сканирование всех потоков процесса. |
| `MonitorThreadCreation()` | Запоминание легитимных потоков и проверка новых. |

</details>

### 5️⃣ **ProcessMonitor** — Мониторинг процессов

<details>
<summary><b>Развернуть список функций</b></summary>

| Функция | Описание |
|---------|----------|
| `MonitorProcessList()` | Сканирование процессов (раз в 5 сек). **Автоматическое завершение.** |

</details>

### 6️⃣ **SelfProtection** — Самозащита

<details>
<summary><b>Развернуть список функций</b></summary>

| Функция | Описание |
|---------|----------|
| `IsAdmin()` | Проверка прав администратора. |
| `ApplyMitigationPolicies()` | Включение DEP и ASLR. |
| `NeutralizeDebugger()` | Патч `DbgBreakPoint` и `DbgUiRemoteBreakin` на `RET`. |
| `SetStrictDACL()` | Строгий DACL на процесс. |
| `HideFromDebugger()` | Скрытие потока через `NtSetInformationThread`. |
| `RegisterDllNotification()` | Колбэк на загрузку DLL через `LdrRegisterDllNotification`. |
| `AntiDump()` | Порча PE-заголовков в памяти + изменение размера модуля в PEB. |
| `ProtectProcess()` | Комплексная защита процесса. |

</details>

### 7️⃣ **HookDetector** — Обнаружение хуков

<details>
<summary><b>Развернуть список функций</b></summary>

| Функция | Описание |
|---------|----------|
| `DetectInlineHooks()` | Проверка байтов `NtQuerySystemInformation` и `NtProtectVirtualMemory` на `0xE9` / `0xCC`. |
| `MonitorHooks()` | Периодическая (3 сек) проверка хуков и критических функций. |

</details>

### 8️⃣ **HookShield** — Защита от хуков

<details>
<summary><b>Развернуть список функций</b></summary>

| Функция | Описание |
|---------|----------|
| `DetectMinHook()` | Поиск MinHook по модулям и экспортам `MH_Initialize`, `MH_CreateHook`. |
| `DetectDetours()` | Поиск Detours по модулям и экспортам `DetourAttach`, `DetourDetach`. |
| `ScanForHookTrampoline()` | Проверка трамплинов: `0xE9`, `0xFF 0x25`, `0xE8`, `0xCC`, `0x68 ... 0xC3`, `0x48 0xB8 ... 0xFF 0xE0`, `0xEB`. |
| `ScanPrivateExecutableRegions()` | Поиск PE-заголовков в приватных исполняемых регионах. |
| `MonitorCriticalFunctions()` | Мониторинг (2 сек) MinHook, Detours, патчей, ручного маппинга. |
| `ReadNtdllFromDisk()` | Чтение оригинального `ntdll.dll` с диска. |

</details>

### 9️⃣ **MouseMonitor** — Мониторинг мыши

<details>
<summary><b>Развернуть список функций</b></summary>

| Функция | Описание |
|---------|----------|
| `MouseHookProc()` | Колбэк `WH_MOUSE_LL`. Детект `LLMHF_INJECTED` / `LLMHF_LOWER_IL_INJECTED`. |
| `StartMonitor()` | Запуск потока мониторинга. |
| `StopMonitor()` | Остановка потока мониторинга. |

</details>

### 🔟 **HWIDCollector** — Сбор отпечатка машины

<details>
<summary><b>Развернуть список функций</b></summary>

| Функция | Описание |
|---------|----------|
| `GetHWID()` | Сбор HWID: MachineGuid, VolumeSerial, SMBIOS UUID, ProcessorId, CPU Vendor, GPU, MAC. SHA-256. |
| `GetMachineGuid()` | Чтение `MachineGuid` из реестра. |
| `GetVolumeSerial()` | Серийный номер тома C:. |
| `GetSmbiosUuid()` | UUID материнской платы через WMI. |
| `GetCpuInfo()` | Информация о CPU через `__cpuid`. |
| `GetProcessorId()` | ProcessorId через WMI. |
| `GetGpuName()` | Имя GPU через `EnumDisplayDevicesW`. |
| `GetPrimaryMac()` | MAC-адрес через `GetAdaptersInfo`. |
| `HmacSha256Hex()` | HMAC-SHA256 для подписи пакетов. |

</details>

### 1️⃣1️⃣ **Heartbeat** — Связь с сервером

<details>
<summary><b>Развернуть список функций</b></summary>

| Функция | Описание |
|---------|----------|
| `Start()` | Запуск потока heartbeat. |
| `HeartbeatThread()` | UDP-пакеты на `x.x.x.x:xxxxx` с `LITEAC_HB:<SteamID>`. |
| `StartServerAuth()` | Запуск потока авторизации. |
| `ServerAuthThread()` | Отправка `LITEAC:<steamId>:<hwid>:<timestamp>:<hmac>`. |

</details>

### 1️⃣2️⃣ **DiscordRPC** — Интеграция с Discord

<details>
<summary><b>Развернуть список функций</b></summary>

| Функция | Описание |
|---------|----------|
| `Initialize()` | Подключение к Discord IPC. |
| `UpdatePresence()` | Обновление статуса (details, state, image, buttons). |
| `SendData()` | Отправка данных в IPC. |
| `Shutdown()` | Закрытие соединения. |

</details>

### 1️⃣3️⃣ **ScreenCapture** — Скриншоты

<details>
<summary><b>Развернуть список функций</b></summary>

| Функция | Описание |
|---------|----------|
| `CaptureScreen()` | 3 метода: GDI+, BitBlt, PrintWindow (WGC). |
| `CaptureScreenWithOverlay()` | Захват с наложением текста (IP, SteamID, причина). |
| `SendToDiscordWebhook()` | Отправка скриншота в Discord с embed. |
| `SendVDFToDiscord()` | Отправка `loginusers.vdf`. |
| `SendCheatReport()` | Отчёт о читере: скриншот, SteamID, IP, причина, DLL. |
| `StartKeyboardMonitor()` | Мониторинг INSERT / HOME / DELETE для ручного скрина. |
| `SendScreenshotOnExit()` | Скриншот при выходе. |
| `GetSteamID()` | SteamID из реестра / `loginusers.vdf` / env. |
| `GetPublicIP()` | Публичный IP через `api.ipify.org`. |

</details>

### 1️⃣4️⃣ **ConsentDialog** — Диалог согласия

<details>
<summary><b>Развернуть список функций</b></summary>

| Функция | Описание |
|---------|----------|
| `ShowConsentDialog()` | TaskDialog с чекбоксом "Запомнить". |
| `IsConsentGiven()` | Проверка согласия в реестре. |
| `SaveConsent()` | Сохранение согласия. |

</details>

### 1️⃣5️⃣ **Deobfuscator** — Деобфускация

<details>
<summary><b>Развернуть список функций</b></summary>

| Функция | Описание |
|---------|----------|
| `Decode()` | XOR-деобфускация `std::string`. |
| `DecodeW()` | XOR-деобфускация `std::wstring`. |

</details>

### 1️⃣6️⃣ **AntiCheatCore** — Ядро системы

<details>
<summary><b>Развернуть список функций</b></summary>

| Функция | Описание |
|---------|----------|
| `Initialize()` | Инициализация: самозащита, запуск всех мониторов, проверка MinHook/Detours, валидация DLL. |
| `Shutdown()` | Остановка мониторинга. |
| `RunProtectionLoop()` | При детекте: отчёт в Discord, приостановка потоков, музыка, MessageBox, завершение процесса. |

</details>

---

## 🔐 Дополнительные технологии

<div align="center">

| Технология | Назначение |
|:----------:|:-----------|
| 🔒 **VMProtect** | Виртуализация и ультра-защита критических функций |
| 🔀 **XOR-обфускация** | Шифрование всех строк |
| 🛡️ **Anti-Dump** | Порча PE-заголовков, изменение размера модуля в PEB |
| 🕵️ **Thread Hiding** | Скрытие потоков через `NtSetInformationThread` |
| 🔐 **Strict DACL** | Защита процесса от внешнего доступа |
| ⚙️ **DEP + ASLR** | Защита от эксплойтов |

</div>

---

## 📦 Зависимости

<div align="center">

| Библиотека | Назначение |
|:----------:|:-----------|
| **VMProtect SDK** | Обфускация и защита кода |
| **GDI+** | Работа со скриншотами |
| **WMI** | Получение HWID |
| **BCrypt** | SHA-256 и HMAC-SHA256 |
| **WinINet** | Отправка данных в Discord |
| **WinMM** | Воспроизведение звуков |
| **WS2_32** | UDP-связь с сервером |

</div>

---

## 🚀 Сборка

### Требования

- **Visual Studio 2022** (v143 / v145)
- **Windows SDK** 10.0
- **C++20**
- **VMProtect SDK**

### Post-Build Event

Проект автоматически копирует папку `MUSIC` в выходную директорию:

```xml
<PostBuildEvent>
  <Command>xcopy /Y /I /E "$(ProjectDir)MUSIC" "$(OutDir)MUSIC\"</Command>
</PostBuildEvent>
```

---

## 📁 Структура проекта

```
LITETEAMRUSTGIT/
│
├── 📂 SDK/                      # VMProtect SDK
│   ├── VMProtectSDK.h
│   ├── VMProtectSDK64.lib
│   └── VMProtectSDK32.lib
│
├── 📂 MUSIC/                    # Звуковые файлы
│   ├── 1.mp3                    # Интро
│   └── 2.mp3                    # Звук при детекте
│
├── 📂 x64/Release/              # Выходная директория
│   └── LiteAC.dll
│
├── 📄 AntiCheat.cpp / .h        # Ядро античита
├── 📄 ScreenCapture.cpp / .h    # Скриншоты
├── 📄 DiscordRPC.cpp / .h       # Discord RPC
├── 📄 ConsentDialog.cpp / .h    # Диалог согласия
├── 📄 Deobfuscator.cpp / .h     # Деобфускация
├── 📄 Heartbeat.cpp             # Связь с сервером
├── 📄 Obfuscation.h             # Oбфускация
├── 📄 dllmain.cpp               # Точка входа
├── 📄 pch.h / pch.cpp           # Precompiled headers
├── 📄 framework.h               # Фреймворк
├── 📄 resource.h                # Ресурсы
├── 📄 Resources.rc              # RC-файл
├── 📄 LiteAC.def                # Экспорты
├── 📄 LiteAC.vcxproj            # Проект VS
└── 📄 README.md                 # Документация
```

---

## 🎮 Использование

### Запуск

1. Запустите **`LiteLauncher.exe`** — он проверит целостность и запустит игру с внедрённой `LiteAC.dll`.
2. При первом запуске появится **диалог согласия** с условиями использования.
3. Античит автоматически начнёт мониторинг в фоновом режиме.

### Автоматические скриншоты

Периодически (каждые 5–15 минут) античит делает автоматические скриншоты и отправляет их в Discord.

### При обнаружении чита

1. Отправляется отчёт в Discord (скриншот + информация).
2. Приостанавливаются все потоки процесса.
3. Воспроизводится звук `2.mp3`.
4. Показывается MessageBox с предупреждением.
5. Процесс завершается через `NtTerminateProcess`.

---

## ⚠️ Дисклеймер

<div align="center">

**Данный проект создан исключительно для защиты игры Rust.**

**Автор не несёт ответственности за любое использование в незаконных целях.**

**Все права защищены © Lajtov**

</div>

---

<div align="center">

### 🌟 Если проект был полезен — поставь звезду!

[![GitHub stars](https://img.shields.io/github/stars/lajtov/LITETEAMRUSTGIT?style=social)](https://github.com/lajtov/LITETEAMRUSTGIT)
[![GitHub forks](https://img.shields.io/github/forks/lajtov/LITETEAMRUSTGIT?style=social)](https://github.com/lajtov/LITETEAMRUSTGIT)

**Сделано с ❤️ для сообщества Rust**

![Footer](https://capsule-render.vercel.app/api?type=waving&color=gradient&customColorList=6,11,20&height=120&section=footer)

</div>
