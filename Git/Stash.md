# Stash
**git stash** - позволяет отложить текущие незавершенные изменения (включая изменения в отслеживаемых файлах и staged-файлах), чтобы переключиться на другую задачу или ветку без создания коммита.
## Основные команды
### 1. Сохранить изменения в stash
```bash
git stash
# или с комментарием:
git stash push -m "Описание изменений"
```
### 2. Просмотреть список сохраненных stash'ей
```bash
git stash list
```
Вывод:
```bash
stash@{0}: On main: Название stash\'a
stash@{1}: WIP on feature: a1b2c3d...
```
### 3. Вернуть последние отложенные изменения
```bash
git stash pop     # применить и УДАЛИТЬ stash из списка
```
или:
```bash
git stash apply   # применить, но ОСТАВИТЬ в списке
```
### 4. Вернуть конкретный stash

```bash
git stash apply stash@{1}   # где 1 - номер stash'a
```
### 5. Удалить stash
```bash
git stash drop stash@{0}    # удалить конкретный stash
git stash clear             # удалить ВСЕ stash'ы
```