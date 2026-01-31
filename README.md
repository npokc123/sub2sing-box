# sub2sing-box

Инструмент для конвертации подписок и прямых ссылок на прокси в конфигурацию sing-box.

## Команды

Используйте `sub2sing-box <command> -h` для просмотра справки по каждой команде.

## Конфигурация

Пример:

```json
{
  "subscriptions": ["адрес_подписки_1", "адрес_подписки_2"],
  "proxies": ["прокси_1", "прокси_2"],
  "template": "путь к шаблону или URL",
  "delete": "оставшийся трафик",
  "rename": { "исходный текст": "новый текст" },
  "group-type": "selector",
  "sort": "name",
  "sort-type": "asc",
  "output": "./config.json"
}
```

Сохраните JSON выше в файл `sub2sing-box.json` и выполните:

```
sub2sing-box convert -c ./sub2sing-box.json
```

Это сгенерирует конфигурацию sing-box без необходимости каждый раз указывать параметры вручную.

## Шаблоны

### Шаблоны по умолчанию

Шаблоны по умолчанию находятся в директории `templates` и используют конфигурацию `tun`. Они предоставлены в качестве примера — измените их под свои нужды.

### Плейсхолдеры

В шаблонах можно использовать следующие плейсхолдеры:

- `<all-proxy-tags>` — вставляет теги всех прокси-нод
- `<all-country-tags>` — вставляет теги всех групп по странам
- `<двухбуквенный код страны>` — вставляет теги всех нод указанной страны, например `<tw>`

#### Примеры использования плейсхолдеров:

Допустим, есть ноды:

- US
- SG
- TW

```json
{
  "type": "selector",
  "tag": "Выбор ноды",
  "outbounds": ["<all-proxy-tags>", "direct"],
  "interrupt_exist_connections": true
}

// После конвертации

{
  "type": "selector",
  "tag": "Выбор ноды",
  "outbounds": ["US", "SG", "TW", "direct"],
  "interrupt_exist_connections": true
}
```

```json
{
  "type": "selector",
  "tag": "Выбор ноды",
  "outbounds": ["<all-country-tags>", "direct"],
  "interrupt_exist_connections": true
}

// После конвертации

{
  "type": "selector",
  "tag": "Выбор ноды",
  "outbounds": ["美国(US)", "新加坡(SG)", "台湾(TW)", "direct"],
  "interrupt_exist_connections": true
}

// Где "美国(US)", "新加坡(SG)", "台湾(TW)" — группы стратегий, содержащие ноды US, SG, TW соответственно
```

```json
{
  "type": "selector",
  "tag": "Bahamut",
  "outbounds": ["<tw>", "direct"],
  "interrupt_exist_connections": true
}

// После конвертации

{
  "type": "selector",
  "tag": "Bahamut",
  "outbounds": ["台湾(TW)", "direct"],
  "interrupt_exist_connections": true
}
```

## Использование Docker

```
docker run -p 8080:8080 nite07/sub2sing-box:latest
```

Можно смонтировать директорию для добавления пользовательских шаблонов.

## API режима Server

### GET /convert

| query | Описание                                                                                                                          |
| ----- | --------------------------------------------------------------------------------------------------------------------------------- |
| data  | Аналогично конфигурации выше, но в кодировке [base64 URL safe](<https://gchq.github.io/CyberChef/#recipe=To_Base64('A-Za-z0-9%2B/%3D')>) |
