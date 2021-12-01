1. Какой системный вызов делает команда `cd`? В прошлом ДЗ мы выяснили, что `cd` не является самостоятельной  программой, это `shell builtin`, поэтому запустить `strace` непосредственно на `cd` не получится. Тем не менее, вы можете запустить `strace` на `/bin/bash -c 'cd /tmp'`. В этом случае вы увидите полный список системных вызовов, которые делает сам `bash` при старте. Вам нужно найти тот единственный, который относится именно к `cd`.

Ответ: chdir("/tmp")

1. Попробуйте использовать команду `file` на объекты разных типов на файловой системе. Например:
    ```bash
    vagrant@netology1:~$ file /dev/tty
    /dev/tty: character special (5/0)
    vagrant@netology1:~$ file /dev/sda
    /dev/sda: block special (8/0)
    vagrant@netology1:~$ file /bin/bash
    /bin/bash: ELF 64-bit LSB shared object, x86-64
    ```
    Используя `strace` выясните, где находится база данных `file` на основании которой она делает свои догадки.

Ответ: судя по успешному вызову openat(AT_FDCWD, "/etc/magic", O_RDONLY) = 3 база данных находиться в файле /etc/magic

1. Предположим, приложение пишет лог в текстовый файл. Этот файл оказался удален (deleted в lsof), однако возможности сигналом сказать приложению переоткрыть файлы или просто перезапустить приложение – нет. Так как приложение продолжает писать в удаленный файл, место на диске постепенно заканчивается. Основываясь на знаниях о перенаправлении потоков предложите способ обнуления открытого удаленного файла (чтобы освободить место на файловой системе).

1. Занимают ли зомби-процессы какие-то ресурсы в ОС (CPU, RAM, IO)?
Ответ: Зомби-процессы не занимают ресурсы ОС, но занимают место в таблице процеесов.

1. В iovisor BCC есть утилита `opensnoop`:
    ```bash
    root@vagrant:~# dpkg -L bpfcc-tools | grep sbin/opensnoop
    /usr/sbin/opensnoop-bpfcc
    ```
    На какие файлы вы увидели вызовы группы `open` за первую секунду работы утилиты? Воспользуйтесь пакетом `bpfcc-tools` для Ubuntu 20.04. Дополнительные [сведения по установке](https://github.com/iovisor/bcc/blob/master/INSTALL.md).
Ответ: насколько понял задание:
 sudo opensnoop-bpfcc
PID    COMM               FD ERR PATH
572    irqbalance          6   0 /proc/interrupts
572    irqbalance          6   0 /proc/stat
572    irqbalance          6   0 /proc/irq/20/smp_affinity
572    irqbalance          6   0 /proc/irq/0/smp_affinity
572    irqbalance          6   0 /proc/irq/1/smp_affinity
572    irqbalance          6   0 /proc/irq/8/smp_affinity
572    irqbalance          6   0 /proc/irq/12/smp_affinity
572    irqbalance          6   0 /proc/irq/14/smp_affinity
572    irqbalance          6   0 /proc/irq/15/smp_affinity
751    vminfo              5   0 /var/run/utmp
565    dbus-daemon        -1   2 /usr/local/share/dbus-1/system-services
565    dbus-daemon        18   0 /usr/share/dbus-1/system-services
565    dbus-daemon        -1   2 /lib/dbus-1/system-services
565    dbus-daemon        18   0 /var/lib/snapd/dbus-1/system-services/
751    vminfo              5   0 /var/run/utmp
565    dbus-daemon        -1   2 /usr/local/share/dbus-1/system-services
565    dbus-daemon        18   0 /usr/share/dbus-1/system-services
565    dbus-daemon        -1   2 /lib/dbus-1/system-services
565    dbus-daemon        18   0 /var/lib/snapd/dbus-1/system-services/


1. Какой системный вызов использует `uname -a`? Приведите цитату из man по этому системному вызову, где описывается альтернативное местоположение в `/proc`, где можно узнать версию ядра и релиз ОС.
Ответ: используется системный вызов uname.
Цитата: 
     Part of the utsname information is also accessible  via  /proc/sys/ker‐
       nel/{ostype, hostname, osrelease, version, domainname}.
(Часть информации так же доступна в /proc/sys/ker‐nel)

1. Чем отличается последовательность команд через `;` и через `&&` в bash? Например:
    ```bash
    root@netology1:~# test -d /tmp/some_dir; echo Hi
    Hi
    root@netology1:~# test -d /tmp/some_dir && echo Hi
    root@netology1:~#
    ```
    Есть ли смысл использовать в bash `&&`, если применить `set -e`?
Ответ: && - логичесий оператор, ;; - последовательность
При && последующая команда выполняется только при успешном выполнении предыдущей команды.
Использовать set -e с && не имеет смысла поскольку команда set -e указывает оболочке выйти в случае когда команда дает ненулевой статус выполнения 9то есть ошибку)

1. Из каких опций состоит режим bash `set -euxo pipefail` и почему его хорошо было бы использовать в сценариях?
-e указывает оболочке выйти, если команда дает ненулевой статус выхода. Проще говоря, оболочка завершает работу при сбое команды.
-u обрабатывает неустановленные или неопределенные переменные, за исключением специальных параметров, таких как подстановочные знаки (*) или «@», как ошибки во время раскрытия параметра.
-x печатает аргументы команды во время выполнения
-o pipfail не дает сценарию выполнятть дальше команды при возникновении ошибки
По сути данный режим дает более информативный вывод при отладке скриптов 
По сути данный режим дает более информативный вывод при отладке скриптов 

3. Используя `-o stat` для `ps`, определите, какой наиболее часто встречающийся статус у процессов в системе. В `man ps` ознакомьтесь (`/PROCESS STATE CODES`) что значат дополнительные к основной заглавной буквы статуса процессов. Его можно не учитывать при расчете (считать S, Ss или Ssl равнозначными).

 
