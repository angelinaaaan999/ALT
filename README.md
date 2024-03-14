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
Таблица №1
| Имя устройства | Интерфейс   | IPv4             | Маска/Префикс       | Шлюз         |
| -----------    | ----------- |------------      |   ---------------   |------        |
| ISP            | ens192      | 10.12.10.6       | 255.255.255.0/24    | 10.12.10.254 |
|                | ens224      | 192.168.0.166    | 255.255.255.252/30  |              |
|                | ens256      | 192.168.0.162    | 255.255.255.252/30  |              |
|                | ens161      | 192.168.0.169    | 255.255.255.252/30  |              |
| HQ-R           | ens192      | 192.168.0.161    | 255.255.255.252/30  | 192.168.0.162|
|                | ens224      | 192.168.0.1      | 255.255.255.128/25  |              |
|                | ens256      | 192.168.0.173    | 255.255.255.252/30  |              |
|                | gre1        | 10.0.0.1         | 255.255.255.252/30  |              |
| HQ-SRV         | ens192      | 192.168.0.126    | 255.255.255.128/25  | 192.168.0.1  |
| BR-R           | ens192      | 192.168.0.165    | 255.255.255.252/30  | 192.168.0.166|
|                | ens224      | 192.168.0.129    | 255.255.255.224/30  |              |
|                | gre1        | 10.0.0.2         | 255.255.255.252/30  |              |
| BR-SRV         | ens192      | 192.168.0.158    | 255.255.255.224/27  | 192.168.0.129|
| CLI            | ens192      | 192.168.0.170    | 255.255.255.252/30  | 192.168.0.169|
|                | ens224      | 192.168.0.174    | 255.255.255.252/30  | 192.168.0.173|
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


