# Офлайн-набор на случай блэкаута

Здесь всё, что я скачал на Mac, чтобы без интернета работали справочники, нейросети и расшифровка голосовых. Ссылки, размеры и команды, которыми я это ставил. Актуально на ноябрь 2026.

Скачивать надо заранее, пока интернет есть. Весь софт бесплатный.

## Что понадобится

Mac на Apple Silicon (M1 и новее). Нейросети подбираются под объём памяти, об этом в разделе «Нейросети».

| Блок | Место на диске |
|---|---|
| Библиотека Kiwix, 23 архива | 250 ГБ (175 ГБ, если английская Википедия без картинок) |
| Нейросети для Mac на 128 ГБ | 191 ГБ |
| Нейросеть для Mac на 16–24 ГБ | 13 ГБ |
| Распознавание речи | 3 ГБ |
| Программы | 1 ГБ |

Полный набор для Mac на 128 ГБ занимает около 450 ГБ. У меня скачивался около двух с половиной часов на скорости примерно 70 МБ/с.

## 1. Программы

Ставь сразу, установщики сохрани на диск. Если систему придётся переставить без сети, брать их будет неоткуда.

| Программа | Зачем | Где взять |
|---|---|---|
| LM Studio | Запуск нейросетей | [lmstudio.ai/download](https://lmstudio.ai/download) |
| Kiwix | Читалка архивов Википедии и других сайтов | [Kiwix для Mac](https://download.kiwix.org/release/kiwix-macos/kiwix-macos.dmg), [iPhone](https://apps.apple.com/app/kiwix/id997079563), [Android](https://play.google.com/store/apps/details?id=org.kiwix.kiwixmobile) |
| Organic Maps или CoMaps | Офлайн-карты и навигация на телефоне | [organicmaps.app](https://organicmaps.app), [comaps.app](https://www.comaps.app) |
| Homebrew | Установщик программ для Терминала | [brew.sh](https://brew.sh) |

Остальное ставится в Терминале через Homebrew:

```bash
brew install aria2 whisper-cpp uv
uv tool install huggingface_hub
```

`aria2` качает большие файлы с докачкой и проверкой контрольных сумм. `whisper-cpp` расшифровывает речь. `huggingface_hub` даёт команду `hf` для скачивания нейросетей.

Карты своей области скачай в самом приложении на телефоне, пока есть сеть.

## 2. Библиотека Kiwix

Kiwix открывает скачанные копии сайтов. Поиск работает без сети.

| Архив | Что внутри | Размер |
|---|---|---|
| Википедия RU / UK / EN | Энциклопедия с картинками | 37 / 28 / 127 ГБ |
| WikiMed RU / EN | Медицинские статьи Википедии | 0,3 / 1,9 ГБ |
| iFixit RU / EN | Инструкции по ремонту техники | 3,6 / 3,6 ГБ |
| Appropedia | Вода, энергия, самодельные решения | 0,6 ГБ |
| Stack Exchange | Вопросы и ответы: ремонт дома, электроника, компьютеры, Mac | 11,6 ГБ |
| Викигид RU / EN | Путеводители | 0,6 / 0,3 ГБ |
| Викиучебник, Викитека | Учебники и тексты на русском | 4 ГБ |
| Викисловарь RU / UK / EN | Словари для перевода | 2,2 / 0,1 / 9,2 ГБ |
| Project Gutenberg | Книги: русский раздел, медицина, сельское хозяйство, технологии | 19,7 ГБ |

Полный список ссылок — в файле [kiwix.txt](kiwix.txt). Ненужное закомментируй символом `#`. Если места мало, замени английскую Википедию на версию без картинок: 53 ГБ вместо 127.

Скачать всё одной командой:

```bash
aria2c -i kiwix.txt -d ~/Offline/kiwix -j 3 -x 8 -s 8 --follow-metalink=mem -c --auto-file-renaming=false
```

Команду можно прервать и запустить снова: докачает с того же места. Контрольную сумму каждого файла aria2 сверяет сама.

Потом открой Kiwix и добавь файлы из `~/Offline/kiwix`.

Справочник неотложной медицины WikEM в ноябре 2026 пропал из каталога Kiwix. Если вернётся, он будет на [library.kiwix.org](https://library.kiwix.org).

### Раздать библиотеку на телефоны по домашнему Wi‑Fi

```bash
curl -LO https://download.kiwix.org/release/kiwix-tools/kiwix-tools_macos-arm64.tar.gz
tar -xzf kiwix-tools_macos-arm64.tar.gz
./kiwix-tools_macos-arm64-*/kiwix-serve --port 8080 ~/Offline/kiwix/*.zim
```

На телефоне в той же сети открой `http://<адрес Mac>:8080`. Адрес Mac покажет команда `ipconfig getifaddr en0`. Если на Mac включён файрвол, разреши kiwix-serve входящие подключения.

Сервер видят все устройства в сети. Запускай его только дома и не открывай порт на роутере наружу.

## 3. Нейросети

Модели работают в LM Studio. Размер модели упирается в память Mac: модель должна поместиться целиком, ещё нужно место под систему и контекст.

| Модель | Нужно памяти | Скорость у меня | Чем хороша | Чем плоха |
|---|---|---|---|---|
| gpt-oss-120b heretic v2 | 63 ГБ | 67 токенов/с | Самая быстрая, точнее других на задачах с правильным ответом | Чаще других выдумывает |
| Qwen3.8-27B heretic | 30 ГБ | 19 токенов/с | Самая честная, читает фото документов | Думает до 30 секунд перед ответом |
| Qwen3.8-Flash-Next Uncensored | 98 ГБ | 38 токенов/с | Больше всех знаний, понимает картинки | Занимает почти всю память |
| gemma-4-12B | 13 ГБ | не мерил | Для Mac на 16–24 ГБ | Обычная, с фильтрами |

Скорость — на моём Mac с 128 ГБ. В памяти держу одну модель за раз.

heretic и Uncensored — сборки без цензурных фильтров: модель не отказывается отвечать. Ответственность за вопросы и за использование ответов на тебе.

Qwen3.8-27B влезает и в Mac на 48–64 ГБ. Для gpt-oss-120b нужен Mac от 96 ГБ, для Flash-Next — 128 ГБ.

### Скачать

Команды кладут модели прямо в папку LM Studio. Если ты её менял, поправь путь.

gpt-oss-120b, 63 ГБ:

```bash
hf download nightmedia/gpt-oss-120b-heretic-v2-mxfp4-q8-hi-mlx --local-dir ~/.lmstudio/models/nightmedia/gpt-oss-120b-heretic-v2-mxfp4-q8-hi-mlx
```

Qwen3.8-Flash-Next, 98 ГБ. Второй файл нужен, чтобы модель видела картинки:

```bash
hf download mradermacher/Qwen3.8-Flash-Next-Uncensored-i1-GGUF Qwen3.8-Flash-Next-Uncensored.i1-IQ4_XS.gguf --local-dir ~/.lmstudio/models/mradermacher/Qwen3.8-Flash-Next-Uncensored-i1-GGUF
hf download mradermacher/Qwen3.8-Flash-Next-Uncensored-GGUF Qwen3.8-Flash-Next-Uncensored.mmproj-f16.gguf --local-dir ~/.lmstudio/models/mradermacher/Qwen3.8-Flash-Next-Uncensored-i1-GGUF
```

Готовая MLX-сборка этой модели от OrcaRouter весит 163 ГБ и в 128 ГБ не влезает. Поэтому здесь GGUF: LM Studio запускает его так же.

gemma-4-12B для Mac на 16–24 ГБ, 13 ГБ:

```bash
hf download lmstudio-community/gemma-4-12B-it-MLX-8bit --local-dir ~/.lmstudio/models/lmstudio-community/gemma-4-12B-it-MLX-8bit
```

### Qwen3.8-27B: собрать самому

Готовой MLX-сборки от OptimizeLLM я не нашёл, поэтому скачал исходник (56 ГБ) и сжал до 8 бит (30 ГБ). Сжатие у меня заняло пару минут.

```bash
hf download OptimizeLLM/Qwen3.8-27B-heretic-MTP-BF16 --exclude "reproduce/*" --local-dir ~/qwen27-bf16
uv run --with mlx-vlm python -m mlx_vlm.convert --hf-path ~/qwen27-bf16 --mlx-path ~/.lmstudio/models/OptimizeLLM/Qwen3.8-27B-heretic-MLX-8bit -q --q-bits 8
```

Конвертировать надо через `mlx-vlm`: `mlx-lm` выбросит часть модели, которая понимает фото. После конвертации папку `~/qwen27-bf16` можно удалить.

### Проверить

Открой LM Studio, вкладка с моделями: все скачанные должны быть в списке. Загрузи модель и задай вопрос.

Модели ошибаются в цифрах. Я спрашивал у всех дозу парацетамола: все назвали 4 г в сутки, в WikiMed написано 3–4 г. Дозировки и всё, от чего зависит здоровье, проверяй в справочнике.

### Не Mac

LM Studio есть для Windows и Linux, но формат MLX работает только на Mac. Там бери GGUF-сборки: [gpt-oss-120b heretic v2](https://huggingface.co/bartowski/kldzj_gpt-oss-120b-heretic-v2-GGUF) и [Qwen3.8-Flash-Next Uncensored](https://huggingface.co/mradermacher/Qwen3.8-Flash-Next-Uncensored-i1-GGUF). Эти варианты я сам не запускал.

## 4. Распознавание речи

Whisper large-v3 расшифровывает голосовые и записи прямо на Mac. Русскую речь пишет слово в слово, у меня 6 секунд записи обрабатывает за 3 секунды.

```bash
hf download ggerganov/whisper.cpp ggml-large-v3.bin --local-dir ~/Offline/whisper
```

Запись в m4a, например из Диктофона, сначала переведи в WAV, потом расшифруй:

```bash
afconvert -f WAVE -d LEI16@16000 -c 1 voice.m4a voice.wav
whisper-cli -m ~/Offline/whisper/ggml-large-v3.bin -l auto -f voice.wav
```

## 5. Проверка без сети

Выключи Wi‑Fi и пройди по списку:

1. Kiwix открывает статью и находит слово через поиск.
2. LM Studio загружает каждую модель и отвечает.
3. `whisper-cli` расшифровывает запись.
4. Телефон строит маршрут в Organic Maps.

Если что-то не открылось, исправь это сейчас, пока интернет есть.

## Резервная копия

Скопируй набор на внешний диск, чтобы восстановить его без интернета:

```bash
rsync -a ~/Offline/ /Volumes/<диск>/Offline/
rsync -a ~/.lmstudio/models/ /Volumes/<диск>/Offline/lmstudio-models/
```
