# ALT
# Топология
# ALT
![image](https://github.com/Ganibal-24/ALT/assets/148868527/b2809564-b485-49ed-a071-6adae0812062)
# Модуль 1: Выполнение работ по проектированию сетевой инфраструктуры
# 1. Выполните базовую настройку всех устройств
a. Присвоить имена в соответствии с топологией
b. Рассчитайте IP-адресацию Ipv4. Необходимо заполинть таблицу №1, чтобы эксперты могли проверить ваше рабочее место
c. Пул адресов для сети офиса BRANCH - не более 16
d. Пул адресов для сети офиса HQ - не более 64
# Выполнение:
# a. Присвоить имена в соответствии с топологией
```
hostnamectl set-hostname <NAME>; exec bash
```
# b. Рассчитайте IP-адресацию IPv4 и IPv6. Необходимо заполнить таблицу №1, чтобы эксперты могли проверить ваше рабочее место.
| Имя устройства | Интерфейс | IPv4/IPv6 | Маска/префикс | Шлюз |
| ----------- | ----------- | ----------- | ----------- | ----------- |
| CLI         | ens192      | 192.168.0.170    | **30/** 255.255.255.252    |  192.168.0.169  |
|             | ens224      | 192.168.0.174    | **30/** 255.255.255.252    |  192.168.0.173  |
| ISP         | ens192      | 10.12.18.13      | **24/** 255.255.255.0      |  10.12.13.254   |
|             | ens224      | 192.168.0.162    | **30/** 255.255.255.252    |                 |
|             | ens256      | 192.168.0.166    | **30/** 255.255.255.252    |                 |
|             | ens161      | 192.168.0.169    | **30/** 255.255.255.252    |                 |
| HQ-R        | ens192      | 192.168.0.1      | **25/** 255.255.255.128    |                 |
|             | ens224      | 192.168.0.161    | **30/** 255.255.255.252    |  192.168.0.162  |
|             | ens256      | 192.168.0.173    | **30/** 255.255.255.252    |                 |
|             | GRE1        | 10.0.0.2         | **30/** 255.255.255.252    |                 |
| BR-R        | ens192      | 192.168.0.129    | **27/** 255.255.255.224    |                 |
|             | ens224      | 192.168.0.165    | **30/** 255.255.255.252    |  192.168.0.166  |
|             | GRE1        | 10.0.0.1         | **30/** 255.255.255.252    |                 |
| HQ-SRV      | ens192      | 192.168.0.126    | **25/** 255.255.255.128    |  192.168.0.1    |
| BR-SRV      | ens192      | 192.168.0.158    | **27/** 255.255.255.224    |  192.168.0.129  |
| Имя устройства | Пользователь | Пароль |
| ----------- | ----------- | ----------- |
| CLI         | root      | toortoor    |
| ISP         | root      | toortoor    |
| HQ-R        | root      | toortoor    |
| BR-R        | root      | toor        |
| HQ-SRV      | root      | toor        |
| BR-SRV      | root      | toor        |
# Базовая настройка устройств, IP адресация и маршрутизация.
> [!TIP]
> **После создания машин добавляем сетевые интерфейсы**
> [!NOTE]
![image](https://github.com/KiRL132/ALT-DEMO/assets/148869565/0bfe9da2-dec4-450d-b8ec-8f3ccb07092c)
> [!TIP]
> - [x] **Задаем имя устройствам**
>
> hostnamectl set-hostname ISP;exec bash
>[!TIP] 
> - [x] **Устанавливаем все необходимые пакеты сразу**
>
> apt-get update
> 
> apt-get install nano
>
> apt-get install -y NetworkManager-{daemon,tui} [на HQ-BR]
>
> apt-get install -y dhcp-server [на HQ-R]
>
> apt-get install -y firewalld [на ISP, BR-R, HQ-R]
>
> apt-get install -y iperf3 [на ISP, HQ-R]
## 1. Маршрутизация
>[!NOTE] 
> - [x] **Включаем IPv4**
>
> vim /etc/net/ifaces/default/options --> **CONFIG_IPV4=yes**
>
> - [x] **Узнаем номера интерфейсов и создаем файлы конфигурации устройств**
> ```
> ip a
> ```
> ![image](https://github.com/KiRL132/ALT-DEMO/assets/148869565/42ce4fd9-4b44-44fd-8878-9711cdc2fbf7)
>
> ```
> mkdir /etc/net/ifaces/ens192
>
> mkdir /etc/net/ifaces/ens224
>
> mkdir /etc/net/ifaces/ens256
>
> mkdir /etc/net/ifaces/ens160
>
> ```
> 
> - [x] **Настраиваем конфигурацию интерфейсов**
> ```
> nano /etc/net/ifaces/ens192/options
> ```
> ![image](https://github.com/KiRL132/ALT-DEMO/assets/148869565/2f69cbde-290e-4e03-acd1-d69e118920ce)
>
> - [x] *Копируем настройки на все необходимые интерфейсы*
> ```
> cp /etc/net/ifaces/ens192/options /etc/net/ifaces/ens**НОМЕР ИНТЕРФЕЙСА**/
> ```
> - [x] **Вписываем IP адреса и при необходимости шлюз по умолчанию**
> ```
> nano /etc/net/ifaces/ens192/ipv4address
> ```
> > Вписываем только адрес и через слеш его маску (Пример: 192.168.0.1/27)
> ```
> nano /etc/net/ifaces/ens192/ipv4route
> ```
> > Прописываем [default via адрес] (Пример: default via 192.168.0.129)
>
> - [x] **Включаем маршрутизацию**
> ```
> nano /etc/net/sysctl.conf --> net.ivp4.ip_forward = 1
> ```

Чтобы поменять адрес интерфейса
```
nano /etc/net/ifaces/ens***/ipv4address
```
Чтобы поменять шлюз по умолчанию интерфейса
```
nano /etc/net/ifaces/ens***/ipv4route
```
Чтобы поменять настройки конфигурации
```
nano /etc/net/ifaces/ens***/options
```
# Поднимаем GRE-туннель средствами nmtui (бонусное задание)
# Выполнение:
Установление пакета
```
apt-get install -y NetworkMnager-{daemon,vui}
```
Включение в автозагрузку
```
systemctl enable --now NetworkManager
```
Вход в интерфейс
```
nmtui
```
![image](https://github.com/Ganibal-24/demo2024/assets/148868527/aa3f89b4-258d-416d-b640-841dc113e882)
Добавить IP tunnel
![image](https://github.com/Ganibal-24/demo2024/assets/148868527/7119312a-f41a-46c5-bf1e-7d04fe7f29ac)
![image](https://github.com/Ganibal-24/demo2024/assets/148868527/102808ba-b1f2-4e68-a05c-7b8afa776640)
Для HQ-R прописываем 10.0.0.1/30, для BR-R 10.0.0.2/30
Для HQ-R:
```
nmcli connection modify HQ-R ip-tunnel.ttl 64
ip r add 192.168.0.128/27 dev gre1
```
Для BR-R:
```
nmcli connection modify BR-R ip-tunnel.ttl 64
ip r add 192.168.0.0/25 dev gre1
```
# Настройте NAT на всех маршрутизаторах (бонусное задание)
# Выполнение:
Устанавливаем пакет:
```
apt-get -y install firewalld
```
Задаём автозагрузку:
```
systemctl enable --now firewalld
```
Прописываем интерфейс, который смотрит во внешнюю сеть:
```
firewall-cmd --permanent --zone=public --add-interface=ens192
```
Прописываем интерфейс, который смотрит во внутреннюю сеть:
```
firewall-cmd --permanent --zone=trusted --add-interface=ens224
firewall-cmd --permanent --zone=trusted --add-interface=ens256
```
Включаем NAT:
```
firewall-cmd --permanent --zone=public --add-masquerade
```
Сохраняем правила:
```
firewall-cmd --reload
```
# 2. Настройте внутреннюю динамическую маршрутизацию по средствам FRR
# Выполнение:
Установил пакет FRR:
```
apt-get install -y frr
```
Проверил состояние:
```
systemctl status frr
```
Вошёл в файл:
```
nano /etc/frr/daemons
```
Изменил значение:
```
ospfd=yes
```
Включаю и добавляю в автозагрузку FRR:
```
systemctl enable --now frr
```
Вошёл в настройку маршрутизации:
```
vtysh
```
Вхожу в конфигурацию терминала, затем запускаю процесс и добавляю сети:
```
conf t
router ospf
passive-interface default
 network 192.168.0.0/25 area 0
 network 10.0.0.0/30 area 0
```
Просматриваю соседей:
```
show ip ospf neighbor
```
Далее нужно прописать sysctl -w net.ipv4.ip_forward=1. Затем раскомментировать сроку net.ipv4.ip_forward в файле /etc/sysctl.conf. Сохранить настройку в vtysh. Проверить настройку можно с помощью команды sysctl -a | grep forward.
# 3. Настройте автоматическое распределение IP-адресов на роутере HQ-R
# Выполнение:
Установка DHCP
```
apt-get install -y dhcp-server
```
Вошёл в файл
```
nano /etc/sysconfig/dhcpd 
```
Указал интерфейс который смотрит на HQ-SRV
```
DHCPDARGS=ens224
Ctrl + s - сохранил изменения
Ctrl + x - вышел с файла
```
Далее следует настройка раздачи адресов, а для этого захожу в файл:
```
nano /etc/dhcp/dhcpd.conf
```
Прописываю конфиг
```
# dhcp.conf
default-lease-time 6000;
max-lease-time 72000;
subnet 192.168.0.0 netmask 255.255.255.128 {
range 192.168.0.10 192.168.0.25;
option routers 192.168.0.1;
}
Ctrl + s - сохранил изменения
Ctrl + x - вышел с файла
```
Повторяю конфиг в файле /etc/dhcp/dhcpd.conf.example
Запускаю и добавляю в автозагрузку слжубу 
```
systemctl enable --now dhcpd
```
Проверить DHCP можно при помощи HQ-SRV, включив на нём DHCP.
# 4. Настройте локальные учётные записи на всех устройствах
| Учётная запись | Пароль      | Примичание     |     
| -----------    | ----------- |------------    | 
| Admin          | toor        | CLI, HQ-SRV, HQ-R| 
| Branch admin   | toor        | BR-SRV, BR-R   |
| Network admin  | toor        | HQ-R, BR-R, BR-SRV|
# Выполнение:
Добавляю пользователя:
```
useradd admin -m -c "Admin" -U 
```
Поменять пароль:
```
passwd admin
```
Для CLI: пуск - центр управления - центр управления системой - локальные учётные записи. Комментарий - Admin; назначенные - users; группы в которые входит - Admin - применить.
>[!NOTE]
> - [x] **Пользователь на CLI**
> 
> ![image](https://github.com/KiRL132/ALT-DEMO/assets/148869565/25bc80d0-432a-43ee-b387-37ec619b2b3a)
> - [x] **Вводим пароль "toor" и нажимаем ОК**
>       
> ![image](https://github.com/KiRL132/ALT-DEMO/assets/148869565/d0e27331-2955-4f3e-8a1e-f70fae15bafb)
> - [x] **Нажимаем "Локальные учётные записи"**
> ![image](https://github.com/KiRL132/ALT-DEMO/assets/148869565/47c0c0df-bbf0-49dd-af4d-673734d1ca9f)
> - [x] **В качестве "Новая учётная запись" пишем "admin" -> нажимаем "Создать"**
> ![image](https://github.com/KiRL132/ALT-DEMO/assets/148869565/2b865191-270d-4428-82a3-a6c1dd8dfd70)
> - [x] **В комментарии пишем "Admin". Задаём пароль "P@ssword"** 
> ![image](https://github.com/KiRL132/ALT-DEMO/assets/148869565/39e4763f-920d-4a10-9199-c21c9f2e3d1b)
> - [x] **Нажимаем "Ctrl + Alt + T" для открытия терминала и вводим команду:**
> **grep admin /etc/passwd**
> ![image](https://github.com/KiRL132/ALT-DEMO/assets/148869565/5ceba8bf-99c8-454a-817c-1b3c8057c5af)
>[!NOTE]
> - [x] **Пользователи на BR-R-SRV и HQ-R-SRV**
> ```
> useradd [Имя-слитно] -m -c "[комментарий имя]" -U
> passwd [Имя-слитно]
> Вводим пароль: P@ssw0rd
> ```
>  ![image](https://github.com/KiRL132/ALT-DEMO/assets/148869565/32984ac0-9ed8-430e-8864-fb2b5606886f)
> - [x] **Проверяем пользователей: nano [имя-слитно] /etc/passwd**
## 6. Проверка пропускной способности iperf3
> [!TIP]
> - [x] **Измерьте пропускную способность сети между HQ-R-ISP**
> ```
> systemctl enable --now iperf3
> ```
> - [x] **На HQ-R**
> ```
> iperf3 -c 192.168.0.162
> ```
> ![image](https://github.com/KiRL132/ALT-DEMO/assets/148869565/5aa8fc27-86ea-4ab6-8b43-4cb2b64f0571)
> [!NOTE]
> - [x] **Доп. инфа для iperf3**
> 
> при помощи параметра "--get-server-output" - можно получить более детальный результат:
>  
> если необходимо получить результаты сервера в выводе клиента
> 
> при помощи параметра "--logfile /path/to/file" - можно указать путь к файлу в который записать вывод данной каманды в качестве отчёта
> ```
> iperf3 -c 11.11.11.1 --get-server-output --logfile ~/iperf3_logfile.txt
> ```
> ![image](https://github.com/KiRL132/ALT-DEMO/assets/148869565/41af8df0-9b66-4218-8981-76cc2974d69c)
> 
> - [x] Содержимое получившегося файла отчёта:
> ![image](https://github.com/KiRL132/ALT-DEMO/assets/148869565/2e28f6a4-c96e-45dc-b844-442579fc13a5)
# 5. Измерьте пропускную способность сети между двумя узлами
# Выполнение:
Устанавливаю пакет:
```
apt-get install -y iperf3
```
Выполняю запуск службы:
```
systemctl enable --now iperf3
```
Запускаю iperf3 в качестве клиента:
```
iperf3 -c 192.168.0.162
```
# 6. Составьте backup скрипты для сохранения конфигурации сетевых устройств
# Выполнение:
Создаю простой скрипт:
```
nano backup-script.sh
```
Содержимое файла:
```
#!/bin/bash
echo "Start backup!"
backup_dir="/etc"
dest_dir="/opt/backup"
mkdir -p $dest_dir
tar -p $dest_dir/$(hostname -s)-$(date +"%d.%m.%y").tgz $backup_dir
echo "Done!"
```
Назначаю права на исполнение для данного файла:
```
chmod +x backup-script.sh
```
Выполняю запуск скрипта:
```
./backup-script.sh
```
Посмотреть содержание архива:
```
tar -tf /opt/backup/hq-r-00.00.00.tgz | less
```
# 7. Настройте подключение по SSH для удалённого конфигурирования устройства
# Выполнение: 
На HQ-SRV меняем порт с 22 на 2222:
```
sed -i "s/#Port 22/Port 2222/g" /etc/openssh/sshd_config
```
Перезагружаем службу sshd:
```
systemctl restart sshd
```
Проверка:
```
ss -tlpn | grep sshd
```
На HQ-R устанавливаем nftables:
```
apt-get install -y nftables
```
Включаем и добавляем в автозагрузку службу nftables:
```
systemctl enable --now nftables
```
Создаём правило:
```
nft add table inet nat
```
Добавляем цепочку в таблицу:
```
nft add chain inet nat prerouting '{ type nat hook prerouting priority 0; }'
```
Добавляем ещё одно правило:
```
nft add rule inet nat prerouting ip daddr 192.168.0.161 tcp dport 22 dnat to 192.168.0.10:2222
```
Сохраняем правила:
```
nft list ruleset | tail -n 7 | tee -a /etc/nftables/nftables.nft
```
Перезапускаем службу:
```
systemctl restart nftables
```
Проверка на BR-R:
```
ssh admin@192.168.0.161
password
hostname
```
# 8. Настройте контроль доступа до HQ-SRV по SSH
# Выполнение:
Устанавливаю nftables:
```
apt-get update && apt-get install -y nftables
```
Включаю и добавляю в автозагрузку:
```
systemctl enable --now nftables
```
Добавляю правило:
```
nft add rule inet filter input ip saddr 192.168.0.170 tcp dport 2222 counter drop
nft add rule inet filter input ip saddr 192.168.0.0/30 tcp dport 2222 counter drop
```
Проверка:
```
nft list ruleset
```
В файле /etc/nftables/nftables.nft удаляю незакомментированные строчки
Отправляю результат:
```
nft list ruleset | tee -a /etc/nftables/nftables.nft
```
Перезапускаю:
```
systemctl restart nftables
```
Проверка заключается в том, что подключиться не получится и будет выдано сообщение: "connect to host 192.168.0.170 port 2222: Connection timed out


