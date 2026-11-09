# Полный набор через Терминал

Это мой набор: около 450 ГБ, для Mac с памятью 128 ГБ. Здесь нужен Терминал, но достаточно копировать команды. Если памяти меньше, пропусти модели, которые не подходят: таблица в [README](README.md).

Простой путь без Терминала описан в [README](README.md). Можно начать с него, а отсюда взять только то, чего там нет.

## Как пользоваться Терминалом

1. Нажми ⌘ Пробел, набери «Терминал» и нажми Enter.
2. Скопируй команду из серого блока: на GitHub у блока справа есть кнопка копирования, или выдели команду мышкой и нажми ⌘C. Вставь в Терминал ⌘V и нажми Enter. Команды вставляй по одной.
3. Жди, пока в конце снова появится строка со знаком `%`. Это значит, что команда закончилась.
4. Остановить команду: Control+C.
5. Если Терминал просит пароль, вводи пароль от Mac. Символы при вводе не видны, так и должно быть.

`~` в командах — твоя домашняя папка, в Finder это значок домика. Папку `~/Offline` команды создадут сами.

Загрузки идут часами. Команды начинаются с `caffeinate -i`: пока команда работает, Mac не уснёт. Крышку не закрывай, держи Mac на зарядке.

## 1. Инструменты

Homebrew устанавливает программы командами:

```bash
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"
```

Установщик попросит пароль от Mac и нажать Enter. Если откроется окно с предложением установить Command Line Tools, нажми «Установить» и дождись конца, это несколько минут. Если после этого установщик остановился, запусти команду ещё раз.

В конце установщик покажет раздел Next steps: там две-три строки, которые начинаются с `echo` и `eval`. Скопируй их и выполни, без них команда `brew` не найдётся. Проверка: команда `brew --version` печатает номер версии.

Теперь три программы:

```bash
brew install aria2 whisper-cpp hf
```

- `aria2` качает большие файлы с докачкой и сама проверяет, что файл скачался целиком;
- `whisper-cpp` расшифровывает речь;
- `hf` скачивает нейросети с сайта Hugging Face.

Закрой Терминал и открой заново. Проверка: `hf --help` печатает справку.

## 2. Библиотека Kiwix, 250 ГБ

Скачай список архивов в домашнюю папку:

```bash
cd ~
curl -LO https://raw.githubusercontent.com/Di-kairos/offline-kit/main/kiwix.txt
```

Если места мало, открой список в TextEdit командой `open -e kiwix.txt` и удали строки с ненужными архивами. Английская Википедия с картинками весит 127 ГБ. Чтобы взять версию без картинок на 53 ГБ, поставь `#` в начало строки `wikipedia_en_all_maxi` и убери `#` в начале строки `wikipedia_en_all_nopic` под ней. Сохрани: ⌘S.

Запусти загрузку:

```bash
caffeinate -i aria2c -i kiwix.txt -d ~/Offline/kiwix -j 3 -x 8 -s 8 --follow-metalink=mem -c --auto-file-renaming=false
```

В конце aria2 напишет таблицу Download Results. У каждого файла должно стоять OK. Если где-то ERR или загрузка оборвалась, запусти ту же команду ещё раз: она докачает с того же места.

Открой архивы в Kiwix: «Открыть...» (Open...), в окне выбора нажми ⌘⇧G, введи `~/Offline/kiwix` и выбери файлы.

## 3. Нейросети

Перед загрузкой хотя бы раз запусти LM Studio. Команды кладут модели в его папку, после этого LM Studio видит их сам. Запускай команды по одной.

**gpt-oss-120b**, 63 ГБ. Нужен Mac от 96 ГБ:

```bash
caffeinate -i hf download nightmedia/gpt-oss-120b-heretic-v2-mxfp4-q8-hi-mlx --local-dir ~/.lmstudio/models/nightmedia/gpt-oss-120b-heretic-v2-mxfp4-q8-hi-mlx
```

**Qwen3.8-Flash-Next**, 98 ГБ. Нужен Mac на 128 ГБ. Первая команда качает саму модель, вторая — файл на 1 ГБ, с которым модель понимает картинки:

```bash
caffeinate -i hf download mradermacher/Qwen3.8-Flash-Next-Uncensored-i1-GGUF Qwen3.8-Flash-Next-Uncensored.i1-IQ4_XS.gguf --local-dir ~/.lmstudio/models/mradermacher/Qwen3.8-Flash-Next-Uncensored-i1-GGUF
```

