# 1. FROM
Определяет базовый образ для сборки.
 **⚠️** **Важно:**
- **Должна быть первой инструкцией** (кроме комментариев и ARG)
- **Лучше указывать конкретную версию** (python:3.9, а не python.lastest)
```dockerfile
# Официальный образ Ubuntu:
FROM ubuntu:22.04
# Образ Python с минимальными зависимостями:
FROM python:3.9-slim
# Минималистичный образ (~5мб)
FROM alpine:lastest
```
# 2. RUN
Выполняет команды внутри контейнера при сборке.
```dockerfile
# Установка curl в Ubuntu:
RUN apt-get update && apt-get install -y curl
# Установка Python-пакета:
RUN pip install flask
```
## Оптимизация:
- Объединяйте команды через **&&** и **\\** для уменьшения количества слоев.
- Чистите кэш (`apt-get clean, rm -rf /var/lib/apt/lists/\*`).
# 3. COPY и ADD
Копируют файлы из хоста в контейнер.
### COPY (рекомендуется)
```dockerfile
# Копирует файл app.py в /app/
COPY .app.py /app/
# Копирует requirements.txt в /tmp/
COPY requirements.txt /tmp/
```
### ADD (редко используется)
Поддерживает распаковку архивов (*\*.tar, \*.gz*) и загрузку по URL.
```dockerfile
# Скачивает и распаковывает:
ADD https://example.com/file.tar.gz /tmp/
# Распаковывает локальный архив:
ADD data.tar.gz /data/
```
### Разница:
- **COPY** - только копирование
- **ADD** - дополнительные возможности, но менее предсказуем
# 4. WORKDIR
Устанавливает рабочую директорию для последующих команд (**RUN, CMD, COPY**) (аналог *cd* в Linux)
```dockerfile
# Переходит в /app:
WORKDIR /app
# Выведет /app
RUN pwd
```
# 5. ENV
Устанавливает переменные окружения.
```dockerfile
ENV PYTHONPATH=/app
ENV FLASK_APP=app.py
```
Использование:
```dockerfile
RUN echo $FLASK_APP
# Доступны в сборке и в запущенном контейнере
```
# 6. EXPOSE
Документирует порт, который контейнер будет слушать.
```dockerfile
# Контейнер слушает порты 80 и 443
EXPOSE 80
EXPOSE 443
```
**⚠️** **Важно:**
- Не открывает порт автоматически! Для этого нужно использовать -p в docker run.
# 7. CMD и ENTRYPOINT
Определяют команду, которая запускается при старте контейнера.
### CMD
```dockerfile
# Запускает app.py (можно переопределить)
CMD ["python", "app.py"]
CMD ["flask", "run", "--host=0.0.0.0"]
```
### ENTRYPOINT
```dockerfile
# Базовый процесс (переопределяется сложнее)
ENTRYPOINT ["python", "app.py"]
```
### Разница:
- **CMD** - аргументы можно переопределить (`docker run my-image flask run`).
- **ENTRYPOINT** - жестко фиксирует команду.
### Комбинирование:
```dockerfile
# Можно переопределить: docker run my-image server.py
ENTRYPOINT ["python"]
CMD ["app.py"]
```
# 8. VOLUME
Создает точку монтирования для внешних данных.
```dockerfile
# Данные в /data сохраняются вне контейнера
VOLUME /data
```
Используется для БД, кэша и т.д.
# Лучшие практики
1. Минимизируйте количество слоев: объединяйте RUN-команды.
2. Используйте *.dockerignore*: исключайте ненужные файлы (*.git, \_\_pycache\_\_*).
3. Выбирайте легковесные образы (*alpine, -slim*).
4. Не запускайте процессы от root:
```dockerfile
RUN useradd -m appuser && chown -R appuser/app
USER appuser
```