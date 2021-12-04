# Решение домашней работы

1. Какой системный вызов делает команда `cd`? В прошлом ДЗ мы выяснили, что `cd` не является самостоятельной  программой, это `shell builtin`, поэтому запустить `strace` непосредственно на `cd` не получится. Тем не менее, вы можете запустить `strace` на `/bin/bash -c 'cd /tmp'`. В этом случае вы увидите полный список системных вызовов, которые делает сам `bash` при старте. Вам нужно найти тот единственный, который относится именно к `cd`. Обратите внимание, что `strace` выдаёт результат своей работы в поток stderr, а не в stdout.

Для выполнения задачи ввёл команду (перенаправил поток stderr на stdout и нашёл все строки в которых указан путь `/tmp`):

```bash
vagrant@vagrant:~$ strace /bin/bash -c 'cd /tmp' 2>&1 | grep \/tmp
```

В результате видим строки:

```bash
vagrant@vagrant:~$ strace /bin/bash -c 'cd /tmp' 2>&1 | grep \/tmp
execve("/bin/bash", ["/bin/bash", "-c", "cd /tmp"], 0x7ffe0fb8e1f0 /* 24 vars */) = 0
stat("/tmp", {st_mode=S_IFDIR|S_ISVTX|0777, st_size=4096, ...}) = 0
chdir("/tmp")                           = 0
```

* `execve` — выполнить исполняемый файл;
* `stat` — получить статус файла;
* `chdir` — изменить текущую директорию.

Ответ: `chdir("/tmp") = 0`

2. Попробуйте использовать команду `file` на объекты разных типов на файловой системе. Например:
    ```bash
    vagrant@netology1:~$ file /dev/tty
    /dev/tty: character special (5/0)
    vagrant@netology1:~$ file /dev/sda
    /dev/sda: block special (8/0)
    vagrant@netology1:~$ file /bin/bash
    /bin/bash: ELF 64-bit LSB shared object, x86-64
    ```
    Используя `strace` выясните, где находится база данных `file` на основании которой она делает свои догадки.

При вызове команды `strace file /dev/sda` видно, что последний открытый файл это `/usr/share/misc/magic.mgc`, после был произведён сигнал вывода.  

```bash
vagrant@vagrant:~$ strace file /dev/sda

...
openat(AT_FDCWD, "/usr/share/misc/magic.mgc", O_RDONLY) = 3
...
```

Ответ: `/usr/share/misc/magic.mgc`

3. Предположим, приложение пишет лог в текстовый файл. Этот файл оказался удален (deleted в lsof), однако возможности сигналом сказать приложению переоткрыть файлы или просто перезапустить приложение – нет. Так как приложение продолжает писать в удаленный файл, место на диске постепенно заканчивается. Основываясь на знаниях о перенаправлении потоков предложите способ обнуления открытого удаленного файла (чтобы освободить место на файловой системе).

Для имитации записи в лог создадим python-скрипт `log.py`:

```python
import time

f=open('log.txt', 'w')
f.write('new log file\n')
f.close()

while True: 
	f=open('log.txt', 'a')
	f.write('new line\n')
	time.sleep(5)
	f.close()

```

Далее, запустим скрипт и выведем его PID:

```bash
vagrant@vagrant:~$ python3 log.py &

[1] 2310
```

Проверим информацию в `ps`:

```bash
vagrant@vagrant:~$ ps aux | grep python3

...
vagrant     2310  0.2  0.4  18420  8928 pts/0    S    20:47   0:00 python3 log.py
...
```

Убедимся, что запись в файл работает:

```bash
vagrant@vagrant:~$ wc -l log.txt

6
```

```bash
vagrant@vagrant:~$ cat log.txt

new log file
new line
new line
new line
new line
new line
```

Удалим файл:

```bash
vagrant@vagrant:~$ rm log.txt
```

Выведем файлы, используемые процессом `2310`:

```bash
vagrant@vagrant:~$ lsof -p 2310

...
python3 2310 vagrant    3w   REG  253,0       22 131092 /home/vagrant/log.txt
...
```

Проверим, что запись в удалённый файл работает:

```bash
vagrant@vagrant:~$ wc -l /proc/2310/fd/3

4
```

С помощью команды `truncate` очистим файл и проверим его размер: 

```bash
vagrant@vagrant:~$  truncate -s 0 /proc/2310/fd/3
vagrant@vagrant:~$  wc -l /proc/2310/fd/3

0
```

4. Занимают ли зомби-процессы какие-то ресурсы в ОС (CPU, RAM, IO)?

Зомби не занимают памяти (как процессы-сироты), но блокируют записи в таблице процессов, размер которой ограничен для каждого пользователя и системы в целом

