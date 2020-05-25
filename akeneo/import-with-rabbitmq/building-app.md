# Building app

1. Create symfony flex app

2. Install rabbimq

    2.0 Description and credentials https://hub.docker.com/r/bitnami/rabbitmq/

RABBITMQ_USERNAME: RabbitMQ application username. Default: user
RABBITMQ_PASSWORD: RabbitMQ application password. Default: bitnami

Configs in bitnami/rabbitmq/conf/

    2.1 Test it
rabbitmqctl list_queues


3. Install 
https://github.com/corvus-ch/rabbitmq-cli-consumer


4. Follow examples


# Additionally

Super important !!!!
https://blog.vandenbrand.org/2015/01/09/symfony2-and-rabbitmq-lessons-learned/

Idea and pictures
https://hollo.me/php/experimental-async-php-volume-2.html