```bash
caffeinate -i hf download mradermacher/Qwen3.8-Flash-Next-Uncensored-GGUF Qwen3.8-Flash-Next-Uncensored.mmproj-f16.gguf --local-dir ~/.lmstudio/models/mradermacher/Qwen3.8-Flash-Next-Uncensored-i1-GGUF
```

Если модель уже скачана через поиск LM Studio, выполни только вторую команду.

Модели gemma проще скачать через поиск LM Studio, как в README.

### Qwen3.8-27B: для опытных, можно пропустить

Готовой версии для Mac нет: скачиваешь исходник на 56 ГБ и сжимаешь его до 30 ГБ. Нужен Mac от 48 ГБ памяти и 90 ГБ свободного места на время сборки.

```bash
brew install uv
caffeinate -i hf download OptimizeLLM/Qwen3.8-27B-heretic-MTP-BF16 --exclude "reproduce/*" --local-dir ~/qwen27-bf16
uv run --with mlx-vlm python -m mlx_vlm.convert --hf-path ~/qwen27-bf16 --mlx-path ~/.lmstudio/models/OptimizeLLM/Qwen3.8-27B-heretic-MLX-8bit -q --q-bits 8
```

Сжимать надо именно через `mlx-vlm`: обычный `mlx-lm` выбросит часть модели, которая читает фото. Когда появится модель в LM Studio, исходник можно удалить: `rm -rf ~/qwen27-bf16`.

## 4. Распознавание речи

```bash
caffeinate -i hf download ggerganov/whisper.cpp ggml-large-v3.bin --local-dir ~/Offline/whisper
```

Как расшифровать запись: перетащи её из Диктофона на Рабочий стол и переименуй в `voice`, чтобы файл назывался `voice.m4a`. Потом:

```bash
afconvert -f WAVE -d LEI16@16000 -c 1 ~/Desktop/voice.m4a ~/Desktop/voice.wav
whisper-cli -m ~/Offline/whisper/ggml-large-v3.bin -l auto -f ~/Desktop/voice.wav
```

Текст появится прямо в окне Терминала. Язык записи модель определяет сама.

## 5. Библиотека на телефонах по домашнему Wi‑Fi: можно пропустить

Mac раздаёт библиотеку, а телефоны открывают её в браузере, без своего Kiwix.

```bash
cd ~
curl -LO https://download.kiwix.org/release/kiwix-tools/kiwix-tools_macos-arm64.tar.gz
tar -xzf kiwix-tools_macos-arm64.tar.gz
./kiwix-tools_macos-arm64-*/kiwix-serve --port 8080 ~/Offline/kiwix/*.zim
```

Узнай адрес Mac в сети: в новом окне Терминала выполни `ipconfig getifaddr en0`, получишь что-то вроде `192.168.1.5`. На телефоне в той же Wi‑Fi сети открой в браузере `http://192.168.1.5:8080`, подставив свой адрес.

Библиотека работает, пока окно Терминала открыто. Остановить: Control+C. Если macOS спросит, разрешить ли входящие подключения, разреши. Делай это только дома: библиотеку видят все устройства в сети.

## 6. Резервная копия на внешний диск

Подключи диск. Подставь его имя, как в Finder, и оставь кавычки:

```bash
rsync -a ~/Offline/ "/Volumes/Имя диска/Offline/"
rsync -a ~/.lmstudio/models/ "/Volumes/Имя диска/Offline/lmstudio-models/"
```

Если macOS спросит, разрешить ли Терминалу доступ к диску, разреши. Диск должен быть в формате APFS или ExFAT: на FAT32 файлы больше 4 ГБ не поместятся. Храни диск отдельно от Mac.

## Не Mac

Kiwix и LM Studio есть для Windows и Linux. Версии моделей с MLX в названии работают только на Mac, там бери GGUF: [gpt-oss-120b heretic v2](https://huggingface.co/bartowski/kldzj_gpt-oss-120b-heretic-v2-GGUF) и [Qwen3.8-Flash-Next Uncensored](https://huggingface.co/mradermacher/Qwen3.8-Flash-Next-Uncensored-i1-GGUF). Эти варианты я сам не запускал.
