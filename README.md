### Домашнее задание к занятию «Запуск приложений в K8S»
## Задание 1. Создать Deployment и обеспечить доступ к репликам приложения из другого Pod
Создать Deployment приложения, состоящего из двух контейнеров — nginx и multitool. Решить возникшую ошибку.
После запуска увеличить количество реплик работающего приложения до 2.
Продемонстрировать количество подов до и после масштабирования.
Создать Service, который обеспечит доступ до реплик приложений из п.1.
Создать отдельный Pod с приложением multitool и убедиться с помощью curl, что из пода есть доступ до приложений из п.1.
## Задание 2. Создать Deployment и обеспечить старт основного контейнера при выполнении условий
Создать Deployment приложения nginx и обеспечить старт контейнера только после того, как будет запущен сервис этого приложения.
Убедиться, что nginx не стартует. В качестве Init-контейнера взять busybox.
Создать и запустить Service. Убедиться, что Init запустился.
Продемонстрировать состояние пода до и после запуска сервиса.

## Решение задания 1. 
## Создать Deployment и обеспечить доступ к репликам приложения из другого Pod

Создаю отдельный Namespace для того, чтобы созданные в этом задании поды, деплойменты, сервисы работали отдельно от остальных, ранее созданных:

![img1_1](https://github.com/user-attachments/assets/e1e2563f-5398-4737-a6e4-43f3be6d41a2)

Пишем манифест Deployment приложения, состоящего из двух контейнеров — nginx и multitool. 
В случае, если порты 80 и 443 заняты, то запуск multitool потребует указания альтернативного порта. Для этого в манифест 
Deployment добавим переменную с указанием порта 1180.

Ссылка на манифест Deployment - https://github.com/slava1005/1_3/blob/main/deployment.yaml

Запускаем Deployment:

![img1_2](https://github.com/user-attachments/assets/598ef5f7-849a-487d-9763-ab914fc003c3)

Проверяю результат:

![img1_3](https://github.com/user-attachments/assets/21c7c198-50ac-4657-8dc2-3d1615346df5)

Сейчас у меня запущена одна реплика приложения nginx-multitool. Увеличим количество реплик до двух и проверим результат:

![img1_4](https://github.com/user-attachments/assets/768cdaba-8cd7-4587-b84c-ca20f8f816f6)

Видно, что количество реплик в AVAILABLE увеличилось до двух и обе запущены.

До масштабирования у меня был один под:

![img1_5](https://github.com/user-attachments/assets/4a11821a-ca73-4953-8d39-f23ac01abbf4)

После масштабирования стало два пода:

![img1_6](https://github.com/user-attachments/assets/9d412ae2-6463-4cd2-9025-c90bb98cc2c9)

Пишем манифест Service с именем nginx-multitool-svc в namespace netology. Применяю манифест:

![img1_7](https://github.com/user-attachments/assets/71ad1285-b57e-4399-bcc7-6f2baa726daa)

Ссылка на манифест Service - https://github.com/slava1005/1_3/blob/main/service.yaml

Проверим сервисы в namespace netology:

![img1_8](https://github.com/user-attachments/assets/1fdbca05-a706-483a-82db-74db76f090cc)

Сервис создан.

Пишем манифест отдельного пода multitool в namespace netology. Применим манифест:

![img1_9](https://github.com/user-attachments/assets/2aa9e961-e16d-4fd1-9f56-83fdb9cead3b)

Ссылка на манифест с подом multitool - https://github.com/slava1005/1_3/blob/main/multitool.yaml

Проверим поды в namespace netology:

![img1_10](https://github.com/user-attachments/assets/afd8040e-3db9-49c9-9d6c-801d8f2727d9)

Видим, что под с именем multitool создан и запущен (хотя на скрине он в состоянии created потом он запустился.
Запуск был долгим).

С помощью curl, проверим, есть ли из пода multitool доступ до приложений из п.1.:

![img1_11](https://github.com/user-attachments/assets/39534195-6c43-4352-93c5-5bada0b0b116)

При обращении на порт 80 сервиса ответил запущенный nginx.

![img1_12](https://github.com/user-attachments/assets/09aec7f6-dbc6-48a5-b78c-1e3d0c8579c5)

При обращении на порт 8080 сервиса ответил запущенный multitool.

## Решение задания 2. 
## Создать Deployment и обеспечить старт основного контейнера при выполнении условий:

1 - 2. Создаем манифест Deployment приложения nginx, который запустится только после запуска сервиса. В качестве Init-контейнера используем busybox:

![img1_13](https://github.com/user-attachments/assets/fada2463-9708-4851-a0e7-14c7713f5b0d)

Deployment создан, проверим, запущен ли под:

![img1_14](https://github.com/user-attachments/assets/66d98910-0cb8-494a-a5c0-83c1be75569f)

Видим, что под не запущен и находится в состоянии Init:0/1.

Ссылка на манифест Deployment - https://github.com/slava1005/1_3/blob/main/nginx-init-deploy.yaml

3 - 4. Создаем манифест Service, применим его и проверим, запустился ли под nginx:

![img1_15](https://github.com/user-attachments/assets/eddcbbd7-2b03-4381-8407-552e4e45aa15)

После запуска сервиса, запустился под с nginx.

Ссылка на манифест Service - https://github.com/slava1005/1_3/blob/main/nginx-init-svc.yaml
