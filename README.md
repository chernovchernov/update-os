Update-os
=========

Данная роль написана для для обновления операционной системы.
В качестве дополнительного требования было реализовано:
- Задание по перезагрузке сервера, после обновление пакетов;
- Исключение обновления выбранного пакета: vars/ переменная excluded_packages;
- После обновления операционной системы, оставляет только 2 последних версий ядра;
- Обновление происходит для операционной системы CentOS7 и Ubuntu 20.04

Требования
-------------
Для использования роли Ansible вам необходимо использовать:
- [Server Ubuntu 20.04]
- [Server CentOS 7]
- [Ansible]

Запуск Playbook
---------------

Используем отдельный мастер сервер, где установлен Ansible

Создаем ssh ключ
```
ssh-keygen
```
Устанавливаем ssh соединение с сервером Ubuntu 20.04: 
Выведем на экран ключ чтобы скопировать его
```
cat ~/root/.ssh/id_rsa.pub
```
Копируем результат:
> ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQDRCPhIiUAiHsv2Unm34nUNx9xotzAx7rIXLTI08Lxwh3vRdeR+nrjCfA9n61UarJF9oKdUFSvGv4FzqLKk5eWBwH9Azd7oOakRrNMxQgiHlwebeefYtOP0uD55DEJgXGCMBs1/k/S8KQPhcsp5q30lwRcnHYyfDEsbhgWDpxqVIrPaGm9OSAxVxfpgEpHzeA4+93PAe54yvNjGquA7u3rE2vos3HEdaHddDUbbHH41HlQMA8VEnNEa+bJHFGcPqYb197afCShH/XYJ/GoRWhv3n9x0R1IPrF9oDFS5Urr7IYbj8ITioacpYTYUIGSD6l1k7mjRDkHCGt2IDmASsmU1 root@localhost.localdomain

Заходим на сервер ubuntu 20.04 (замените user и ip_addr_server_ubuntu20.04 на ваши переменные):
```
ssh user@ip_addr_server_ubuntu20.04
```
Копируем полученный ключ на сервер Ubuntu 20.04 в файл `/root/.ssh/authorized_keys`
```
nano /root/.ssh/authorized_keys
```

Перезагружаем ssh командой 
``` 
service ssh restart 
``` 
Возвращаемся на наш мастер сервер. Устанавливаем ssh соединение с сервером Centos 7:
```
ssh-copy-id root@ip_addr_server_centos7
```
Нажимаем `yes`, вводим пароль от сервера

Соединение настроено, проверим соединение Ansible c серверами: перейдем в директорию с нашим проектом: 
``` 
cd update-os 
``` 
Откройте файл `inventory.txt`, впишите туда ip адреса своих серверов 
``` 
nano inventory.txt 
``` 
Напротив ip адреса сервера ubuntu вставьте строчку `ansible_python_interpreter=/usr/bin/python3.8`

Откройте файл `roles/update-os/tasks/main.yml`, впишите название Ubuntu сервера в последней строке, в нашем случае это vm6 
``` 
when: ansible_fqdn == "vm6"
``` 

Запускаем проверку ansible ping командой 
``` 
ansible -i inventory.txt all -m ping 
``` 
В ответ должно прийти сообщение от ansible: 
``` 
192.168.247.145 | SUCCESS => { - python version = 3.8.10 "changed": false,
- jinja version = 2.10.1    "ping": "pong"
}
192.168.247.135 | SUCCESS => {
    "changed": false,
    "ping": "pong"
}
```
Это значит соединение Ansible с серверами настроено и можем запускать наш Playbook.yml:
```
ansible-playbook -i inventory.txt playbook.yml
```

[Ansible]: <https://docs.ansible.com>
[Server Ubuntu 20.04]: <https://releases.ubuntu.com/focal/>
[Server CentOS 7]: <https://www.centos.org/download/>


-------------------

Автор: Борис Чернов <chernov001@gmail.com>
https://t.me/chernovchernov

