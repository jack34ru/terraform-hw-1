# Домашнее задание к занятию «Введение в Terraform» - Кузнецов Евгений Александрович

### Цели задания

1. Установить и настроить Terrafrom.
2. Научиться использовать готовый код.

------

### Чек-лист готовности к домашнему заданию

1. Скачайте и установите **Terraform** версии >=1.12.0 . Приложите скриншот вывода команды ```terraform --version```.
2. Скачайте на свой ПК этот git-репозиторий. Исходный код для выполнения задания расположен в директории **01/src**.
3. Убедитесь, что в вашей ОС установлен docker.

------

### Инструменты и дополнительные материалы, которые пригодятся для выполнения задания

1. Репозиторий с ссылкой на зеркало для установки и настройки Terraform: [ссылка](https://github.com/netology-code/devops-materials).
2. Установка docker: [ссылка](https://docs.docker.com/engine/install/ubuntu/). 
------
### Внимание!! Обязательно предоставляем на проверку получившийся код в виде ссылки на ваш github-репозиторий!
------

### Задание 1

1. Перейдите в каталог [**src**](https://github.com/netology-code/ter-homeworks/tree/main/01/src). Скачайте все необходимые зависимости, использованные в проекте. 
2. Изучите файл **.gitignore**. В каком terraform-файле, согласно этому .gitignore, допустимо сохранить личную, секретную информацию?(логины,пароли,ключи,токены итд)
3. Выполните код проекта. Найдите  в state-файле секретное содержимое созданного ресурса **random_password**, пришлите в качестве ответа конкретный ключ и его значение.
4. Раскомментируйте блок кода, примерно расположенный на строчках 29–42 файла **main.tf**.
Выполните команду ```terraform validate```. Объясните, в чём заключаются намеренно допущенные ошибки. Исправьте их.
5. Выполните код. В качестве ответа приложите: исправленный фрагмент кода и вывод команды ```docker ps```.
6. Замените имя docker-контейнера в блоке кода на ```hello_world```. Не перепутайте имя контейнера и имя образа. Мы всё ещё продолжаем использовать name = "nginx:latest". Выполните команду ```terraform apply -auto-approve```.
Объясните своими словами, в чём может быть опасность применения ключа  ```-auto-approve```. Догадайтесь или нагуглите зачем может пригодиться данный ключ? В качестве ответа дополнительно приложите вывод команды ```docker ps```.
7. Уничтожьте созданные ресурсы с помощью **terraform**. Убедитесь, что все ресурсы удалены. Приложите содержимое файла **terraform.tfstate**. 
8. Объясните, почему при этом не был удалён docker-образ **nginx:latest**. Ответ **ОБЯЗАТЕЛЬНО НАЙДИТЕ В ПРЕДОСТАВЛЕННОМ КОДЕ**, а затем **ОБЯЗАТЕЛЬНО ПОДКРЕПИТЕ** строчкой из документации [**terraform провайдера docker**](https://library.tf/providers/kreuzwerker/docker/latest).  (ищите в классификаторе resource docker_image )

Скриншот 1 к чек-листу 1  
![Screen 1](https://github.com/jack34ru/terraform-hw-1/blob/main/screenshots/Screenshot_211.png)  

### Решение 1

# 1  
Скриншот 1 к пункту 1  
![Screen 1](https://github.com/jack34ru/terraform-hw-1/blob/main/screenshots/Screenshot_212.png)  

# 2  
Скриншот 1 к пункту 2  
![Screen 1](https://github.com/jack34ru/terraform-hw-1/blob/main/screenshots/Screenshot_213.png)  

# 3  
Скриншот 1 к пункту 2  
![Screen 1](https://github.com/jack34ru/terraform-hw-1/blob/main/screenshots/Screenshot_214.png)  

"result": "gqLN51vilVHwqyPS"

# 4  
- У ресурса docker_image отсутствовало имя ресурса
- Имя ресурса docker_container начиналось с цифры 1, в terraform это запрещено
- Неправильное обращение к random_password: T заглавное в result и несуществующий ресурс random_string_FAKE

# 5  
Исправленный фрагмент кода:  
```
resource "docker_image" "nginx"{
  name         = "nginx:latest"
  keep_locally = true
}

resource "docker_container" "nginx" {
  image = docker_image.nginx.image_id
  name  = "example_${random_password.random_string.result}"

  ports {
    internal = 80
    external = 9090
  }
}
```
Скриншот 1 к пункту 5  
![Screen 1](https://github.com/jack34ru/terraform-hw-1/blob/main/screenshots/Screenshot_215.png)  

# 6  
Скриншот 1 к пункту 6  
![Screen 1](https://github.com/jack34ru/terraform-hw-1/blob/main/screenshots/Screenshot_216.png)  

-auto-approve - изменения применяются без подтверждения. Можно удалить инфраструктуру, базы данных, пересоздать серверы. Может пригодиться в скриптах автоматизации например.

# 7  
Скриншот 1 к пункту 7  
![Screen 1](https://github.com/jack34ru/terraform-hw-1/blob/main/screenshots/Screenshot_217.png)  
Содержимое файла terraform.tfstate
```
{
  "version": 4,
  "terraform_version": "1.14.5",
  "serial": 11,
  "lineage": "20ab73b7-166a-833f-88ca-0f0b8f718a11",
  "outputs": {},
  "resources": [],
  "check_results": null
}
```

# 8  
В ресурсе  docker_image есть ключ keep_locally = true  
Из документации: keep_locally (Boolean) If true, then the Docker image won't be deleted on destroy operation. If this is false, it will delete the image from the docker local storage on destroy operation. (Если значение true, то образ не удаляется из локального хранилища. Если false - то удаляется)


[Финальный файл main.tf](https://github.com/jack34ru/terraform-hw-1/blob/main/main.tf)