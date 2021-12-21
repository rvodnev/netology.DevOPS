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

```bash
vagrant@vagrant:~$ tty
/dev/pts/0
vagrant@vagrant:~$ top
top - 13:18:15 up 1 min,  1 user,  load average: 0.32, 0.14, 0.05
Tasks: 130 total,   1 running, 129 sleeping,   0 stopped,   0 zombie
%Cpu(s):  0.0 us,  1.5 sy,  0.0 ni, 98.5 id,  0.0 wa,  0.0 hi,  0.0 si,  0.0 st
MiB Mem :   1987.1 total,   1381.4 free,    129.8 used,    475.9 buff/cache
MiB Swap:    980.0 total,    980.0 free,      0.0 used.   1697.8 avail Mem
..................
[1]+  Stopped                 top
vagrant@vagrant:~$ ps -a
    PID TTY          TIME CMD
   1227 pts/0    00:00:00 top
   1228 pts/0    00:00:00 ps
   
vagrant@vagrant:~$ screen -S 1227
```
in screen
```bash
vagrant@vagrant:~$ reptyr 1227
Unable to attach to pid 1227: Operation not permitted
The kernel denied permission while attaching. If your uid matches
the target's, check the value of /proc/sys/kernel/yama/ptrace_scope.
For more information, see /etc/sysctl.d/10-ptrace.conf

vagrant@vagrant:~$ sudo reptyr 1227
Unable to attach to pid 1227: Operation not permitted
The kernel denied permission while attaching. If your uid matches
the target's, check the value of /proc/sys/kernel/yama/ptrace_scope.
For more information, see /etc/sysctl.d/10-ptrace.conf
vagrant@vagrant:~$ exit
```  
Правим права
```bash
vagrant@vagrant:~$ sudo nano /proc/sys/kernel/yama/ptrace_scope
```
Возвращаемся
```bash
vagrant@vagrant:~$ screen -S 1227
```
in screen 
```bash
agrant@vagrant:~$ reptyr 1227
ctrl+a и d
```
Результат
```bash
[detached from 1261.1227]
vagrant@vagrant:~$ ps -a
    PID TTY          TIME CMD
   1269 pts/1    00:00:00 reptyr
   1270 pts/0    00:00:00 top <defunct>
   1276 pts/0    00:00:00 ps
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
