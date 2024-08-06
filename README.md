<h1> Спринт 3. </h1>
<H2> Цель: </H2>
Настройка мониторинга и логирования. <br>
<H2> Задача: </H2> 
1) Настройка сборки логов.  <br>
2) Выбор метрик для мониторинга.  <br>
3) Настройка дашборда. <br>
4) Алертинг.<br>
<br>
<H3>Задание 1. Настройка сборки логов. </H3>
- Для сборки логов буду использовать стэк <b> Fluentd/Clickhouse/Loghouse </b>. <br>
- Устанавливать буду с помощью helm. <br>
- Путь к настройкам стэка: https://github.com/Suirus777/skillfactory-diplom/tree/main/monitor/loghouse <br>
- Вначале добавим repo для Loghouse <br>
<code> # helm repo add loghouse https://flant.github.io/loghouse/charts/  </code><br>
- Устанавливаем стэк <b> Fluentd/Clickhouse/Loghouse </b> в namespace "loghouse". <br>
<code> # helm install --namespace loghouse --create-namespace -f monitor/loghouse/loghouse-values.yml loghouse loghouse/loghouse </code> <br>
- Проверяем в K8S кластере: <br>
<code> root@diplom:/home/odmin# kubectl get pods -n loghouse <br>
NAME                                                              READY   STATUS      RESTARTS   AGE  <br>
clickhouse-server-0                                               2/2     Running     0          76m  <br>
fluentd-kltjb                                                     1/1     Running     0          76m  <br>
fluentd-npdxz                                                     1/1     Running     0          76m  <br>
loghouse-f7c55bbf7-9kl2j                                          2/2     Running     0          76m   <br>
loghouse-migrate-tables-e07085a0-f7f6-42ce-a5c4-cad5d5773e467qr   0/1     Completed   0          76m  </code><br>
- Создаём Dashbourd для сборки логов нашего приложения и кластера K8S <br>
- Результат:
<img src="https://github.com/Suirus777/skillfactory-diplom/blob/main/images/Loghouse1.JPG">
<H3>Задание 2. Выбор метрик для мониторинга. </H3>   
- Для мониторнига сервера SRV, доступности нашего приложения "APP-DEP" и кластера "K8S" буду использовать стэк <b>Grafana\Prometheus\Blackbox\Node Exporter\Alertmanager </b><br>
- Весь стэк буду разворачивать на SRV сервере в Docker-compose. <br>
- Путь к манифестам: https://github.com/Suirus777/skillfactory-diplom/tree/main/monitor/Prometheus_stack <br>
- Для уставноки нужно перейти в каталог <b>/monitor/Prometheus_stack </b> и развернуть стэк командой:  <br>
 <code># docker-compose up -d </code> <br>
- В результате действия команды в докере, должен быть развёрнут полный стэк <b>Grafana\Prometheus\Blackbox\Node Exporter\Alertmanager </b>:<br>   
<img src="https://github.com/Suirus777/skillfactory-diplom/blob/main/images/grafana1.JPG">
- Так же были созданы K8S манифесты для деплоя Prometheus и сбора метрик кластера <br>
- Путь к манифестам: https://github.com/Suirus777/skillfactory-diplom/tree/main/monitor/Prometheus_stack/k8s  <br>
- Был создан namespace "monitoring" и в него задеплоин манифест: <br>
<code># kubectl create namespace monitoring <br>
   #Kubectl apply -f .  </code> <br>
- Результат:  <br>
<code> root@diplom:/home/odmin# kubectl get pods -n monitoring                 <br>
NAME                                     READY   STATUS    RESTARTS   AGE      <br>
   prometheus-deployment-599bbd9457-xmdmz   1/1     Running   0          113m    </code>  <br>
- Заходим в Grafana и подключаемся к Prometheus на сервере SRV и в кластере K8S:<br>
<img src="https://github.com/Suirus777/skillfactory-diplom/blob/main/images/pom_grafana.JPG"><br>
<b>- В итоге в кластре K8S будут работать ПОДЫ :</b> <br> 
<img src="https://github.com/Suirus777/skillfactory-diplom/blob/main/images/All_PODS_K8S.JPG">
<H3>Задание 3. Настройка дашборда. </H3>
- Для сборки метрик буду использовать "Grafana" <br>
- Буду использовать 3 дашборда: <br>
 <img src="https://github.com/Suirus777/skillfactory-diplom/blob/main/images/Dashboards.JPG"> <br>
- Сбор метрик состояния сервера SRV при помощи "Node Exporter":<br>
<img src="https://github.com/Suirus777/skillfactory-diplom/blob/main/images/Node_exporter.JPG"><br>
- Сбор метрик о доступности нашего сайта (app-dep) при помощи "BlackBox":  <br>
<img src="https://github.com/Suirus777/skillfactory-diplom/blob/main/images/HTTP_Satus_APP_Grafana.JPG"> <br>
- Сбор метрик о состоянии K8S кластера и на podа - APP-DEP: <br>
<img src="https://github.com/Suirus777/skillfactory-diplom/blob/main/images/K8S_cluster_grafana.JPG"><br>
<H3>Задание 4. Алертинг. </H3>
- Для отправки алертов буду использовать <b> Telegram </b>. <br>
- Создал в Telegram нового бота - <b> SkillFactory_diplom </b> . <br>
- Был установлен и настроен Docker контенер для отправки сообщений в Telegram. <br> 
<img src="https://github.com/Suirus777/skillfactory-diplom/blob/main/images/telegrambot.JPG">
- Все чувствительные данные были вынесены в отдельный файл "/data/tele.var" и добавлен в .gitignor. <br>
- Добавляем Telegram Bot в стэк Prometheus\Grafana: https://github.com/Suirus777/skillfactory-diplom/blob/main/monitor/Prometheus_stack/docker-compose.yml <br> 
- Настройка отправки сообщений: https://github.com/Suirus777/skillfactory-diplom/blob/main/monitor/Prometheus_stack/alertmanager/alertmanager.yml <br>
- Результат работы алертинга, отправка алертов о статусе APP-DEP в telegram: <br>
<img src="https://github.com/Suirus777/skillfactory-diplom/blob/main/images/Telegram_alert.JPG"> 
<h2> Спринт 3. Закончен. </h2>
<h1> Все задания выполнены, диплом закончен! Заранее спасибо!!! </h1>
<a href="http://158.160.4.27"> Адресс работающего приложения!!!!!!!!!!! </a>