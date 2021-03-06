# Домашнее задание к занятию "11.01 Введение в микросервисы"

Вас пригласили консультантом, в крупную компанию, которая решила построить Микросервисную Платформу для внутренней разработки.
Вам как DevOps специалисту необходимо выдвинуть предложение по организации инфраструктуры, для разработки и эксплуатации.

>
> ## Задача 1: Обеспечить разработку
>
> Предложите решение для обеспечения процесса разработки: хранение исходного кода, непрерывная интеграция и непрерывная поставка. 
> Решение может состоять из одного или нескольких программных продуктов и должно описывать способы и принципы их взаимодействия.
>
> Решение должно соответствовать следующим требованиям:
> - Облачная система;
> - Система контроля версий Git;
> - Репозиторий на каждый сервис;
> - Запуск сборки по событию из системы контроля версий;
> - Запуск сборки по кнопке с указанием параметров;
> - Возможность привязать настройки к каждой сборке;
> - Возможность создания шаблонов для различных конфигураций сборок;
> - Возможность безопасного хранения секретных данных: пароли, ключи доступа;
> - Несколько конфигураций для сборки из одного репозитория;
> - Кастомные шаги при сборке;
> - Собственные докер образы для сборки проектов;
> - Возможность развернуть агентов сборки на собственных серверах;
> - Возможность параллельного запуска нескольких сборок;
> - Возможность параллельного запуска тестов;
>
> Обоснуйте свой выбор.



> Хранение исходного кода, непрерывная интеграция и непрерывная поставка. 

1) Обе функции выполняет GitLab (есть свой CI\CD).   
Система облачная.     
GitLab Container Registry - имеет собственный реестр образов Docker.     
Есть пайплайны\кастомовые шаги, поддерживает параллельный запуск тестов\сборок.       

2) https://habr.com/ru/post/476368/    
GitHub Actions, мало знакомый мне инструмент, не уверен в выполнении всех требований.
3)https://blog.jetbrains.com/ru/teamcity/2020/11/teamcity-2020-2-integration-with-bitbucket-cloud-python-and-jetbrains-space-agentless-build-steps-new-administration-features-and-more/     
Как вариант могут подойти такие связки:
TeamCity Cloud + Bitbucket Cloud (но также можно в связке с GitHub\GitLab).
Вместо TeamCity, наверное, подойдет также облачная версия Jenkins X (что-то новое и неизвестное пока для меня).      



>
> ## Задача 2: Логи
>
> Предложите решение для обеспечения сбора и анализа логов сервисов в микросервисной архитектуре.
> Решение может состоять из одного или нескольких программных продуктов и должно описывать способы и принципы их взаимодействия.
>
> Решение должно соответствовать следующим требованиям:
> - Сбор логов в центральное хранилище со всех хостов обслуживающих систему;
> - Минимальные требования к приложениям, сбор логов из stdout;
> - Гарантированная доставка логов до центрального хранилища;
> - Обеспечение поиска и фильтрации по записям логов;
> - Обеспечение пользовательского интерфейса с возможностью предоставления доступа разработчикам для поиска по записям логов;
> - 
>
> Обоснуйте свой выбор.

Как самое популярное данный момент решение - ELK.
Возможность cбора из stdout. 
Фильтрация по ElasticSearch, имеет свой web-интерфейс.
Хорошо масштабируется по ElasticSearch.
По требованиям гарантии доставки настроить доставку нужно будет через TCP.    



>
> ## Задача 3: Мониторинг
>
> Предложите решение для обеспечения сбора и анализа состояния хостов и сервисов в микросервисной архитектуре.
> Решение может состоять из одного или нескольких программных продуктов и должно описывать способы и принципы их взаимодействия.
>
> Решение должно соответствовать следующим требованиям:
> - Сбор метрик со всех хостов, обслуживающих систему;
> - Сбор метрик состояния ресурсов хостов: CPU, RAM, HDD, Network;
> - Сбор метрик потребляемых ресурсов для каждого сервиса: CPU, RAM, HDD, Network;
> - Сбор метрик, специфичных для каждого сервиса;
> - Пользовательский интерфейс с возможностью делать запросы и агрегировать информацию;
> - Пользовательский интерфейс с возможность настраивать различные панели для отслеживания состояния системы;
>
> Обоснуйте свой выбор.

Для данной задачи на мой взгляд подойдет Prometheus, есть свой Dashboard,
который можно расширить добавлением к нему Grafana для визуализации агрегированных данных. 
Grafana может работать с Prometheus и обеспечивать графическое отображение,
имеет свои триггеры (в дополнение к имеющимся в Prometheus).
Возможно быстро настроить за счет наличия экспортеров практически под любую задачу.

> Доработка.   
> Не рассматривали вариант использования Elastic Stack для мониторинга с помощью metricsbeat, heartbeat? 

Возможно для сбора основных метрик использовать Metricbeat, модуль system.   
И «специфичных метрик» с помощью соответствующих модулей.  Не рассматривал изначально, так как не знал о наличии «специфичных» модулей.    
Снимать отдаваемые сервисами метрики по API.   
> Как будете мониторить ресурсы приложений с помощью Prometheus?   
 

Для Prometheus использовал бы exporter «Node exporter» для сбора общих метрик. И специфичных метрик используя exporters под конкретный сервис.    
Изначально неправильно понял, что значит специфичные, если имелось ввиду метрики отдаваемые сервисами по API, то тогда все же выбор будет в пользу ELK.      


>
> ## Задача 4: Кластеризация * (необязательная)
>
> Предложите решение для обеспечения развертывания, запуска и управления приложениями.
> Решение может состоять из одного или нескольких программных продуктов и должно описывать способы и принципы их взаимодействия.
>
> Решение должно соответствовать следующим требованиям:
> - Поддержка контейнеров;
> - Обеспечивать обнаружение сервисов и маршрутизацию запросов;
> - Обеспечивать возможность горизонтального масштабирования;
> - Обеспечивать возможность автоматического масштабирования;
> - Обеспечивать явное разделение ресурсов доступных извне и внутри системы;
> - Обеспечивать возможность конфигурировать приложения с помощью переменных среды, в том числе с возможностью безопасного хранения чувствительных данных таких как пароли, ключи доступа, ключи шифрования и т.п.
>
> Обоснуйте свой выбор.
>


> Доработка.    
>Не увидел предложенных вариантов инфраструктуры, доработайте, пожалуйста   


Думаю для данной цели следует использовать кластер  Kubernetes.   
В качестве оркестратора контейнеров можно использовать Kubernetes\ OpenShift.   
Kubernetes  имеет свои инструменты для построения сетей.   
Kubernetes имеет свой механизм хранения секретов (объект типа секрет),  но как понял более безопасный\ распространённым инструментом является  Vault от HashiCorp и при возможности лучше использовать его.   





