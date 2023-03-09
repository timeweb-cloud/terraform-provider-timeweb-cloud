# Timeweb Cloud | Terraform Provider Docs

Terraform позволяет автоматизированно управлять множеством ресурсов в Timeweb Cloud с помощью файлов удобных файлов конфигурации формата HCL (HashiCorp Configuration Language) и детальных планов вносимых изменений.

Подробную информацию о ресурсах провайдера можно получить в документации на [сайте Terraform](https://developer.hashicorp.com/terraform/docs).

## Документация

Подробная документация по параметрам ресурсов и источников данных провайдера

* Ресурсы

  * [twc_server](docs/resources/server.md) - сервер;
  * [twc_server_ip](docs/resources/server_ip.md) - дополнительный IP адрес для сервера;
  * [twc_server_disk](docs/resources/server_disk.md) - дополнительный диск для сервера;
  * [twc_server_disk_backup](docs/resources/server_disk_backup.md) - резервная копия  основного и дополнительного диска сервера;
  * [twc_server_disk_backup_schedule](docs/resources/server_disk_backup_schedule.md) - настройки автоматически создаваемых резервных копий основного и дополнительного диска сервера;

* Источники данных

  * [twc_configurator](docs/data-sources/configurator.md) - конфигураторы для ручного выбора требуемых ресурсов VDS;
  * [twc_os](docs/data-sources/os.md) - ОС для установки на сервер;
  * [twc_presets](docs/data-sources/presets.md) - пресеты конфигураций сервера;
  * [twc_projects](docs/data-sources/projects.md) - проекты аккаунта;
  * [twc_software](docs/data-sources/software.md) - ПО для установки на сервер;
  * [twc_ssh_keys](docs/data-sources/ssh_keys.md) - SSH-ключи для установки на сервер;

## Быстрый старт

### Установите Terraform

Вы можете установить Terraform согласно [официальной инструкции](https://developer.hashicorp.com/terraform/tutorials/aws-get-started/install-cli?in=terraform%2Faws-get-started)

### Создание конфигурации Terraform

1. Создайте новую директорию с произвольным названием, к примеру: `timeweb-cloud-terraform`;

2. В созданной директории добавьте конфигурационный файл с расширением `.tf`, например: `main.tf`.

### Добавление провайдера

1. В начале конфигурационного файла `main.tf` добавьте следующий блок:

    ```
    terraform {
      required_providers {
        twc = {
          source = "tf.timeweb.cloud/timeweb-cloud/timeweb-cloud"
        }
      }
      required_version = ">= 0.13"
    }
    ```

    > Провайдер поддерживает только версии Terraform 0.13 и выше, на версиях ниже работа провайдера не поддерживается

2. Выполните `terraform init` для скачивания провайдера

### Установка API-токена для работы с провайдером

Для работы с провайдером Terraform требуется получения токена API в [соответствующем разделе Панели управления](https://timeweb.cloud/my/api-keys) для доступа к ресурсам аккаунта.

После создания токена им можно воспользоваться одним из указанных способов:

Добавьте созданный токен в переменные окружения для его использования в Terraform:

```sh
export TWC_TOKEN=eyJhbGc...
```

или

Добавьте созданный токен в качестве параметра провайдера:

```terraform
provider "twc" {
  token = "eyJhbGci..."
}
```

### Подготовка конфигурации

При помощи Terraform в Timeweb Cloud можно управлять различными типами типы ресурсов. Подробная информация о каждом ресурсе и источнике данных находится в отдельных разделах данной документации

В качестве примера будет применена конфигурация ниже, в рамках которой будет создана виртуальная машина с именем `Example server` c NVMe диском на 10 Гб, 1 ядром CPU и 1 Гб RAM с установленной ОС `Ubuntu 22.04` в локации `ru-1`.

```terraform
terraform {
  required_providers {
    twc = {
      source = "tf.timeweb.cloud/timeweb-cloud/timeweb-cloud"
    }
  }
  required_version = ">= 0.13"
}

data "twc_configurator" "configurator" {
  location = "ru-1"
  disk_type = "nvme"
}

data "twc_os" "os" {
  name = "ubuntu"
  version = "22.04"
}
resource "twc_server" "example-server" {
  name = "Example server"
  os_id = data.twc_os.os.id

  configuration {
    configurator_id = data.twc_configurator.configurator.id
    disk = 1024 * 10
    cpu = 1
    ram = 1024
  }
}
```

> Для работы данного примера требуется установленная переменная окружения TWC_TOKEN

Для доступа у созданной VDS потребуется пароль, который после создания сервера будет отправлен на почту. Как альтернативный вариант можно использовать доступ по SSH, подробнее о котором можно узнать в документации по ресурсу `twc_server` и источнику данных `twc_ssh_keys`.


### Проверка файлов конфигурации

Проверка конфигурации выполняется конфигурацию командой:

```sh
terraform validate
```

Если конфигурация является допустимой, появится сообщение:

```
Success! The configuration is valid.
```

> Другие команды `terraform` можно узнать из [официальной документации Terraform](https://developer.hashicorp.com/terraform/cli)

### Применение файлов конфигурации

1. Для составления плана применения конфигурации выполните команду:

    ```sh
    terraform plan
    ```

    В терминале будет выведен список планируемых изменений. На этом этапе конфигурация применена не будет

2. Чтобы применить конфигурацию и создать ресурсы выполните команду:

    ```sh
    terraform apply
    ```

3. Подтверждение создания ресурсов

  Перед применением конфигурации Terraform повторно выведет план вносимых изменений и запросит ручное подтверждение перед началом своей работы:

  ```sh
  Do you want to perform these actions?
    Terraform will perform the actions described above.
    Only 'yes' will be accepted to approve.

    Enter a value:
  ```

  введите в терминал слово `yes` и нажмите *Enter*.

  После применения конфигурации Terraform выведет результат применения конфигурации, который можно проверить в [Панели управления](https://timeweb.cloud/my)

### Удаление созданных ресурсов

Чтобы удалить все ресурсы, созданные через Terraform:

1. Выполните команду;
    ```sh
    terraform destroy
    ```
    Во время выполнения будет выведен план удаления всех созданных ресурсов и Terraform запросит ручное подтверждение перед началом своей работы:

    ```sh
    Do you really want to destroy all resources?
      Terraform will destroy all your managed infrastructure, as shown above.
      There is no undo. Only 'yes' will be accepted to confirm.

      Enter a value:
    ```

2. Введите слово `yes` и нажмите *Enter*
