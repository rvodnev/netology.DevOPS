###1. Найдите полный хеш и комментарий коммита, хеш которого начинается на `aefea`.

Answer: 
```bash
$ git show --pretty=format:"%H %s" aefea
aefead2207ef7e2aa5dc81a34aedf0cad4c32545 Update CHANGELOG.md
```

###2. Какому тегу соответствует коммит `85024d3`?
Answer:
```bash
$ git show 85024d3
commit 85024d3100126de36331c6982bfaac02cdab9e76 (tag: v0.12.23)
```
###3.Сколько родителей у коммита `b8d720`? Напишите их хеши.
Answer: **2шт**
```bash
$ git cat-file -p b8d720
parent 56cd7859e05c36c06b56d013b55a252d0bb7e158
parent 9ea88f22fc6269854151c571162c5bcf958bee2b
```
###4.Перечислите хеши и комментарии всех коммитов которые были сделаны между тегами  v0.12.23 и v0.12.24.
Answer: **10 штук**
```bash
$ git log v0.12.23..v0.12.24 --oneline
33ff1c03b (tag: v0.12.24) v0.12.24
b14b74c49 [Website] vmc provider links
3f235065b Update CHANGELOG.md
6ae64e247 registry: Fix panic when server is unreachable
5c619ca1b website: Remove links to the getting started guide's old location
06275647e Update CHANGELOG.md
d5f9411f5 command: Fix bug when using terraform login on Windows
4b6d06cc5 Update CHANGELOG.md
dd01a3507 Update CHANGELOG.md
225466bc3 Cleanup after v0.12.23 release
```
###5. Найдите коммит в котором была создана функция `func providerSource`, ее определение в коде выглядит
так `func providerSource(...)` (вместо троеточего перечислены аргументы).

Answer: 
```bash
$ git log -S 'func providerSource(' --oneline
8c928e835 main: Consult local directories as potential mirrors of providers

$ git show 8c928e835
**commit 8c928e83589d90a031f811fae52a81be7153e82f**
Author: Martin Atkins <mart@degeneration.co.uk>
Date:   Thu Apr 2 18:04:39 2020 -0700
```
###6.Найдите все коммиты в которых была изменена функция `globalPluginDirs`.

Answer:
```bash
$ git grep -n 'func globalPluginDirs('
plugins.go:18:func globalPluginDirs() []string {
  
$ git log --no-patch --oneline -L :globalPluginDirs:plugins.go
78b122055 Remove config.go and update things using its aliases
52dbf9483 keep .terraform.d/plugins for discovery
41ab0aef7 Add missing OS_ARCH dir to global plugin paths
66ebff90c move some more plugin search path logic to command
8364383c3 Push plugin discovery down into command package
}
```
###7. Кто автор функции `synchronizedWriters`? 
Answer: Автор функции `synchronizedWriters()` — Martin Atkins
```bash
$ git log -S 'func synchronizedWriters(' --pretty=format:"%h %an %ad %s"
bdfea50cc James Bardin Mon Nov 30 18:02:04 2020 -0500 remove unused
5ac311e2a Martin Atkins Wed May 3 16:25:41 2017 -0700 main: synchronize writes to VT100-faker on Windows
```