# Changelog

# [v1.6.1] - 01.07.2025
## Added
* Добавлена обработка ошибок у управляемых сервисов на этапе создания (K8S/DB)

# [v1.6.0] - 27.06.2025
## Added
* Добавлен автохилинг для K8S
* Добавлен автоскейлинг в update для K8S

# [v1.5.9] - 18.06.2025
## Added
* Конфигуратор для master нод K8S

# [v1.5.8] - 22.05.2025
## Fixed
Исправлен поиск DNS записей (сделан дополнительный поиск в dns записях по FQDN и Type записи)

## Fixed
* Исправлена простановка preset ID при чтении ответа для кластеров БД

# [v1.5.7] - 07.05.2025

## Fixed
* Исправлена простановка preset ID при чтении ответа для кластеров БД

# [v1.5.6] - 07.05.2025

## Added
* Добавлена возможность устанавливать дополнения и редактировать манифесты для K8S кластеров 

# [v1.5.5] - 06.05.2025

## Fixed
* Исправлено создание Redis
* Поправлена документация в базах данных с примером привязки приватной сети

# [v1.5.4] - 29.04.2025

## Added
* Поддержка конфигураторов в ресурсе K8S

# [v1.5.3] - 28.04.2025

## Added
* Добавлена возможность устанавливать тейнты и лейблы для группы нод в K8S

# [v1.5.2] - 04.04.2025

## Added
* В балансировщиках добавлена возможность установить плавающий IP

# [v1.5.1] - 04.04.2025

## Added
* Новые поля haproxy в балансировщиках

# [v1.5.0] - 04.04.2025

## Added
* Дата фильтр на конфигураторы S3
* Поддержка конфигураторов в ресурсе S3


# [v1.4.5] - 27.03.2025

## Added
* Добавлена возможность передать идентификатор VPC сети в K8s

## Fixed
* Поправлено поле is_autoscaling в K8s, теперь оно действительно необязательное


# [v1.4.4] - 20.03.2025

## Fixed
* Создание Postgres

# [v1.4.3] - 19.03.2025

## Added
* Обработка ошибок при попытке использования устаревших типов баз данных

# [v1.4.2] - 14.03.2025

## Added
* В пресетах K8S добавлена возможность указать локацию

## Fixed
* Исправлена невозможность создавать новые сервисы в проектах с токеном дополнительного пользователя


# [v1.4.1] - 21.02.2025

## Fixed
* Исправлена невозможность изменить логин пользователя в ресурсе twc_database_user

# [v1.4.0] - 12.02.2025

## Added
* Добавлен ресурс сетевых дисков
* Добавлен фильтр сетевых дисков
* Добавлен фильтр пресетов сетевых дисков


# [v1.3.18] - 10.02.2025

## Added
* В пресетах VDS добавлено поле preset_type для удобного выбора тарифной линейки. Поле disk_type помечено как устаревшее.

# [v1.3.17] - 06.02.2025

## Added
* Поддержка создания VDS без пароля
* Получение root пароля VDS в манифесте

## [v1.3.16] - 03.02.2025

## Added
* Поддержка новых тарифных линеек VDS-конфигуратора (Dedicated CPU, GPU)
* В конфигураторы добавлено поле preset_type для удобного выбора тарифной линейки. Поле disk_type помечено как устаревшее.

## [v1.3.15] - 24.01.2025

## Added
* Поддержка автоскейлинга в k8s
* Вывод ошибки при попытке изменить кастомный пресет

## [v1.3.14] - 10.01.2025

## Added
* Возможность создания кластеров баз данных с репликацией

## [v1.3.13] - 27.12.2024

## Fixed
* Обновили документацию примером импорта плавающего IP
* Исправили ошибку, из-за которой невозможно было привязать файрвол к базам данным и балансировщикам
* Поправили ссылку в документации на DNS

## [v1.3.12] - 25.12.2024

## Added

* Добавили возможность указать зону доступности при фильтрации пресетов
* Добавили описание ошибки при несовпадении пресета с зоной

## Fixed
* Исправили ошибку, из-за которой в некоторых случаях нельзя было удалить файрвол
* Исправили ошибку, из-за которой невозможно было создать приватную сеть для некоторых конфигураций облачных серверов

## [v1.3.11] - 23.12.2024

## Added

* Динамическое получение типов баз данных
* Фикс создания баз mongoDB 6 и mongoDB 7

## [v1.3.10] - 23.12.2024

## Added

* Новые локации `nl-1` и `ru-3` для VPC

## [v1.3.9] - 10.12.2024

## Added

* Новые локации `nl-1` и `ru-3` для VPC

## [v1.3.8] - 22.08.2024

## Added

* Для ресурса twc_k8s_cluster актуализированы статусы

## [v1.3.7] - 06.08.2024

## Added

* Для ресурса twc_server добавлено поле floating_ip_id, которое привязывает IP к серверу в момент создания и позволяет использовать сеть во время работы Cloud-init

## [v1.3.6] - 30.05.2024

## Fixed

* Исправлен тип ID ресурса для плавающего IP-адреса

## [v1.3.5] - 16.04.2024

## Fixed

* Исправлена ошибка при параллельном удалении большого числа файлов из S3;

## [v1.3.4] - 25.03.2024

## Fixed

* При превышении лимита запросов к API провайдер автоматически будет выполнять повторный запрос вместо аварийного завершения (для 503 ошибок);

## [v1.3.3] - 13.03.2024

## Added

* Добавлена поддержка СУБД PostgreSQL 15 и ClickHouse для ресурса `twc_database_cluster`;

## Fixed

