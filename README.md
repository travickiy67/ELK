# Травицкий Сергей
# Домашнее задание к занятию «ELK»

### Инструкция по выполнению домашнего задания

1. Сделайте fork [репозитория c шаблоном решения](https://github.com/netology-code/sys-pattern-homework) к себе в Github и переименуйте его по названию или номеру занятия, например, https://github.com/имя-вашего-репозитория/gitlab-hw или https://github.com/имя-вашего-репозитория/8-03-hw).
2. Выполните клонирование этого репозитория к себе на ПК с помощью команды `git clone`.
3. Выполните домашнее задание и заполните у себя локально этот файл README.md:
   - впишите вверху название занятия и ваши фамилию и имя;
   - в каждом задании добавьте решение в требуемом виде: текст/код/скриншоты/ссылка;
   - для корректного добавления скриншотов воспользуйтесь инструкцией [«Как вставить скриншот в шаблон с решением»](https://github.com/netology-code/sys-pattern-homework/blob/main/screen-instruction.md);
   - при оформлении используйте возможности языка разметки md. Коротко об этом можно посмотреть в [инструкции по MarkDown](https://github.com/netology-code/sys-pattern-homework/blob/main/md-instruction.md).
4. После завершения работы над домашним заданием сделайте коммит (`git commit -m "comment"`) и отправьте его на Github (`git push origin`).
5. Для проверки домашнего задания преподавателем в личном кабинете прикрепите и отправьте ссылку на решение в виде md-файла в вашем Github.
6. Любые вопросы задавайте в чате учебной группы и/или в разделе «Вопросы по заданию» в личном кабинете.

Желаем успехов в выполнении домашнего задания.

---

## Дополнительные ресурсы

При выполнении задания используйте дополнительные ресурсы:
- [docker-compose elasticsearch + kibana](11-03/docker-compose.yaml);
- [поднимаем elk в docker](https://www.elastic.co/guide/en/elasticsearch/reference/7.17/docker.html);
- [поднимаем elk в docker с filebeat и docker-логами](https://www.sarulabs.com/post/5/2019-08-12/sending-docker-logs-to-elasticsearch-and-kibana-with-filebeat.html);
- [конфигурируем logstash](https://www.elastic.co/guide/en/logstash/7.17/configuration.html);
- [плагины filter для logstash](https://www.elastic.co/guide/en/logstash/current/filter-plugins.html);
- [конфигурируем filebeat](https://www.elastic.co/guide/en/beats/libbeat/5.3/config-file-format.html);
- [привязываем индексы из elastic в kibana](https://www.elastic.co/guide/en/kibana/7.17/index-patterns.html);
- [как просматривать логи в kibana](https://www.elastic.co/guide/en/kibana/current/discover.html);
- [решение ошибки increase vm.max_map_count elasticsearch](https://stackoverflow.com/questions/42889241/how-to-increase-vm-max-map-count).

**Примечание**: если у вас недоступны официальные образы, можете найти альтернативные варианты в DockerHub, например, [такой](https://hub.docker.com/layers/bitnami/elasticsearch/7.17.13/images/sha256-8084adf6fa1cf24368337d7f62292081db721f4f05dcb01561a7c7e66806cc41?context=explore).

### Задание 1. Elasticsearch 

Установите и запустите Elasticsearch, после чего поменяйте параметр cluster_name на случайный. 

*Приведите скриншот команды 'curl -X GET 'localhost:9200/_cluster/health?pretty', сделанной на сервере с установленным Elasticsearch. Где будет виден нестандартный cluster_name*.

#### Пришлось некоторое время повозится с установкеой. ошибка: запрет установки в нашей стране. Удалось установить по этой схеме.

```
sudo install -d -m 0755 /etc/apt/keyrings
wget -qO - https://artifacts.elastic.co/GPG-KEY-elasticsearch | sudo apt-key add –
echo "deb [trusted=yes] https://mirror.yandex.ru/mirrors/elastic/7/ stable main" | sudo tee /etc/apt/sources.list.d/elastic-7.x.list
sudo apt update
sudo apt install elasticsearch
```
*Скрин*  
![image](https://github.com/travickiy67/ELK/blob/main/img/img1.1.png)  

*Скрин*  

![image](https://github.com/travickiy67/ELK/blob/main/img/img1.2.png)  
---

### Задание 2. Kibana

Установите и запустите Kibana.

*Приведите скриншот интерфейса Kibana на странице http://<ip вашего сервера>:5601/app/dev_tools#/console, где будет выполнен запрос GET /_cluster/health?pretty*.

#### Kibana проще поставилась.

*Скрин 1*  

![image](https://github.com/travickiy67/ELK/blob/main/img/img2.1.png)  

*Скрин 2*  

![image](https://github.com/travickiy67/ELK/blob/main/img/img2.2.png)
---

### Задание 3. Logstash

Установите и запустите Logstash и Nginx. С помощью Logstash отправьте access-лог Nginx в Elasticsearch. 

*Приведите скриншот интерфейса Kibana, на котором видны логи Nginx.*

*Скрин 1*  

![image](https://github.com/travickiy67/ELK/blob/main/img/img3.1.png)  

*Скрин 2*  

![image](https://github.com/travickiy67/ELK/blob/main/img/img3.2.png)  

*Скрин 3*  

![image](https://github.com/travickiy67/ELK/blob/main/img/img3.3.png)  

*Скрин 4*  

![image](https://github.com/travickiy67/ELK/blob/main/img/img3.4.png)  

*Скрин 5*  

![image](https://github.com/travickiy67/ELK/blob/main/img/img3.5.png)  

*Конфигурация*
```
input {
file {
path => "/var/log/nginx/access.log"
start_position => "beginning"
  }
}

filter {
    grok {
        match => { "message" => "%{IPORHOST:remote_ip} - %{DATA:user} \[%{HTTPDATE:access_time}\] \"%{WORD:http_method} %{DATA:url} HTTP/%{NUMBER:http_version}\" %{NUMBER:response_code} %{NUMBER:body_sent_bytes} \"%{DATA:referrer}\" \"%{DATA:agent}\"" }

  }

}
output {
elasticsearch {
hosts => "localhost"
index => "travitskii-logs-%{+YYYY.MM}"
 }
}
```
 
---

### Задание 4. Filebeat. 

Установите и запустите Filebeat. Переключите поставку логов Nginx с Logstash на Filebeat. 

*Приведите скриншот интерфейса Kibana, на котором видны логи Nginx, которые были отправлены через Filebeat.*
*Скрин 1*  

![image](https://github.com/travickiy67/ELK/blob/main/img/img4.1.png)  

*Скрин 2*  

![image](https://github.com/travickiy67/ELK/blob/main/img/img4.2.png)  

*Скрин 3*  

![image](https://github.com/travickiy67/ELK/blob/main/img/img4.3.png)  

*Скрин 4*  

![image](https://github.com/travickiy67/ELK/blob/main/img/img4.4.png)  

*Конфигурация*

```
input {
beats {
port => 5044
}
}

filter {
 if [event][dataset] == "nginx.access" {
   grok {
    match => { "message" => "%{IPORHOST:remote_ip} - %{DATA:user} \[%{HTTPDATE:access_time}\] \"%{WORD:http_method} %{DATA:url} HTTP/%{NUMBER:http_version}\" %{NUMBER:response_code} %{NUMBER:body_seody_sent_bytes} \"%{DATA:referrer}\" \"%{DATA:agent}\"" }


    overwrite => [ "message" ]
  }
   mutate {
    convert => ["response", "integer"]
    convert => ["bytes", "integer"]
    convert => ["responsetime", "float"]
   }
  geoip {
   source => "clientip"
   target => "geoip"
   add_tag => [ "nginx-geoip" ]
  }
  date {
   match => [ "timestamp" , "dd/MMM/YYYY:HH:mm:ss Z" ]
   remove_field => [ "timestamp" ]
  }

 # useragent {
 #  source => "user_agent"
 # }
 }
}

output {
elasticsearch {
hosts => [ "localhost:9200" ]
#        manage_template => false
        index => "nginx-%{+YYYY.MM.dd}"
    }
}

```
## Дополнительные задания (со звёздочкой*)
Эти задания дополнительные, то есть не обязательные к выполнению, и никак не повлияют на получение вами зачёта по этому домашнему заданию. Вы можете их выполнить, если хотите глубже шире разобраться в материале.

### Задание 5*. Доставка данных 

Настройте поставку лога в Elasticsearch через Logstash и Filebeat любого другого сервиса , но не Nginx. 
Для этого лог должен писаться на файловую систему, Logstash должен корректно его распарсить и разложить на поля. 

*Приведите скриншот интерфейса Kibana, на котором будет виден этот лог и напишите лог какого приложения отправляется.*

