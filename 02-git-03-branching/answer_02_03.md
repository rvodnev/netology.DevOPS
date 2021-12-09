##**Начальная подготовка**
>**Создайте в своем репозитории каталог `branching` и в нем два файла `merge.sh` и `rebase.sh` с 
>содержимым:**
---
```bash
#!/bin/bash
# display command line options

count=1
for param in "$*"; do
    echo "\$* Parameter #$count = $param"
    count=$(( $count + 1 ))
done
```
---
Проводим стартовые подготовки.
```bash 
$ mkdir branching           #Создали каталог
$ cd branching              #Перешли в созданный каталог branching 
$ touch branching\merge.sh  #Создали файл
$ touch branching\rebase.sh #Создали файл
$ nano merge.sh             #Открываем файл в редакторе nano и вносим изменения
$ nano rebase.sh            #Открываем файл в редакторе nano и вносим изменения
$ cd ..\\                   #Вернулись на каталог назад
```

>**Создадим коммит с описанием `prepare for merge and rebase` и отправим его в ветку main.** 

```bash 
$ git commit -m "prepare for merge and rebase"     #Создали стартовый коммит
```

##**Подготовка файла `merge.sh`**

>Создайте ветку git-merge.

```bash
$ git checkout -b git-merge   #Создали ветку git-merge
```

>Замените в ней содержимое файла merge.sh

```bash
$ nano branching/merge.sh  #Меняем содержимое

########меняем содержимое на#########

#!/bin/bash
# display command line options

count=1
for param in "$@"; do
    echo "\$@ Parameter #$count = $param"
    count=$(( $count + 1 ))
done
#####################################
```

>Создайте коммит merge: @ instead * отправьте изменения в репозиторий.

```bash
$ git commit -am "merge: @ instead *"        #Создали коммит и одновременно добавили в гит "2 в 1" (git add и git commit)
$ git push --set-upstream origin git-merge   #Отправили в репозиторий
```
>И разработчик подумал и решил внести еще одно изменение в merge.sh

```bash
$ nano branching/merge.sh #Вносим изменения

########меняем содержимое на#########
#!/bin/bash
# display command line options

count=1
while [[ -n "$1" ]]; do
    echo "Parameter #$count = $1"
    count=$(( $count + 1 ))
    shift
done
#####################################
```
>Создайте коммит merge: use shift и отправьте изменения в репозиторий.

```bash
$ git commit -am "merge: use shift" #Создали коммит и одновременно добавили в гит "2 в 1" (git add и git commit)
$ git push                          #Отправили в репозиторий
```

##**Изменим ~~main~~ `master`**

>Вернитесь в ветку ~~main~~ master.

```bash
$ git checkout master      #Идем в ветку master
```
>Предположим, что кто-то, пока мы работали над веткой git-merge, изменил main.
>Для этого изменим содержимое файла rebase.sh на следующее
---
```bash
#!/bin/bash
# display command line options

count=1
for param in "$@"; do
    echo "\$@ Parameter #$count = $param"
    count=$(( $count + 1 ))
done

echo "====="
```
---
Начинаем работу
```bash
$ nano branching/rebase.sh  #открываем файл в редакторе nano и вносим изменения.
```

> Отправляем измененную ветку ~~main~~ master в репозиторий.
```bash
$ git commit -am "change rebase.sh"  #Создали коммит и одновременно добавили в гит "2 в 1" (git add и git commit)
$ git push                           #Отправили в репозиторий
```

##**Подготовка файла `rebase.sh`**

>Предположим, что теперь другой участник нашей команды не сделал `git pull`, либо просто хотел ответвиться не от 
>последнего коммита в ~~main~~ master, а от коммита когда мы только создали два файла `merge.sh` и `rebase.sh` на первом шаге.  

**Создадим ветку `git-rebase` основываясь на текущем коммите.**

