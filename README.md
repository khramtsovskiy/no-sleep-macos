# no-sleep

![Platform](https://img.shields.io/badge/platform-macOS-blue)
![License](https://img.shields.io/badge/license-MIT-green)

Простой CLI-переключатель режима сна при закрытой крышке для macOS.

Позволяет одной командой запретить MacBook уходить в сон при закрытой крышке — удобно для долгих загрузок, сборок, рендера, скачивания файлов и т.п.

```
no-sleep up       # 🔒 не спать при закрытой крышке
no-sleep down     # 💤 обычное поведение
no-sleep status   # посмотреть текущее состояние
```

## Зачем

Встроенный `caffeinate` не помогает, когда закрываешь крышку — macOS всё равно уходит в сон. Единственный рабочий способ — выставить `disablesleep 1` через `pmset`. Этот скрипт делает это удобно и безопасно, а ещё показывает текущее состояние, чтобы не забыть вернуть всё назад.

## Требования

- macOS (проверено на Apple Silicon, работает и на Intel)
- `zsh` или `bash`
- Права `sudo` для изменения настроек питания

## Установка

### Через Homebrew (рекомендуется)

```bash
brew install khramtsovskiy/tap/no-sleep
```

### Вручную
### 1. Скопируй скрипт

```bash
mkdir -p ~/bin
curl -fsSL https://raw.githubusercontent.com/khramtsovskiy/no-sleep-macos/main/no-sleep -o ~/bin/no-sleep
chmod +x ~/bin/no-sleep
```

Или склонируй репозиторий и скопируй вручную:

```bash
git clone https://github.com/khramtsovskiy/no-sleep-macos.git
mkdir -p ~/bin
cp no-sleep-macos/no-sleep ~/bin/no-sleep
chmod +x ~/bin/no-sleep
```

### 2. Добавь `~/bin` в `PATH`

Открой `~/.zshrc`:

```bash
nano ~/.zshrc
```

Добавь строку:

```bash
export PATH="$HOME/bin:$PATH"
```

Примени:

```bash
source ~/.zshrc
```

### 3. (Опционально) Разреши `pmset` без пароля

Чтобы `no-sleep up` / `down` не спрашивали пароль каждый раз, создай правило в sudoers:

```bash
sudo visudo -f /etc/sudoers.d/pmset
```

Вставь строку (замени `твой_юзер` на результат команды `whoami`):

```
твой_юзер ALL=(ALL) NOPASSWD: /usr/bin/pmset -a disablesleep 0, /usr/bin/pmset -a disablesleep 1
```

Сохрани (`Ctrl+O`, Enter, `Ctrl+X`).

> ⚠️ Будь осторожен с `visudo` — синтаксическая ошибка в sudoers может сломать доступ к `sudo`. Всегда используй именно `visudo`, а не прямой редактор.

## Использование

| Команда | Что делает |
|---------|-----------|
| `no-sleep up` | Запрещает сон при закрытой крышке (`disablesleep 1`) |
| `no-sleep down` | Возвращает обычное поведение (`disablesleep 0`) |
| `no-sleep status` | Показывает текущее состояние |

Пример:

```bash
$ no-sleep up
🔒 SleepDisabled = 1 — сон при закрытой крышке ОТКЛЮЧЁН

$ no-sleep status
🔒 SleepDisabled = 1 — Mac НЕ спит при закрытой крышке

$ no-sleep down
💤 SleepDisabled = 0 — сон при закрытой крышке включён
```

## ⚠️ Важно

**Не оставляй режим `up` включённым надолго.** В этом состоянии закрытый MacBook продолжает работать, потреблять энергию и греться. Особенно опасно оставлять его в рюкзаке или сумке — возможен перегрев.

Хорошая привычка: закончил работу → `no-sleep down`.

## Как это работает

Скрипт — обёртка над системной утилитой `pmset`:

```bash
sudo pmset -a disablesleep 1   # запретить сон
sudo pmset -a disablesleep 0   # разрешить сон
```

Состояние читается из вывода `pmset -g` — там параметр называется `SleepDisabled` (не `disablesleep`, как в команде установки — это частая путаница).

## Uninstall

### Если устанавливал через Homebrew

```bash
brew uninstall no-sleep
brew untap khramtsovskiy/tap
```

### Если устанавливал вручную
Удалить скрипт:

```bash
rm ~/bin/no-sleep
```

И правило sudoers, если ты его создавал:

```bash
sudo rm /etc/sudoers.d/pmset
```


## Совместимость

| Платформа | Статус |
|-----------|--------|
| macOS 14 (Sonoma), Apple Silicon | ✅ Работает |
| macOS 13 (Ventura), Apple Silicon | ✅ Работает |
| macOS 12 (Monterey) | ✅ Должно работать |
| Intel Mac | ✅ Должно работать |

## Лицензия

MIT — делай что хочешь.

## Contributing

PR и issues приветствуются. Если нашёл баг или есть идея — открывай issue.