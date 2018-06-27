[![Build Status](https://travis-ci.org/Otus-DevOps-2018-05/statusxt_infra.svg?branch=master)](https://travis-ci.org/Otus-DevOps-2018-05/statusxt_infra)

# statusxt_infra
statusxt Infra repository

# Table of content
- [Homework-03](#homework-03)
- [Homework-04](#homework-04)
- [Homework-05](#homework-05)

# Homework 03

## 3.1 Задание с 35 слайда

- Способ подключения к someinternalhost в одну команду:
ssh -i ~/.ssh/appuser2 -A appuser@35.206.144.111 ssh 10.132.0.3
- Вариант решения для подключения из консоли при помощи команды вида ssh
someinternalhost из локальной консоли рабочего устройства:
```
touch ~/.ssh/config
nano ~/.ssh/config
	Host someinternalhost
		HostName 10.132.0.3
		User appuser
		ProxyCommand ssh -W %h:%p -i ~/.ssh/appuser appuser@35.206.144.111
ssh someinternalhost
```

## 3.2 Файлы

- setupvpn.sh - установка VPN сервера

- cloudbastion.ovpn - конфиг для подключения клиента

## 3.3 Конфигурация

bastion_IP = 35.206.144.111

someinternalhost_IP = 10.132.0.3

# Homework 04

```
gcloud compute instances create reddit-app-2\
 --boot-disk-size=10GB \
 --image-family ubuntu-1604-lts \
 --image-project=ubuntu-os-cloud \
 --machine-type=g1-small \
 --tags puma-server \
 --metadata-from-file startup-script=/root/statusxt_infra/startup.sh \
 --restart-on-failure
```
```
gcloud compute firewall-rules create "default-puma-server"\
 --allow tcp:9292 \
 --source-ranges=0.0.0.0/0 \
 --target-tags=puma-server
```
testapp_IP = 35.187.70.142

testapp_port = 9292

# Homework 05

## 5.1 Что было сделано

- установлен packer
- создан ADC
- создан и проверен packer template ubuntu16.json
- параметризован созданный шаблон
- исследованы другие опции builder для GCP: Описание образа, Размер и тип диска, Название сети, Теги

В рамках задания со *:
- создан шаблон immutable.json для создания образа с "запеченным" приложением
- создан shell-скрипт для запуска VM из этого образа

## 5.2 Как запустить проект
### 5.2.1 Base
Предполагается, что все действия происходят в каталоге `packer`:
- проверить шаблон и запустить build:
```
packer validate ./ubuntu16.json
packer build -var-file=variables.json ubuntu16.json
```

### 5.2.2 *

Предполагается, что все действия происходят в каталоге `packer`:
- проверить шаблон и запустить build:
```
packer validate ./immutable.json
packer build -var-file=variables.json immutable.json
```
- запустить скрипт по созданию VM в GCP из шаблона:
```
../config-scripts/create-reddit-vm.sh
```

## 5.3 Как проверить

- перейти в GCP (https://console.cloud.google.com), Compute engine -> instances, в списке будет присуствовать созданный экземпляр и его внешний адрес
- открыть в браузере http://<внешний_адрес_экземпляра>:9292