* При превышении лимита запросов к API провайдер автоматически будет выполнять повторный запрос вместо аварийного завершения;

## [v1.3.2] - 04.03.2024

### Fixed

* Числовой тип заменен на строков в качестве ожидаемого ID ресурса для floating_ip;
* Исправлено создание node_group c именем default для кластеров K8S;

## [v1.3.1] - 09.02.2024

### Fixed

- Исправлена 409 ошибка при связывании ресурса с Firewall
- Исправлено создание кластера базы данных с сетью

## [v1.3.0] - 22.01.2024

### Added

- Ресурс для управления плавающими IP-адресами
- Источник данных для получения плавающих IP-адресов
- Параметр `availability_zone` для ресурса `twc_server`
- Параметр `availability_zone` для ресурса `twc_lb`
- Параметр `availability_zone` для ресурса `twc_database_cluster`

### Fixed

- Исправлена 429 ошибка при удалении Firewall

## [v1.2.3] - 19.01.2024

### Fixed

- Исправлена ошибка из-за которой не менялся пароль пользователя БД без привилегий

## [v1.2.2] - 18.01.2024

### Fixed

- Исправлена 409 при добавлении к Firewall ресурсов сразу после создания

## [v1.2.1] - 22.12.2023

### Added

- Добавлен параметр `is_external_ip` для управляемых кластеров БД

### Fixed

- Исправлен валидатор типа значения для `network.0.id` в управляемых кластерах БД из-за которого нельзя было создать кластер в приватной сети

## [v1.2.0] - 14.12.2023

### Added

- Новые ресурсы для работы с управляемыми БД

## [v1.1.0] - 25.10.2023

### Added

- Возможность передачи cloud-init для сервера

## [v1.0.0] - 04.09.2023

### Added

- Добавлена поддержка пагинации в ресурсах

## [v0.0.14] - 25.08.2023

### Fixed

- Исправлена ошибка при обновлении сервера без изменения образа или ОС
- Исправлена 429 ошибка при создании Firewall со множеством правил

## [v0.0.13] - 16.08.2023

### Added

- Поддержка образов при создании серверов через image_id
- Источник данных для получения образов twc_image
- Поле для получения основного IPv4 адреса сервера

## [v0.0.12] - 27.07.2023

### Added

- Добавлена поддержка бекапов для управляемых БД
- Добавлена поддержка конфигурации автоматических бекапов БД
- Добавлена поддержка VPC
- Для управляемого кластера K8S добавлена переменная с kubeconfig

### Fixed

- Исправлены описания в документации ресурсов

## [v0.0.11] - 12.07.2023

### Added

- Добавлена поддержка управляемого Firewall

## [v0.0.10] - 11.07.2023

### Added

- Добавлена поддержка управляемых кластеров балансировщиков
- Добавлены новые параметры конфигурации управляемых БД

### Fixed

- Исправлена документация по ресурсу SSH-ключей
- Исправлена обработка пустых строк в качестве параметров БД от API
- Добавлена обработка no_paid статуса при создании БД

## [v0.0.9] - 01.06.2023

### Added

- Добавлена поддержка управляемых кластеров K8S

## [v0.0.8] - 24.05.2023

### Fixed

- Исправлена проблема при создании VDS с локальной сетью

## [v0.0.7] - 15.05.2023

### Added

- Добавлен ресурс управления DNS-записями
- Добавлен источник данных для DNS-зон
- Добавлен CHANGELOG.md

## [v0.0.6] - 26.04.2023

### Fixed

- Исправлена проблема с отсутствием данных после создания ресурса БД

## [v0.0.5] - 19.04.2023

- Добавлена поддержка управляемой СУБД MySQL 5.7
- Добавлена поддержка управляемой СУБД MySQL 8
- Добавлена поддержка управляемой СУБД Postgres
- Добавлена поддержка управляемой СУБД Redis
- Добавлена поддержка управляемой СУБД MongoDB

## [v0.0.4] - 05.04.2023

- Добавлена поддержка управления проектами
- Добавлена поддержка управления S3-хранилищами

## [v0.0.3] - 28.03.2023

### Added

- Добавлена обработка ошибок создания сервера при недостаточном балансе

### Fixed

- Исправлена ошибка создания сервера с SSH-ключом

## [v0.0.2] - 22.03.2023

### Added

- В документацию добавлена информация об отключении подтверждения удаления токена
- Добавлена возможность импорта существующих ресурсов в состояние Terraform
- Добавлена поддержка управления SSH-ключами на созданном сервере
- Добавлена возможность управления SSH-ключами через ресурс twc_ssh_key

### Changed

- Изменено сообщение об ошибке при пустом или отсутствующем токене

## [v0.0.1] - 09.03.2023

Первый выпуск Terraform-провайдера к инфраструктуре Timeweb Cloud.

### Added

- `twc_server` – ресурс управления сервером;
- `twc_server_ip` – ресурс управления дополнительными IP адресами сервера;
- `twc_server_disk` – ресурс управление дополнительными дисками для сервера;
- `twc_server_disk_backup` – ресурс управления резервными копиями основного и дополнительного диска сервера;
- `twc_server_disk_backup_schedule` – ресурс настройки автоматически создаваемых резервных копий основного и дополнительного диска сервера;
- `twc_configurator`– источник данных для ручного выбора требуемых ресурсов VDS;
- `twc_os` – источник данных ОС для установки на сервер;
- `twc_presets` – источник данных пресетов конфигураций сервера;
- `twc_projects` – источник данных проектов аккаунта;
- `twc_software` – источник данных ПО для установки на сервер;
- `twc_ssh_keys` – источник данных SSH-ключей для установки на сервер;
