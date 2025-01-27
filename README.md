# Домашнее задание к занятию "`Введение в Terraform`" - `Маркин Алексей`

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
8. Объясните, почему при этом не был удалён docker-образ **nginx:latest**. Ответ **ОБЯЗАТЕЛЬНО НАЙДИТЕ В ПРЕДОСТАВЛЕННОМ КОДЕ**, а затем **ОБЯЗАТЕЛЬНО ПОДКРЕПИТЕ** строчкой из документации [**terraform провайдера docker**](https://docs.comcloud.xyz/providers/kreuzwerker/docker/latest/docs).  (ищите в классификаторе resource docker_image )

## Решение 1

2. В файле под названием **personal.auto.tfvars**

3. **SetLoXNmKny82I04**

![Вопрос3](https://github.com/Markin-AI/ter-01/blob/main/img/3.png)

4. Первая ошибка не указано название русурса, вторая ошибка опечатка в названии ресурса.

![Вопрос4](https://github.com/Markin-AI/ter-01/blob/main/img/4-1.png)

Третья ошибка после устранения первых двух, там так же опечатки при обращении к ресурсу.

![Вопрос4](https://github.com/Markin-AI/ter-01/blob/main/img/4-2.png)

5. 

```
terraform {
  required_providers {
    docker = {
      source  = "kreuzwerker/docker"
      version = "~> 3.0.1"
    }
  }
  required_version = ">=1.8.4" /*Многострочный комментарий.
 Требуемая версия terraform */
}
provider "docker" {}

#однострочный комментарий

resource "random_password" "random_string" {
  length      = 16
  special     = false
  min_upper   = 1
  min_lower   = 1
  min_numeric = 1
}


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

![Вопрос5](https://github.com/Markin-AI/ter-01/blob/main/img/5.png)

6. 

 - Возможно ошибиться и без подтверждения изменить состояние инфраструктуры, изменить в том числе и фатально.
 
 - При использовании автоматизации, например в CI/CD системах где все уже проверено, и нужно автоматически запустить развертывание структуры.

![Вопрос6](https://github.com/Markin-AI/ter-01/blob/main/img/6.png)
 
7. 

![Вопрос7](https://github.com/Markin-AI/ter-01/blob/main/img/7.png)

8. 

![Вопрос8](https://github.com/Markin-AI/ter-01/blob/main/img/8.png)

```keep_locally = true```

Если значение стоит **true** Docker image не будет удален при операции уничтножения. Если значение **false**, то образ будет удален из локального хранилища docker при операции destroy.

[Ссылка на документацию](https://docs.comcloud.xyz/providers/kreuzwerker/docker/latest/docs/resources/image)

---