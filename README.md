# Домашнее задание к занятию 1 «Disaster recovery и Keepalived»

## Шкутов Иван Владимирович


### Цель задания
В результате выполнения этого задания вы научитесь:
1. Настраивать отслеживание интерфейса для протокола HSRP;
2. Настраивать сервис Keepalived для использования плавающего IP

------

### Чеклист готовности к домашнему заданию

1. Установлена программа Cisco Packet Tracer
2. Установлена операционная система Ubuntu на виртуальную машину и имеется доступ к терминалу
3. Сделан клон этой виртуальной машины, они находятся в одной подсети и имеют разные IP адреса
4. Просмотрены конфигурационные файлы, рассматриваемые на лекции, которые находятся по [ссылке](1/)


------

### Инструкция по выполнению домашнего задания

1. Сделайте fork [репозитория c шаблоном решения](https://github.com/netology-code/sys-pattern-homework) к себе в Github и переименуйте его по названию или номеру занятия, например, https://github.com/имя-вашего-репозитория/gitlab-hw или https://github.com/имя-вашего-репозитория/8-03-hw).
2. Выполните клонирование этого репозитория к себе на ПК с помощью команды git clone.
3. Выполните домашнее задание и заполните у себя локально этот файл README.md:
   - впишите вверху название занятия и ваши фамилию и имя;
   - в каждом задании добавьте решение в требуемом виде: текст/код/скриншоты/ссылка;
   - для корректного добавления скриншотов воспользуйтесь инструкцией [«Как вставить скриншот в шаблон с решением»](https://github.com/netology-code/sys-pattern-homework/blob/main/screen-instruction.md);
   - при оформлении используйте возможности языка разметки md. Коротко об этом можно посмотреть в [инструкции по MarkDown](https://github.com/netology-code/sys-pattern-homework/blob/main/md-instruction.md).
4. После завершения работы над домашним заданием сделайте коммит (git commit -m "comment") и отправьте его на Github (git push origin).
5. Для проверки домашнего задания преподавателем в личном кабинете прикрепите и отправьте ссылку на решение в виде md-файла в вашем Github.
6. Любые вопросы задавайте в чате учебной группы и/или в разделе «Вопросы по заданию» в личном кабинете.



------


### Задание 1
- Дана [схема](1/hsrp_advanced.pkt) для Cisco Packet Tracer, рассматриваемая в лекции.
- На данной схеме уже настроено отслеживание интерфейсов маршрутизаторов Gi0/1 (для нулевой группы)
- Необходимо аналогично настроить отслеживание состояния интерфейсов Gi0/0 (для первой группы).
- Для проверки корректности настройки, разорвите один из кабелей между одним из маршрутизаторов и Switch0 и запустите ping между PC0 и Server0.
- На проверку отправьте получившуюся схему в формате pkt и скриншот, где виден процесс настройки маршрутизатора.

------

![PC01](https://github.com/Ivan-Shkutov/sflt-homeworks-9-01/blob/main/img/1.1.png)

![show_standby_brief](https://github.com/Ivan-Shkutov/sflt-homeworks-9-01/blob/main/img/1.2.png)

![standby_brief](https://github.com/Ivan-Shkutov/sflt-homeworks-9-01/blob/main/img/1.3.png)

![HSRP_configuration](https://github.com/Ivan-Shkutov/sflt-homeworks-9-01/blob/main/img/1.4.png)



#### Router0 configuration	

      interface GigabitEthernet0/0
   
        ip address 192.168.1.2 255.255.255.0
       
        duplex auto
       
        speed auto
       
        standby version 2
       
        standby 1 ip 192.168.1.1
       
        standby 1 priority 120
       
        standby 1 preempt
   
      !
   
      interface GigabitEthernet0/1
       
        ip address 192.168.2.2 255.255.255.0
       
        duplex auto
       
        speed auto
       
        standby version 2
       
        standby 2 ip 192.168.2.1
       
        standby 2 priority 120
       
        standby 2 preempt	
 
 
 
 #### Router1 configuration
 
      interface GigabitEthernet0/0
       
        ip address 192.168.1.3 255.255.255.0
        
        duplex auto
        
        speed auto
        
        standby version 2
        
        standby 1 ip 192.168.1.1
      
      !
      
      interface GigabitEthernet0/1
      
        ip address 192.168.2.3 255.255.255.0
        
        duplex auto
        
        speed auto
        
        standby version 2
        
        standby 2 ip 192.168.2.1

        
------
### Задание 2
- Запустите две виртуальные машины Linux, установите и настройте сервис Keepalived как в лекции, используя пример конфигурационного [файла](1/keepalived-simple.conf).
- Настройте любой веб-сервер (например, nginx или simple python server) на двух виртуальных машинах
- Напишите Bash-скрипт, который будет проверять доступность порта данного веб-сервера и существование файла index.html в root-директории данного веб-сервера.
- Настройте Keepalived так, чтобы он запускал данный скрипт каждые 3 секунды и переносил виртуальный IP на другой сервер, если bash-скрипт завершался с кодом, отличным от нуля (то есть порт веб-сервера был недоступен или отсутствовал index.html). Используйте для этого секцию vrrp_script
- На проверку отправьте получившейся bash-скрипт и конфигурационный файл keepalived, а также скриншот с демонстрацией переезда плавающего ip на другой сервер в случае недоступности порта или файла index.html

------
#### Установка Nginx на Ubuntu:

      sudo apt update
   
      sudo apt install nginx -y

#### Создание файла index.html на двух виртуальных машинах:

      echo "Welcome to the web server!" | sudo tee /var/www/html/index.html

#### Запуск сервера Nginx на Ubuntu:

      sudo systemctl start nginx
      
      sudo systemctl status nginx

#### Установка и настройка Keepalived:

      sudo apt install keepalived -y
      
#### Конфигурация файла keepalived.conf:

      rrp_instance check_webserver {
      
       state BACKUP
      
       interface enp0s8
       
       virtual_router_id 15
       
       priority 255
       
       advert_int 1

       virtual_ipaddress {
       
           192.168.1.15
       
       }
       
       track_script {
       
           check_webserver
          }
      }

      vrrp_script check_web {
       
       script "/usr/local/bin/check_webserver.sh"
       
       interval 3
       
       fall 1
       
       rise 2
      }

#### Создание Bash-скрипта:

      if ! nc -z 127.0.0.1 80; then
   
       echo "Port 80 is down"
       
       exit 1
       
      fi
   
      if [ ! -f /var/www/html/index.html ]; then
   
       echo "File index.html is missing"
    
       exit 1
       
      fi

      echo "Web server is running"
   
      exit 0

#### Сделать скрипт исполняемым:

   sudo chmod +x /usr/local/bin/check_weserver.sh

#### Удаляем файл index.html на vm1 и проверяем состояние web запросов на обеих виртуальных машинах:

   vm1: нет связи с 192.168.1.5 и виртуальным ip 192.168.1.15

   vm2: связь присутствует на всех ip адресах 192.168.1.5,  192.168.1.7,  192.168.1.15.

![1](https://github.com/Ivan-Shkutov/smon-homeworks-8-03/blob/main/img/1.1.png)

![2](https://github.com/Ivan-Shkutov/smon-homeworks-8-03/blob/main/img/1.1.png)

![3](https://github.com/Ivan-Shkutov/smon-homeworks-8-03/blob/main/img/1.1.png)

![4](https://github.com/Ivan-Shkutov/smon-homeworks-8-03/blob/main/img/1.1.png)

![5](https://github.com/Ivan-Shkutov/smon-homeworks-8-03/blob/main/img/1.1.png)

![6](https://github.com/Ivan-Shkutov/smon-homeworks-8-03/blob/main/img/1.1.png)

![7](https://github.com/Ivan-Shkutov/smon-homeworks-8-03/blob/main/img/1.1.png)


------

