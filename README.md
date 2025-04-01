# Мои крипты для Linux

## Рандомизация MAC-адреса

1. Создать файл 99-macchanger.conf:

```bash

[device]
wifi.scan-rand-mac-address=yes

[connection]
wifi.cloned-mac-address=random
ethernet.cloned-mac-address=random
connection.stable-id=/

```
2. Скопировать файл в директорию /etc/NetworkManager/conf.d/ и перезагрузить NetworkManager

```bash

#!/bin/bash

cp 99-macchanger.conf /etc/NetworkManager/conf.d/99-macchanger.conf

systemctl restart NetworkManager

```

## Рандомизация имени ПК при перезагрузке

1. Создать файл hotnames.txt, содержащий по одному имени в каждой строке:

2. Создать файл randomize_hostname.sh:

```bash

#!/bin/bash

HOSTNAMES_FILE="/usr/local/bin/randomize_hostname/hostnames.txt"

num_lines=$(wc -l < "$HOSTNAMES_FILE")

random_line=$((RANDOM % num_lines + 1))

new_hostname=$(sed -n "${random_line}p" "$HOSTNAMES_FILE")

echo "Setting new hostname: $new_hostname"

hostnamectl set-hostname $new_hostname

```

3. Создать файл randomize_hostname.service:

```bash

[Unit]
Description=Randomize Hostname
After=network.target

[Service]
Type=oneshot
ExecStart=/usr/local/bin/randomize_hostname/randomize_hostname.sh

[Install]
WantedBy=multi-user.target
```

3. Скопировать файлы в необходимые директории и включить скрипт:

```bash
#!/bin/bash

sudo mkdir /usr/local/bin/randomize_hostname

sudo cp randomize_hostname.sh /usr/local/bin/randomize_hostname/randomize_hostname.sh
sudo chmod +x /usr/local/bin/randomize_hostname/randomize_hostname.sh

sudo cp hostnames.txt /usr/local/bin/randomize_hostname/hostnames.txt

sudo cp randomize_hostname.service /etc/systemd/system/randomize_hostname.service

sudo systemctl daemon-reload
sudo systemctl enable randomize_hostname.service
```
