### Задание №1 – Знакомимся с gitlab и bitbucket 

Добавлен репозиторий gitlub в bitbucket
```bash
$ git remote -v

bitbucket https://Reysonk@bitbucket.org/reysonk/netology.devops.git (fetch)
bitbucket https://Reysonk@bitbucket.org/reysonk/netology.devops.git (push)
gitlab  https://gitlab.com/reysonk/netology.devops.git (fetch)
gitlab  https://gitlab.com/reysonk/netology.devops.git (push)
origin  https://github.com/reysonk/netology.DevOPS.git (fetch)
origin  https://github.com/reysonk/netology.DevOPS.git (push)
```

### Задание №2 – Теги

1. Создайте легковестный тег v0.0 на HEAD коммите и запуште его во все три добавленных на предыдущем этапе upstream.

```bash
git tag v0.0 ec93909
git push origin --tags
git push gitlab --tags
git push bitbucket --tags
```

2. Аналогично создайте аннотированный тег v0.1.

```bash
git tag -a v0.1 ec93909 -m "v0.1"
```

### Задание №3 – Ветки

1.  Переключитесь обратно на ветку ~~main~~`master`, которая должна быть связана с веткой ~~main~~`master` репозитория на `github`.

```bash
git checkout origin/master
```

2. Посмотрите лог коммитов и найдите хеш коммита с названием `Prepare to delete and move`, который был создан в пределах предыдущего домашнего задания.

```bash
git log --oneline

1c1c1a8 Prepare to delete and move
```

3. Выполните `git checkout` по хешу найденного коммита.

```bash
git checkout 1c1c1a8
```

4. Создайте новую ветку `fix` базируясь на этом коммите `git switch -c fix`.

```bash
git switch -c fix
```

5. Отправьте новую ветку в репозиторий на гитхабе `git push -u origin fix`.

```bash
git push -u origin fix
```
### Задание №4 – Упрощаем себе жизнь

Успешно, действительно удобнее, но консоль помогает понять смысл операций.

###Итог

В результате домашней работы были получены знания про добавление и управление внешними репозиториями тегов и создание веток.