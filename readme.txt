Для решения задачи:
Создайте Docker-образ приложения, которое будет при запуске контейнера скачивать favicon заданного приложению сайта.

Выбрал не полноценный образ для скачивания favicon, а максимально урезанную среду busybox.
На мой взгляд это самое бережное использование ресурсов хостовой машины для выполнения этой задачи.

Необходимо сразу отметить, что у образа нет даже bash только sh


Работа с монтированием раздела:
sudo docker volume create wget #Создаём раздел
sudo docker run  --rm --name busybox_01 --mount type=volume,src=wget,dst=/opt/test_busybox -e url=vk.ru 1155cbf93b04

где /opt/test_busybox - точка монтирования, указанная в Dockerfile, иначе качает, но не сохраняет, дело в ls
    1155cbf93b04 - ID образа, созданного при sudo dockers build ./ (см: sudo docker image ls )
Работа с пробросом в директорию:
sudo docker run  --rm --name busybox_01 --mount type=bind,src=/tmp/,dst=/opt/test_busybox -e url=vk.ru 1155cbf93b04 #Сохраняет в каталог /tmp/
    
Был испытан вариант с загрузкой через: wget http://www.google.com/s2/favicons?domain={url} где {url} имя подставленного сайта, не срабатывает, ругается на tls


Ключ --rm  используется не случайно, нет смысла держать машину в демане, разовый запуск, после чего она себя удаляет.

sudo docker run -ti --mount type=volume,src=wget,dst=/opt/test_busybox -e url=vk.ru 76a3b17471a9 sh (внимание на sh, а не bash)