1. Запустите Blackbox exporter. Пример конфигурации ниже
```docker-compose
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

```blackbox.yml
modules:
  http_2xx:
    prober: http
    timeout: 30s
    http:
      method: GET
      preferred_ip_protocol: ip4
  http_2xx_w_basic_auth:
    prober: http
    timeout: 30s
    http:
      method: GET
      preferred_ip_protocol: ip4
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