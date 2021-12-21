# Решение домашней работы

1. Какого типа команда `cd`? Попробуйте объяснить, почему она именно такого типа; опишите ход своих мыслей, если считаете что она могла бы быть другого типа.

```bash
vagrant@vagrant:~$ type cd
cd is a shell builtin
```

```bash
vagrant@vagrant:~$ whereis cd
cd:
```

Команда `cd` (change directory) встроенная команда shell для изменения текущей директории. Не является файлом/процессом. Таким образом есть тип встроенных (builtin) команд, среди которых `cd`.

2. Какая альтернатива без pipe команде `grep <some_string> <some_file> | wc -l`? `man grep` поможет в ответе на этот вопрос. Ознакомьтесь с [документом](http://www.smallo.ruhr.de/award.html) о других подобных некорректных вариантах использования pipe.

Альтернатива: `grep -c <some_string> <some_file>`. Флаг `-c` выводит количество строк в которых найдено совпадение строки.

3. Какой процесс с PID `1` является родителем для всех процессов в вашей виртуальной машине Ubuntu 20.04?

```bash
pstree -p 1
```

```bash
vagrant@vagrant:~$ pstree -p 1
systemd(1)─┬─VBoxService(808)─┬─{VBoxService}(810)
           │                  ├─{VBoxService}(811)
           │                  ├─{VBoxService}(812)
           │                  ├─{VBoxService}(813)
           │                  ├─{VBoxService}(814)
           │                  ├─{VBoxService}(815)
           │                  ├─{VBoxService}(816)
           │                  └─{VBoxService}(817)
           ├─accounts-daemon(621)─┬─{accounts-daemon}(636)
           │                      └─{accounts-daemon}(664)
           ├─agetty(844)
           ├─atd(833)
           ├─cron(830)
           ├─dbus-daemon(625)
           ├─irqbalance(635)───{irqbalance}(646)
           ├─multipathd(558)─┬─{multipathd}(559)
           │                 ├─{multipathd}(560)
           │                 ├─{multipathd}(561)
           │                 ├─{multipathd}(562)
           │                 ├─{multipathd}(563)
           │                 └─{multipathd}(564)
           ├─networkd-dispat(637)
           ├─polkitd(673)─┬─{polkitd}(674)
           │              └─{polkitd}(676)
           ├─rpcbind(588)
           ├─rsyslogd(639)─┬─{rsyslogd}(650)
           │               ├─{rsyslogd}(651)
           │               └─{rsyslogd}(652)
           ├─sshd(836)───sshd(1123)───sshd(1175)───bash(1176)───pstree(1195)
           ├─systemd(1139)───(sd-pam)(1140)
           ├─systemd-journal(384)
           ├─systemd-logind(644)
           ├─systemd-network(425)
           ├─systemd-resolve(591)
           └─systemd-udevd(411)
```

Процесс `systemd`.

4. Как будет выглядеть команда, которая перенаправит вывод stderr `ls` на другую сессию терминала?

Чтобы выяснить путь текущего терминала введём команду `tty`:

```bash
vagrant@vagrant:~$ tty
/dev/pts/0
```

Для второй сессии используется путь `/dev/pts/1`:

```bash
vagrant@vagrant:~$ tty
/dev/pts/1
```

Создал ещё одну SSH-сессию командой `vagrant ssh` и направил поток std out по пути `/dev/pts/0` to `/dev/pts/1`:

```bash
vagrant@vagrant:~$ ls > /dev/pts/1
vagrant@vagrant:~$
-------
vagrant@vagrant:~$ file_2.txt  file.txt
```

5. Получится ли одновременно передать команде файл на stdin и вывести ее stdout в другой файл? Приведите работающий пример.

```bash
cat < file.txt > file_2.txt
```

6. Получится ли находясь в графическом режиме, вывести данные из PTY в какой-либо из эмуляторов TTY? Сможете ли вы наблюдать выводимые данные?

Да, пример: `echo 'Hi it is am' > /dev/pts/1`.

7. Выполните команду `bash 5>&1`. К чему она приведет? Что будет, если вы выполните `echo netology > /proc/$$/fd/5`? Почему так происходит? 

Команда `bash 5>&1` создаёт дескриптор с условным номером 5 и связывает его с std out текущего процесса. При выполнении команды `echo netology > /proc/$$/fd/5` выведится слово `netology`.

8. Получится ли в качестве входного потока для pipe использовать только stderr команды, не потеряв при этом отображение stdout на pty? Напоминаем: по умолчанию через pipe передается только stdout команды слева от `|` на stdin команды справа.
Это можно сделать, поменяв стандартные потоки местами через промежуточный новый дескриптор, который вы научились создавать в предыдущем вопросе.

Направляем созданный 5 дескриптор на 2, а 2 на 1: `ls dir 5>&2 2>&1 | grep dir`.

9. Что выведет команда `cat /proc/$$/environ`? Как еще можно получить аналогичный по содержанию вывод?

Команда выводит переменные окружения текущей сессии. Аналог — `env`.

10. Используя `man`, опишите что доступно по адресам `/proc/<PID>/cmdline`, `/proc/<PID>/exe`.

```bash
man proc | grep -n -B 1 -A 2 cmdline
man proc | grep -n -B 1 -A 2 exe
```
```bash
194:       /proc/[pid]/cmdline
195-              This read-only file holds the complete command line for the process, unless the process is a zombie.  In the latter case, there is nothing
196-              in  this  file: that is, a read on this file will return 0 characters.  The command-line arguments appear in this file as a set of strings
```
```bash
241:       /proc/[pid]/exe
242:              Under Linux 2.2 and later, this file is a symbolic link containing the actual pathname of the executed command.  This symbolic link can be
243:              dereferenced normally; attempting to open it will open the executable.  You can even type /proc/[pid]/exe to run another copy of the  same
244:              executable  that  is being run by process [pid].  If the pathname has been unlinked, the symbolic link will contain the string '(deleted)'
245-              appended to the original pathname.  In a multithreaded process, the contents of this symbolic link are not available if  the  main  thread
246-              has already terminated (typically by calling pthread_exit(3)).
```
`/proc/<PID>/cmdline` — файл, содержащий полную командную строку для процесса.

`/proc/<PID>/exe` — файл, содержащий символьную ссылку на фактический путь к процессу

11. Узнайте, какую наиболее старшую версию набора инструкций SSE поддерживает ваш процессор с помощью `/proc/cpuinfo`.

```bash
vagrant@vagrant:~$ cat /proc/cpuinfo | grep sse
flags           : fpu vme de pse tsc msr pae mce cx8 apic sep mtrr pge mca cmov pat pse36 clflush mmx fxsr sse sse2 ht syscall nx rdtscp lm constant_tsc rep_good nopl xtopology nonstop_tsc cpuid tsc_known_freq pni pclmulqdq ssse3 cx16 pcid sse4_1 sse4_2 x2apic popcnt aes xsave avx rdrand hypervisor lahf_lm pti fsgsbase md_clear flush_l1d
flags           : fpu vme de pse tsc msr pae mce cx8 apic sep mtrr pge mca cmov pat pse36 clflush mmx fxsr sse sse2 ht syscall nx rdtscp lm constant_tsc rep_good nopl xtopology nonstop_tsc cpuid tsc_known_freq pni pclmulqdq ssse3 cx16 pcid sse4_1 sse4_2 x2apic popcnt aes xsave avx rdrand hypervisor lahf_lm pti fsgsbase md_clear flush_l1d
flags           : fpu vme de pse tsc msr pae mce cx8 apic sep mtrr pge mca cmov pat pse36 clflush mmx fxsr sse sse2 ht syscall nx rdtscp lm constant_tsc rep_good nopl xtopology nonstop_tsc cpuid tsc_known_freq pni pclmulqdq ssse3 cx16 pcid sse4_1 sse4_2 x2apic popcnt aes xsave avx rdrand hypervisor lahf_lm pti fsgsbase md_clear flush_l1d
flags           : fpu vme de pse tsc msr pae mce cx8 apic sep mtrr pge mca cmov pat pse36 clflush mmx fxsr sse sse2 ht syscall nx rdtscp lm constant_tsc rep_good nopl xtopology nonstop_tsc cpuid tsc_known_freq pni pclmulqdq ssse3 cx16 pcid sse4_1 sse4_2 x2apic popcnt aes xsave avx rdrand hypervisor lahf_lm pti fsgsbase md_clear flush_l1d
vagrant@vagrant:~$
```

Версия SSE: `sse4_2`.

12. При открытии нового окна терминала и `vagrant ssh` создается новая сессия и выделяется pty. Это можно подтвердить командой `tty`, которая упоминалась в лекции 3.2. Однако:

     ```bash
     vagrant@netology1:~$ ssh localhost 'tty'
     not a tty
     ```

     Почитайте, почему так происходит, и как изменить поведение.

Необходимо принудительно выделить tty с помощью флага `-t`:

```bash
ssh -t localhost 'tty'
```

13. Бывает, что есть необходимость переместить запущенный процесс из одной сессии в другую. Попробуйте сделать это, воспользовавшись `reptyr`. Например, так можно перенести в `screen` процесс, который вы запустили по ошибке в обычной SSH-сессии.

Для имитации тяжелого процесса создам 100000 файлов `touch {1..100000}`, затем прерву исполнение командой `Ctrl + Z`. После чего зафиксирую количество созданных файлов. Так же зафиксирую PID нужного мне процесса. Перейду в новую сессию и выполню эту же команду с `reptyr`. 

```bash
vagrant@vagrant:~$ touch {1..100000}
^Z
[3]+  Stopped                 touch {1..100000}
vagrant@vagrant:~$ ls | wc -l
27886
````

```bash
ps -a | grep touch
```

```bash
   1467 pts/0    00:00:00 touch
```

```bash 
screen
touch {1..100000} reptyr 1467
exit
```

```bash
vagrant@vagrant:~$ ls | wc -l
100003
```
100001-й файл - это тестовый файлик file.txt
100002-й файл - это тестовый файлик file_2.txt
100003-й файл — это файл `reptyr`.

Удалим лишние файлы
```bash
 rm {1..100000}
```

Повторное Решение п.13

pts/9
```bash
vagrant@vagrant:~$ tty
/dev/pts/0
vagrant@vagrant:~$ echo 0 > /proc/sys/kernel/yama/ptrace_scope
vagrant@vagrant:~$ top
top - 07:26:07 up 34 min,  2 users,  load average: 0.05, 0.05, 0.07
Tasks: 125 total,   1 running, 124 sleeping,   0 stopped,   0 zombie
%Cpu(s):  1.2 us,  2.4 sy,  0.0 ni, 95.2 id,  0.0 wa,  0.0 hi,  1.2 si,  0.0 st
MiB Mem :   1987.1 total,   1230.9 free,    151.7 used,    604.6 buff/cache
MiB Swap:    980.0 total,    980.0 free,      0.0 used.   1669.1 avail Mem

    PID USER      PR  NI    VIRT    RES    SHR S  %CPU  %MEM     TIME+ COMMAND
    827 netdata   20   0   53276   3584   2008 S   5.0   0.2   0:36.56 apps.plugin
   2558 vagrant   20   0   11852   3668   3160 R   5.0   0.2   0:00.01 top
      1 root      20   0  167368  11456   8500 S   0.0   0.6   0:01.60 systemd
      2 root      20   0       0      0      0 S   0.0   0.0   0:00.00 kthreadd
      3 root       0 -20       0      0      0 I   0.0   0.0   0:00.00 rcu_gp
      4 root       0 -20       0      0      0 I   0.0   0.0   0:00.00 rcu_par_gp
      6 root       0 -20       0      0      0 I   0.0   0.0   0:00.00 kworker/0:0H
      9 root       0 -20       0      0      0 I   0.0   0.0   0:00.00 mm_percpu_wq
     10 root      20   0       0      0      0 S   0.0   0.0   0:00.02 ksoftirqd/0
     11 root      20   0       0      0      0 I   0.0   0.0   0:00.41 rcu_sched
     12 root      rt   0       0      0      0 S   0.0   0.0   0:00.03 migration/0
     13 root     -51   0       0      0      0 S   0.0   0.0   0:00.00 idle_inject/0
     14 root      20   0       0      0      0 S   0.0   0.0   0:00.00 cpuhp/0
     15 root      20   0       0      0      0 S   0.0   0.0   0:00.00 cpuhp/1
     16 root     -51   0       0      0      0 S   0.0   0.0   0:00.00 idle_inject/1
     17 root      rt   0       0      0      0 S   0.0   0.0   0:00.35 migration/1
     18 root      20   0       0      0      0 S   0.0   0.0   0:00.03 ksoftirqd/1
     20 root       0 -20       0      0      0 I   0.0   0.0   0:00.00 kworker/1:0H-kblockd
     21 root      20   0       0      0      0 S   0.0   0.0   0:00.00 cpuhp/2
     22 root     -51   0       0      0      0 S   0.0   0.0   0:00.00 idle_inject/2
     23 root      rt   0       0      0      0 S   0.0   0.0   0:00.36 migration/2
     24 root      20   0       0      0      0 S   0.0   0.0   0:00.03 ksoftirqd/2
     26 root       0 -20       0      0      0 I   0.0   0.0   0:00.00 kworker/2:0H-kblockd
[1]+  Stopped                 top
vagrant@vagrant:~$ ps -a
    PID TTY          TIME CMD
   2558 pts/0    00:00:00 top
   2559 pts/0    00:00:00 ps
vagrant@vagrant:~$
vagrant@vagrant:~$ ps -a
    PID TTY          TIME CMD
   2560 pts/1    00:00:00 screen
   2569 pts/2    00:00:00 reptyr
   2570 pts/0    00:00:00 top <defunct>
   2571 pts/0    00:00:00 ps
vagrant@vagrant:~$ pstree
systemd─┬─VBoxService───8*[{VBoxService}]
        ├─accounts-daemon───2*[{accounts-daemon}]
        ├─agetty
        ├─atd
        ├─cron
        ├─dbus-daemon
        ├─fwupd───4*[{fwupd}]
        ├─irqbalance───{irqbalance}
        ├─multipathd───6*[{multipathd}]
        ├─netdata─┬─apps.plugin
        │         ├─bash
        │         ├─nfacct.plugin
        │         └─20*[{netdata}]
        ├─networkd-dispat
        ├─node_exporter───4*[{node_exporter}]
        ├─polkitd───2*[{polkitd}]
        ├─rpcbind
        ├─rsyslogd───3*[{rsyslogd}]
        ├─screen───bash───reptyr
        ├─sshd───sshd───sshd───bash─┬─pstree
        │                           └─top───top
        ├─systemd───(sd-pam)
        ├─systemd-journal
        ├─systemd-logind
        ├─systemd-network
        ├─systemd-resolve
        └─systemd-udevd

```
pts/1
```bash
vagrant@vagrant:~$ tty
/dev/pts/1
screen
reptyr 2558
---
```

14. `sudo echo string > /root/new_file` не даст выполнить перенаправление под обычным пользователем, так как перенаправлением занимается процесс shell'а, который запущен без `sudo` под вашим пользователем. Для решения данной проблемы можно использовать конструкцию `echo string | sudo tee /root/new_file`. Узнайте что делает команда `tee` и почему в отличие от `sudo echo` команда с `sudo tee` будет работать.


Суть команды `tee` - принимает данные из одного источника и может сохранять их на выходе в нескольких местах + обладает правами для записи в `root`

**СИНТАКСИС КОМАНДЫ TEE**

Синтаксис команды достаточно простой:
```bash
$ tee опции файл

Опции команды:
    -a или -append - Используется для записи вывода в конец существующего файла.
     -i или -ignore-interrupts - Используется, чтобы игнорировать прерывающие сигналы.
    -help - Используется для показа всех возможных операций.
    -version - Используется для показа текущей версии этой команды.

Для сохранения вывода команды можно передать один или несколько файлов.
```