---
>~~Для этого при помощи команды `git log` найдем хэш коммита `prepare for merge and rebase` 
>и выполним `git checkout` на него примерно так:
>`git checkout 8baf217e80ef17ff577883fda90f6487f67bbcea` (хэш будет другой).~~

---
~~Херня~~, можно проще - создадим ветку `git-rebase` основываясь на хеше коммита.

```bash
$ git log  #Смотрим лог
...
commit 7010b213a6a162f5970b9461b79d36989005a84b  #<<== это хэш
Author: Aleksandr Kirichenko <reysonk@gmail.com>
Date:   Thu Dec 9 03:47:22 2021 +0700

    prepare for merge and rebase
...
```
```bash
$ git branch git-rebase 7010b213  #Cоздадим ветку git-rebase основываясь на хеше коммита.
```

```bash
$ git checkout git-rebase #Переходим в ветку git-rebase
```

>Изменим содержимое файла `rebase.sh` на следующее, тоже починив скрипт, 
>но немного в другом стиле
---
```bash
#!/bin/bash
# display command line options

count=1
for param in "$@"; do
    echo "Parameter: $param"
    count=$(( $count + 1 ))
done

echo "====="
```
---
```bash
$ nano branching/rebase.sh        #Открываем файл в редакторе nano и вносим изменения
```

>**Отправим эти изменения в ветку `git-rebase`, с комментарием git-rebase 1.**

```bash
$ git commit -am "git-rebase 1"  #Создали коммит и одновременно добавили в гит "2 в 1" (git add и git commit)
$ git push --set-upstream origin git-rebase #Отправили в репозиторий
```

>И сделаем еще один коммит `git-rebase 2` с пушем заменив echo `"Parameter: $param"` на `echo "Next parameter: $param"`.

Вносим изменения и пушим

```bash
$ nano branching/rebase.sh      #Открываем файл в редакторе nano и вносим изменения
```

```bash
$ git commit -am "git-rebase 2" #Создали коммит и одновременно добавили в гит "2 в 1" (git add и git commit)
$ git push                      #Отправили в репозиторий
```

`ПРОМЕЖУТОЧНЫЙ ИТОГ`


