# Status
Git status - одна из самых часто используемых команд в Git. Она показывает текущее состояние рабочей директории и индексированных файлов, помогая понять:
- Какие файлы изменены.
- Какие файлы добавлены в staging (индекс).
- Какие файлы не отслеживаются Git.
- Состояние ветки (есть ли расхождения с удаленным репозиторием).
## Базовое использование
Просто выполните:
```bash
git status
```
Пример вывода:
```
On branch main
Your branch is up to date with 'origin/main'.

Changes to be commited:
	(use "git restore --staged <file>..." to unstage)
		modified: README.md

Changes not staged for commit:
	(use "git add <file>..." to update what will be committed)
	(use "git restore <file>..." to discard changes in working directory)
		modified: script.js

Untracked files:
	(use "git add <file>..." to include in what will be committed)
		new-file.txt
```
## Статусы файлов
### 1. Изменения, готовые к коммиту (Changes to be committed)
Файлы, добавленные в staging area (индекс) командой git add:
```bash
git add file.txt     # файл теперь в staged-состоянии
git status           # покажет "Changes to be committed"
```
Как убрать из staging:
```bash
git restore --staged file.txt
```
### 2. Изменения, не подготовленные к коммиту (Changes not staged for commit)
Файлы, которые:
- Были изменены, но не добавлены в staging.
- Уже отслеживаются Git (ранее добавлены в репозиторий).
Как добавить в staging:
```bash
git add file.txt
```
Как откатить изменения:
```bash
git restore file.txt    # отмена локальных изменений (опасно!)
```
### 3. Неотслеживаемые файлы (Untracked files)
Файлы, которые Git никогда не видел (новые файлы).
```bash
git add new-file.txt   # начать отслеживать
```
Как игнорировать файлы:
- Добавьте их в .gitignore:
```bash
echo "temp.log" >> .gitignore
```
## Полезные флаги git status

| Команда              | Описание                           |
| -------------------- | ---------------------------------- |
| git status -s        | Короткий вывод (компактный формат) |
| git status --ignored | Показать игнорируемые файлы        |
| git status -v        | Подробный вывод (c diff)           |
Пример git status -s:
```bash
 M script.js     # modified, not staged
A  README.md     # added to staging
?? new-file.txt  # untracked
```
Обозначения:
- (пробел) - нет изменений
- M - изменен (А - добавлен, D - удален)
- ?? - неотслеживаемый файл