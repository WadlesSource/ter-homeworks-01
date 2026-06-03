# ter-homeworks-01

Задание 1
1. Перейдите в каталог src. Скачайте все необходимые зависимости, использованные в проекте.
2. Изучите файл .gitignore. В каком terraform-файле, согласно этому .gitignore, допустимо сохранить личную, секретную информацию?(логины,пароли,ключи,токены итд)
personal.auto.tfvars
3. Выполните код проекта. Найдите в state-файле секретное содержимое созданного ресурса random_password, пришлите в качестве ответа конкретный ключ и его значение.
 "result": "cs2fdpGtKTFhtYwJ"
4. Раскомментируйте блок кода, примерно расположенный на строчках 29–42 файла main.tf. Выполните команду terraform validate. Объясните, в чём заключаются намеренно допущенные ошибки. Исправьте их.
Error: Missing name for resource
on main.tf line 22, in resource "docker_image":
All resource blocks must have 2 labels (type, name).

Error: Invalid resource name
on main.tf line 27, in resource "docker_container" "1nginx":
A name must start with a letter or underscore and may contain only letters, digits, underscores, and dashes.

Error: Reference to undeclared resource
on main.tf line 29, in resource "docker_container" "nginx":
A managed resource "random_password" "random_string_FAKE" has not been declared in the root module.

5. Выполните код. В качестве ответа приложите: исправленный фрагмент кода и вывод команды docker ps.
resource "docker_image" "nginx" {
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
wadles@compute-vm-2-2-20-hdd-1779792515901:~/terraform$ docker ps
CONTAINER ID   IMAGE          COMMAND                  CREATED         STATUS         PORTS                  NAMES
230adf94e200   5aca99593157   "/docker-entrypoint.…"   6 seconds ago   Up 5 seconds   0.0.0.0:9090->80/tcp   example_cs2fdpGtKTFhtYwJ
6. Замените имя docker-контейнера в блоке кода на hello_world. Не перепутайте имя контейнера и имя образа. Мы всё ещё продолжаем использовать name = "nginx:latest". Выполните команду terraform apply -auto-approve. Объясните своими словами, в чём может быть опасность применения ключа -auto-approve. Догадайтесь или нагуглите зачем может пригодиться данный ключ? В качестве ответа дополнительно приложите вывод команды docker ps.
Исключение проверки человеком. Автоматизация и CI/CD пайплайны, локальная разработка и песочницы (Sandbox), регулярные автоматические задачи.
CONTAINER ID   IMAGE          COMMAND                  CREATED         STATUS         PORTS                  NAMES
32bdb5b2e140   5aca99593157   "/docker-entrypoint.…"   4 seconds ago   Up 3 seconds   0.0.0.0:9090->80/tcp   example_cs2fdpGtKTFhtYwJ
7. Уничтожьте созданные ресурсы с помощью terraform. Убедитесь, что все ресурсы удалены. Приложите содержимое файла terraform.tfstate.
<img width="1919" height="204" alt="image" src="https://github.com/user-attachments/assets/acbdbd82-cf24-4488-8a96-fd45e225470c" />
8. Объясните, почему при этом не был удалён docker-образ nginx:latest. Ответ ОБЯЗАТЕЛЬНО НАЙДИТЕ В ПРЕДОСТАВЛЕННОМ КОДЕ, а затем ОБЯЗАТЕЛЬНО ПОДКРЕПИТЕ строчкой из документации terraform провайдера docker. (ищите в классификаторе resource docker_image )
В первом ресурсе используется параметр keep_locally = true, поэтому образ не удаляется.
resource "docker_image" "nginx" {
  name         = "nginx:latest"
  keep_locally = true
}
resource "docker_container" "hello_world" {
  image = docker_image.nginx.image_id
  name  = "example_${random_password.random_string.result}"

  ports {
    internal = 80
    external = 9090
  }
}
keep_locally (Boolean) If true, then the Docker image won't be deleted on destroy operation. If this is false, it will delete the image from the docker local storage on destroy operation.