![](https://github.com/reysonk/netology.DevOPS/blob/master/02-git-03-branching/img/pr_itog_1.png)


##Merge

>Сливаем ветку `git-merge` в main и отправляем изменения в репозиторий, должно получиться без конфликтов:

```bash
$ git checkout master  #Переключаемся в ветку мастер
$ git merge git-merge  #Мержим git-merge в main
Merge made by the 'ort' strategy.
 bash.md            | 8 ++++++++
 bash.txt           | 0
 branching/merge.sh | 7 ++++---
 3 files changed, 12 insertions(+), 3 deletions(-)
 create mode 100644 bash.md
 delete mode 100644 bash.txt
 $ git push            #Отправили в репозиторий
```  
`ПРОМЕЖУТОЧНЫЙ ИТОГ 2`


![](https://github.com/reysonk/netology.DevOPS/blob/master/02-git-03-branching/img/pr_itog_2.png)


##Rebase

>А перед мержем ветки `git-rebase` выполним ее `rebase` на main. Да, мы специально создали
>ситуацию с конфликтами, чтобы потренироваться их решать. 

```bash
$ git checkout git-rebase    #Переключаемся на ветку git-rebase
$ git rebase -i master       #Выполняем rebase в main
Auto-merging branching/rebase.sh
CONFLICT (content): Merge conflict in branching/rebase.sh
error: could not apply 40cab95... git-rebase 1
hint: Resolve all conflicts manually, mark them as resolved with
hint: "git add/rm <conflicted_files>", then run "git rebase --continue".
hint: You can instead skip this commit: run "git rebase --skip".
hint: To abort and get back to the state before "git rebase", run "git rebase --abort".
Could not apply 40cab95... git-rebase 1

```  
`МЕНЯЕМ fixup`


![](https://github.com/reysonk/netology.DevOPS/blob/master/02-git-03-branching/img/fixup.png)

```bash
$ nano branching/rebase.sh   #Идем смотреть rebase.sh
```  
`Вносим правки`


![](https://github.com/reysonk/netology.DevOPS/blob/master/02-git-03-branching/img/rebase.1.png)

>Cообщим гиту, что конфликт решен и продолжим ребейз.

```bash
$ git add branching/rebase.sh  #Добавляем изменения в git
$ git rebase --continue        #Продложаем Ребейз
#В результате Ничего не дописываем сохраняем как есть результат
```  
>И опять в получим конфликт в файле rebase.sh при попытке применения нашего второго коммита. Давайте разрешим конфликт, оставив строчку echo "Next parameter: $param".


```bash
$ nano branching/rebase.sh #Идем смотреть rebase.sh
```

`Вносим правки`


![](https://github.com/reysonk/netology.DevOPS/blob/master/02-git-03-branching/img/rebase.2.png)

>Далее опять сообщаем гиту о том, что конфликт разрешен и продолжим ребейз.
```bash
$ git add branching/rebase.sh   #Добавляем изменения в git
$ git rebase --continue         #Продложаем Ребейз
[detached HEAD f7ae233] git-rebase 1
 Date: Thu Dec 9 05:28:47 2021 +0700
 1 file changed, 2 insertions(+), 1 deletion(-)
Successfully rebased and updated refs/heads/git-rebase. #УСПЕХ
```  

>И попробуем выполнить `git push`, либо `git push -u origin git-rebase` чтобы точно указать что и куда мы хотим запушить. 

```bash
$ git push    #Отправили в репозиторий
To https://github.com/reysonk/HM_3.git
 ! [rejected]        git-rebase -> git-rebase (non-fast-forward)
error: failed to push some refs to 'https://github.com/reysonk/HM_3.git'    #ВОЗНИКЛА ОШИБКА
hint: Updates were rejected because the tip of your current branch is behind
hint: its remote counterpart. Integrate the remote changes (e.g.
hint: 'git pull ...') before pushing again.
hint: See the 'Note about fast-forwards' in 'git push --help' for details.
```  

Форсанем изменения в мастер
```bash
$ git push -u origin git-rebase -f   #Отправили в репозиторий с параметром  -f, --force (force updates)
Enumerating objects: 7, done.
Counting objects: 100% (7/7), done.
Delta compression using up to 8 threads
Compressing objects: 100% (4/4), done.
Writing objects: 100% (4/4), 401 bytes | 401.00 KiB/s, done.
Total 4 (delta 2), reused 0 (delta 0), pack-reused 0
remote: Resolving deltas: 100% (2/2), completed with 2 local objects.
To https://github.com/reysonk/HM_3.git
 + b465a74...f7ae233 git-rebase -> git-rebase (forced update)
Branch 'git-rebase' set up to track remote branch 'git-rebase' from 'origin'.
```  

>Теперь можно смержить ветку `git-rebase` в main без конфликтов и без дополнительного мерж-комита простой перемоткой.

```bash
$ git checkout master  #Переключаемся на ветку master
Switched to branch 'master'
Your branch is up to date with 'origin/master'.

$ git merge git-rebase  #Делаем ребейз
Updating 42057bc..f7ae233
Fast-forward            #Обратите внимание просто переносится HEAD
 branching/rebase.sh | 3 ++-
 1 file changed, 2 insertions(+), 1 deletion(-)

$ git push  #Отправили в репозиторий
Total 0 (delta 0), reused 0 (delta 0), pack-reused 0
To https://github.com/reysonk/HM_3.git
   42057bc..f7ae233  master -> master  

```  
`ГОТОВО, поставленная задача выполнена`


![](https://github.com/reysonk/netology.DevOPS/blob/master/02-git-03-branching/img/finall.png)


Посмотреть работу можно https://github.com/reysonk/HM_3/network

**Автор:** Александр `Reyson`Кириченко
