1. Запустите Blackbox exporter. Пример для конфигурации docker compose ниже

docker-compose.yaml
```yaml
services:
  blackbox-exporter:
    image: prom/blackbox-exporter:v0.26.0
    volumes:
      - ${PWD}/blackbox.yml:/config/blackbox.yml
    command:
      - '--config.file=/config/blackbox.yml'
    restart: always
    ports:
      - "9115:9115"
```
blackbox.yml
```yaml
modules:
  http_2xx:
    prober: http
    timeout: 30s
    http:
      method: GET
      preferred_ip_protocol: ip4
      valid_status_codes: []
  http_2xx_w_basic_auth:
    prober: http
    timeout: 30s
    http:
      method: GET
      preferred_ip_protocol: ip4
      valid_status_codes: []
      basic_auth:
        username: username
        password: password
```

2. В Zabbix создайте новый хост для сайта, который мониторите (1 сайт = 1 хост)
3. В Macros этого хоста заполните

| Macro                                 | Пример значения          | Описание                                                                                                                                                                              |
| :------------------------------------ | ------------------------ | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| {$BLACKBOX_EXPORTER_METRICS_ENDPOINT} | http://192.168.0.10:9115 | Эндпоинт Blackbox Exporter                                                                                                                                                            |
| {$BLACKBOX_EXPORTER_MODULE}           | http_2xx                 | Имя используемого модуля Blackbox Exporter. Дополнительные модули, например http_2xx_w_basic_auth, нужны для добавления кастомных параметров, например логин и пароль HTTP Basic Auth |
| {$BLACKBOX_EXPORTER_TARGET_ENDPOINT}  | http://google.com        | Эндпоинт, который будет мониторить Blackbox Exporter                                                                                                                                  |
|                                       |                          |                                                                                                                                                                                       |

Макросы {$BLACKBOX_EXPORTER_METRICS_ENDPOINT} и {$BLACKBOX_EXPORTER_MODULE} для большинства сайтов будут одинаковые. Их можно определить или на уровне шаблона, или на уровне Global Value ( Administration -> General -> Macros )
Есть проверка на срок действия сертификата, но в Zabbix 6.0 она не работает. А в 7.0 работает.
