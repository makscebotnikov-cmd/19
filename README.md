# Домашнее задание к занятию 23.3 "`Безопасность в облачных провайдерах`" - `Чеботников М.Б.`

Используя конфигурации, выполненные в рамках предыдущих домашних заданий, нужно добавить возможность шифрования бакета.

---
## Задание 1. Yandex Cloud   

1. С помощью ключа в KMS необходимо зашифровать содержимое бакета:

 - создать ключ в KMS;
 - с помощью ключа зашифровать содержимое бакета, созданного ранее.
2. (Выполняется не в Terraform)* Создать статический сайт в Object Storage c собственным публичным адресом и сделать доступным по HTTPS:

 - создать сертификат;
 - создать статическую страницу в Object Storage и применить сертификат HTTPS;
 - в качестве результата предоставить скриншот на страницу с сертификатом в заголовке (замочек).

Полезные документы:

- [Настройка HTTPS статичного сайта](https://cloud.yandex.ru/docs/storage/operations/hosting/certificate).
- [Object Storage bucket](https://registry.terraform.io/providers/yandex-cloud/yandex/latest/docs/resources/storage_bucket).
- [KMS key](https://registry.terraform.io/providers/yandex-cloud/yandex/latest/docs/resources/kms_symmetric_key).

---

### Ответ:

** Terraform plan **  
<details>
<summary>развернуть Terraform Plan</summary>  

```
user-test@ubuntu-26-hw:~/Netology/Terraform$ terraform plan
yandex_kms_symmetric_key.storage_kms_key: Refreshing state... [id=abj4h82nkr2jibfjhfo9]

Terraform used the selected providers to generate the following execution plan. Resource actions are indicated with the following symbols:
  + create

Terraform will perform the following actions:

  # yandex_storage_bucket.hw23-3_bucket will be created
  + resource "yandex_storage_bucket" "hw23-3_bucket" {
      + acl                   = "public-read"
      + bucket                = "index-hw23-3-static-site"
      + bucket_domain_name    = (known after apply)
      + default_storage_class = (known after apply)
      + folder_id             = (known after apply)
      + force_destroy         = false
      + id                    = (known after apply)
      + website_domain        = (known after apply)
      + website_endpoint      = (known after apply)

      + anonymous_access_flags {
          + list = false
          + read = true
        }

      + server_side_encryption_configuration {
          + rule {
              + apply_server_side_encryption_by_default {
                  + kms_master_key_id = "abj4h82nkr2jibfjhfo9"
                  + sse_algorithm     = "aws:kms"
                }
            }
        }

      + versioning (known after apply)

      + website {
          + index_document = "index.html"
        }
    }

  # yandex_storage_object.hw23-3_image will be created
  + resource "yandex_storage_object" "hw23-3_image" {
      + acl          = "public-read"
      + bucket       = "index-hw23-3-static-site"
      + content_type = "image/jpeg"
      + id           = (known after apply)
      + key          = "image.jpg"
      + source       = "image.jpg"
    }

  # yandex_storage_object.hw23-3_index will be created
  + resource "yandex_storage_object" "hw23-3_index" {
      + acl          = "public-read"
      + bucket       = "index-hw23-3-static-site"
      + content_type = "text/html"
      + id           = (known after apply)
      + key          = "index.html"
      + source       = "index.html"
    }

  # yandex_vpc_network.homework-network will be created
  + resource "yandex_vpc_network" "homework-network" {
      + created_at                = (known after apply)
      + default_security_group_id = (known after apply)
      + folder_id                 = (known after apply)
      + id                        = (known after apply)
      + labels                    = (known after apply)
      + name                      = "homework-network"
      + subnet_ids                = (known after apply)
    }

  # yandex_vpc_route_table.private-rt will be created
  + resource "yandex_vpc_route_table" "private-rt" {
      + created_at = (known after apply)
      + folder_id  = (known after apply)
      + id         = (known after apply)
      + labels     = (known after apply)
      + name       = "private-rt"
      + network_id = (known after apply)

      + static_route {
          + destination_prefix = "0.0.0.0/0"
          + next_hop_address   = "192.168.10.254"
            # (1 unchanged attribute hidden)
        }
    }

  # yandex_vpc_subnet.private-subnet will be created
  + resource "yandex_vpc_subnet" "private-subnet" {
      + created_at     = (known after apply)
      + folder_id      = (known after apply)
      + id             = (known after apply)
      + labels         = (known after apply)
      + name           = "private"
      + network_id     = (known after apply)
      + route_table_id = (known after apply)
      + v4_cidr_blocks = [
          + "192.168.20.0/24",
        ]
      + v6_cidr_blocks = (known after apply)
      + zone           = "ru-central1-a"
    }

  # yandex_vpc_subnet.public-subnet will be created
  + resource "yandex_vpc_subnet" "public-subnet" {
      + created_at     = (known after apply)
      + folder_id      = (known after apply)
      + id             = (known after apply)
      + labels         = (known after apply)
      + name           = "public"
      + network_id     = (known after apply)
      + v4_cidr_blocks = [
          + "192.168.10.0/24",
        ]
      + v6_cidr_blocks = (known after apply)
      + zone           = "ru-central1-a"
    }

Plan: 7 to add, 0 to change, 0 to destroy.

Changes to Outputs:
  + bucket_name = "index-hw23-3-static-site"
  + image_url   = "https://index-hw23-3-static-site.storage.yandexcloud.net/image.jpg"
  + kms_key_id  = "abj4h82nkr2jibfjhfo9"
  + website_url = "http://index-hw23-3-static-site.website.yandexcloud.net"

──────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────

Note: You didn't use the -out option to save this plan, so Terraform can't guarantee to take exactly these actions if you run "terraform apply" now.
```

</details>

** Terraform apply **    
```
Apply complete! Resources: 8 added, 0 changed, 0 destroyed.

Outputs:

bucket_name = "index-hw23-3-static-site"
image_url = "https://index-hw23-3-static-site.storage.yandexcloud.net/image.jpg"
kms_key_id = "abjp6047vb93ht5e17pf"
website_url = "http://index-hw23-3-static-site.website.yandexcloud.net"
```

** Проверяем в Yandex Cloud наш бакет **      

<img width="1601" height="537" alt="1" src="https://github.com/user-attachments/assets/bb7a673a-28c8-484e-8011-7b46da40906b" />



** Проверяем в Yandex Cloud наш ключ **      

<img width="1805" height="318" alt="2" src="https://github.com/user-attachments/assets/17066e56-0785-4720-b83d-06f79ab2e2dd" />



** Проверяем в Yandex Cloud что объект зашифрован **      

<img width="1834" height="520" alt="3" src="https://github.com/user-attachments/assets/c0739c00-06b8-4ede-a3c3-9edfa1bb59ea" />




--- 
## Задание 2*. AWS (задание со звёздочкой)

Это необязательное задание. Его выполнение не влияет на получение зачёта по домашней работе.

**Что нужно сделать**

1. С помощью роли IAM записать файлы ЕС2 в S3-бакет:
 - создать роль в IAM для возможности записи в S3 бакет;
 - применить роль к ЕС2-инстансу;
 - с помощью bootstrap-скрипта записать в бакет файл веб-страницы.
2. Организация шифрования содержимого S3-бакета:

 - используя конфигурации, выполненные в домашнем задании из предыдущего занятия, добавить к созданному ранее бакету S3 возможность шифрования Server-Side, используя общий ключ;
 - включить шифрование SSE-S3 бакету S3 для шифрования всех вновь добавляемых объектов в этот бакет.

3. *Создание сертификата SSL и применение его к ALB:

 - создать сертификат с подтверждением по email;
 - сделать запись в Route53 на собственный поддомен, указав адрес LB;
 - применить к HTTPS-запросам на LB созданный ранее сертификат.

Resource Terraform:

- [IAM Role](https://registry.terraform.io/providers/hashicorp/aws/latest/docs/resources/iam_role).
- [AWS KMS](https://registry.terraform.io/providers/hashicorp/aws/latest/docs/resources/kms_key).
- [S3 encrypt with KMS key](https://registry.terraform.io/providers/hashicorp/aws/latest/docs/resources/s3_bucket_object#encrypting-with-kms-key).

Пример bootstrap-скрипта:

```
#!/bin/bash
yum install httpd -y
service httpd start
chkconfig httpd on
cd /var/www/html
echo "<html><h1>My cool web-server</h1></html>" > index.html
aws s3 mb s3://mysuperbacketname2021
aws s3 cp index.html s3://mysuperbacketname2021
```

### Правила приёма работы

Домашняя работа оформляется в своём Git репозитории в файле README.md. Выполненное домашнее задание пришлите ссылкой на .md-файл в вашем репозитории.
Файл README.md должен содержать скриншоты вывода необходимых команд, а также скриншоты результатов.
Репозиторий должен содержать тексты манифестов или ссылки на них в файле README.md.