5. В iovisor BCC есть утилита `opensnoop`:
    ```bash
    root@vagrant:~# dpkg -L bpfcc-tools | grep sbin/opensnoop
    /usr/sbin/opensnoop-bpfcc
    ```
    На какие файлы вы увидели вызовы группы `open` за первую секунду работы утилиты? Воспользуйтесь пакетом `bpfcc-tools` для Ubuntu 20.04. Дополнительные [сведения по установке](https://github.com/iovisor/bcc/blob/master/INSTALL.md).

```bash
vagrant@vagrant:~$ sudo apt-get install bpfcc-tools linux-headers-$(uname -r)
...
vagrant@vagrant:~$ sudo opensnoop-bpfcc

PID    COMM               FD ERR PATH
808    vminfo              4   0 /var/run/utmp
625    dbus-daemon        -1   2 /usr/local/share/dbus-1/system-services
625    dbus-daemon        18   0 /usr/share/dbus-1/system-services
625    dbus-daemon        -1   2 /lib/dbus-1/system-services
625    dbus-daemon        18   0 /var/lib/snapd/dbus-1/system-services/
808    vminfo              4   0 /var/run/utmp
625    dbus-daemon        -1   2 /usr/local/share/dbus-1/system-services
625    dbus-daemon        18   0 /usr/share/dbus-1/system-services
625    dbus-daemon        -1   2 /lib/dbus-1/system-services
625    dbus-daemon        18   0 /var/lib/snapd/dbus-1/system-services/
635    irqbalance          6   0 /proc/interrupts
635    irqbalance          6   0 /proc/stat
635    irqbalance          6   0 /proc/irq/20/smp_affinity
635    irqbalance          6   0 /proc/irq/0/smp_affinity
635    irqbalance          6   0 /proc/irq/1/smp_affinity
635    irqbalance          6   0 /proc/irq/8/smp_affinity
635    irqbalance          6   0 /proc/irq/12/smp_affinity
635    irqbalance          6   0 /proc/irq/14/smp_affinity
635    irqbalance          6   0 /proc/irq/15/smp_affinity
808    vminfo              4   0 /var/run/utmp
625    dbus-daemon        -1   2 /usr/local/share/dbus-1/system-services
625    dbus-daemon        18   0 /usr/share/dbus-1/system-services
625    dbus-daemon        -1   2 /lib/dbus-1/system-services
625    dbus-daemon        18   0 /var/lib/snapd/dbus-1/system-services/
...
```

6. Какой системный вызов использует `uname -a`? Приведите цитату из man по этому системному вызову, где описывается альтернативное местоположение в `/proc`, где можно узнать версию ядра и релиз ОС.

Системный вызов `/usr/bin/uname`.

Для более удобной работы с `man` установим пакет `manpages-dev`:

```bash
sudo apt install manpages-dev
```

Командой `man 2 uname` найдём альтернативный способ получения версии ядра или релиз ОС

```bash
vagrant@vagrant:~$ man 2 uname

...
Part of the utsname information is also accessible via /proc/sys/kernel/{ostype, hostname, osrelease, version, domainname}.
...
```

Проверим ОС: 

```bash
vagrant@vagrant:~$ cat /proc/sys/kernel/version
#90-Ubuntu SMP Fri Jul 9 22:49:44 UTC 2021
```

7. Чем отличается последовательность команд через `;` и через `&&` в bash? Например:
    ```bash
    root@netology1:~# test -d /tmp/some_dir; echo Hi
    Hi
    root@netology1:~# test -d /tmp/some_dir && echo Hi
    root@netology1:~#
    ```
    Есть ли смысл использовать в bash `&&`, если применить `set -e`?

`;` — последовательное выполнение команд. сообщение `Hi` выведется в любом случае.

`&&` — логическое И; `Hi` выведется в случае если существует директория `/tmp/some_dir`.


8. Из каких опций состоит режим bash `set -euxo pipefail` и почему его хорошо было бы использовать в сценариях?

Вот [тут в explainshell](https://explainshell.com/explain?cmd=set%20-euxo%20pipefail) хорошо и наглядно расписано 

> `-e`  When this option is on, if a simple command fails for any of the reasons listed in Consequences of
>       Shell  Errors or returns an exit status value >0, and is not part of the compound list following a
>       while, until, or if keyword, and is not a part of an AND  or  OR  list,  and  is  not  a  pipeline
>       preceded by the ! reserved word, then the shell shall immediately exit.
> 
> `-u`  The shell shall write a message to standard error when it tries to expand a variable that  is  not
>       set and immediately exit. An interactive shell shall not exit.
> 
> `-x`  The  shell shall write to standard error a trace for each command after it expands the command and
>       before it executes it. It is unspecified whether the command that turns tracing off is traced.
>
> `-o`  Write the current settings of the options to standard output in an unspecified format.
> 
> `+o`  Write the current option settings to standard output in a format that is suitable for  reinput  to
>       the shell as commands that achieve the same options settings.
9. Используя `-o stat` для `ps`, определите, какой наиболее часто встречающийся статус у процессов в системе. В `man ps` ознакомьтесь (`/PROCESS STATE CODES`) что значат дополнительные к основной заглавной буквы статуса процессов. Его можно не учитывать при расчете (считать S, Ss или Ssl равнозначными).

```bash
vagrant@vagrant:~$ ps -o stat
STAT
Ss
T
T
T
R+
```

```bash
PROCESS STATE CODES
       Here are the different values that the s, stat and state output specifiers (header "STAT" or "S") will display to describe the state of a
       process:

               D    uninterruptible sleep (usually IO)
               I    Idle kernel thread
               R    running or runnable (on run queue)
               S    interruptible sleep (waiting for an event to complete)
               T    stopped by job control signal
               t    stopped by debugger during the tracing
               W    paging (not valid since the 2.6.xx kernel)
               X    dead (should never be seen)
               Z    defunct ("zombie") process, terminated but not reaped by its parent

       For BSD formats and when the stat keyword is used, additional characters may be displayed:

               <    high-priority (not nice to other users)
               N    low-priority (nice to other users)
               L    has pages locked into memory (for real-time and custom IO)
               s    is a session leader
               l    is multi-threaded (using CLONE_THREAD, like NPTL pthreads do)
               +    is in the foreground process group
```