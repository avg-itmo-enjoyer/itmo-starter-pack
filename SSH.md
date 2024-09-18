# Краткий гайд, упрощающий использование терминального SSH-клиента
## 1. Аутентификация по сертификатам:
  Вместо того, чтобы при каждом логине на сервер вводить пароль, можно сгенерировать пару ключей (публичный и приватный), 
  выгрузить публичный ключ на сервер и логиниться на сервера (1-на пара ключей может быть использована более чем для 1-го сервера) при помощи данной пары ключей.

  Ssh-ключи также необходимы для нормальной работы с хостингами git-репозиториев (github, gitlab, gitverse, gitflic). Но тут уже сами.
  
  Для начала необходимо сгенерировать ключи, введя в терминале:
  
  ```console
  user@host /path/to/current/location$ ssh-keygen
  Generating public/private rsa key pair.
  Enter file in which to save the key (<path to your home directory>/.ssh/id_rsa):
  Enter passphrase (empty for no passphrase):
  Enter same passphrase again:
  Your identification has been saved in <path to your home directory>/.ssh/id_rsa
  Your public key has been saved in <path to your home directory>/.ssh/id_rsa.pub
  The key fingerprint is:
  SHA256:iuGVsO70abWATj9berzpzicd6vJ2wN2LDWl/gtDfDME user@host
  The key's randomart image is:
  +---[RSA 3072]----+
  |                 |
  |                 |
  |    .      .     |
  |     o .    E    |
  |    o.o.So o .   |
  |   oo+..= B o    |
  |   o=..+.B O =   |
  |   o..==B.* * +  |
  |    ..=@O=   o   |
  +----[SHA256]-----+ 
  ```
  
  После ввода данной данной команды и успешного скипа (путём нажатия Enter) всех шагов - получится что-то вроде вывода, приведённого выше.
  После этого необходимо выгрузить публичный ключ на сервер. Это можно сделать банальным копированием через *scp* в нужную директорию.
  Однако проще это сделать следующим образом:

  ```console
  user@host /path/to/current/location$ ssh-copy-id -p 2222 s<XXXXXX>@se.ifmo.ru
  ```
  
  Если ключи были сгенерированы по стандартному пути, то их имена можно не задавать явно при использовании *ssh-copy-id*. 
  В противном случае можно воспользоваться опцией *ssh-copy-id -i* (подробнее прочитать о ней можно в мануле или стандартной справке утилиты).

  Теперь можно попробовать залогиниться (пароль от учётной записи больше не потребуется).

## 2. Создание config-файла для SSH-клиента:
  Следующей ступенью на пути упрощения использования ssh может стать создание конфигурационного файла.
  В моём случае это было полезно для того, чтобы упростить процесс логина на helios. Вместо:

  ```console
  user@host /path/to/current/location$ ssh -p 2222 s<XXXXXX>@se.ifmo.ru
  ```
  
  Теперь можно ввести:

  ```console
  user@host /path/to/current/location$ ssh <your defined alias for host>
  ```

  Для этого необходимо создать файл *<path to your home directory>/.ssh/config* со следующим содержанием:

  ```config
  Host <your defined alias for host>
    HostName se.ifmo.ru
    PreferredAuthentications publickey
    IdentityFile <path to your home directory>/.ssh/<your private key filename - id_rsa by default>
    User s<XXXXXX>
    Port 2222
  ```

  После сохранения файла станет доступен выше описанный вариант логина на сервер (однако на этом возможные опции конфигурации не исчерпываются).
