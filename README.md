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
- Путь к настройкам стэка: https://github.com/Avolon/logs_diplom/tree/main/loghouse<br>
- Вначале добавим repo для Loghouse <br>
<code> # helm repo add loghouse https://flant.github.io/loghouse/charts/  </code><br>
- Устанавливаем стэк <b> Fluentd/Clickhouse/Loghouse </b> в namespace "loghouse". <br>
<code> # helm install --namespace loghouse --create-namespace -f monitor/loghouse/loghouse-values.yml loghouse loghouse/loghouse </code> <br>
- Проверяем в K8S кластере: <br>
<code> root@diplom:/home/odmin# kubectl get pods -n loghouse <br>
NAME                        READY   STATUS    RESTARTS   AGE
clickhouse-server-0         2/2     Running   0          8m17s
fluentd-lfbxz               1/1     Running   0          44m
fluentd-xdhzd               1/1     Running   0          53m
loghouse-57c56f948c-dhnfz   2/2     Running   0          30m
- Создаём Dashbourd для сборки логов нашего приложения и кластера K8S <br>
<H3>Задание 2. Выбор метрик для мониторинга. </H3>   
- Для мониторнига сервера SRV, доступности нашего приложения "APP-DEP" и кластера "K8S" буду использовать стэк <b>Grafana\Prometheus\Blackbox\Node Exporter\Alertmanager </b><br>
- Весь стэк буду разворачивать на SRV сервере в Docker-compose. <br>
- Путь к манифестам: https://github.com/Avolon/logs_diplom/tree/main/Prometheus_stack <br>
- Для уставноки нужно перейти в каталог <b>/monitor/Prometheus_stack </b> и развернуть стэк командой:  <br>
 <code># docker-compose up -d </code> <br>
- В результате действия команды в докере, должен быть развёрнут полный стэк <b>Grafana\Prometheus\Blackbox\Node Exporter\Alertmanager </b>:<br>   
- Так же были созданы K8S манифесты для деплоя Prometheus и сбора метрик кластера <br>
- https://github.com/Avolon/logs_diplom/tree/main/Prometheus_stack/k8s <br>
- Был создан namespace "monitoring" и в него задеплоин манифест: <br>
<code># kubectl create namespace monitoring <br>
   #Kubectl apply -f .  </code> <br>
- Результат:  <br>
<code> root@diplom:/home/odmin# kubectl get pods -n monitoring                 <br>
NAME                                     READY   STATUS    RESTARTS   AGE      <br>
   prometheus-deployment-599bbd9457-xmdmz   1/1     Running   0          113m    </code>  <br>
- Заходим в Grafana и подключаемся к Prometheus на сервере SRV и в кластере K8S:<br>
<b>- В итоге в кластре K8S будут работать ПОДЫ :</b> <br> 
<H3>Задание 3. Настройка дашборда. </H3>
- Для сборки метрик буду использовать "Grafana" <br>
- Буду использовать 3 дашборда: <br>
- Сбор метрик состояния сервера SRV при помощи "Node Exporter":<br>
- Сбор метрик о доступности нашего сайта (app-dep) при помощи "BlackBox":  <br>
- Сбор метрик о состоянии K8S кластера и на podа - APP-DEP: <br>
<H3>Задание 4. Алертинг. </H3>
- Для отправки алертов буду использовать <b> Telegram </b>. <br>
- Был установлен и настроен Docker контенер для отправки сообщений в Telegram. <br> 
- Все чувствительные данные были вынесены в отдельный файл "/data/tele.var" и добавлен в .gitignor. <br>
- Добавляем Telegram Bot в стэк Prometheus\Grafana:https://github.com/Avolon/logs_diplom/tree/main/Prometheus_stack/docker-compose.yml <br> 
- Настройка отправки сообщений: https://github.com/Avolon/logs_diplom/tree/main/Prometheus_stack/alertmanager/alertmanager.yml <br>
