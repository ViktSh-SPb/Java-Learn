# wait()
**wait()** - приостанавливает текущий поток до тех пор, пока другой поток не вызовет **notify()** или **notifyAll()** для этого объекта.
### Особенности
1. Должен вызываться в синхронизированном блоке (synchronized)
2. Освобождает монитор объекта на время ожидания
3. Имеет три варианта:
```java
wait(); // ждет бесконечно
wait(long timeout); // ждет указанное время (мс)
wait(timeout, nanos); // точное время (мс + наносекунды)
```
### Пример
```java
synchronized (lock) {
	while (!condition){
		lock.wait(); // Поток ждет
	}
}
```
# notify()
**notify()** - пробуждает один случайный поток, ожидающий на мониторе этого объекта.
### Особенности
1. Требует синхронизированного контекста
2. Не гарантирует, какой именно поток будет разбужен
### Пример
```java
synchronized (lock){
	condition = true;
	lock.notify(); // Пробуждает 1 поток
}
```
# notifyAll()
**notifyAll()** - пробуждает все потоки, ожидающие монитора этого объекта.
### Особенности
1. Потоки поочередно переходят в состояние RUNNABLE и борются за монитор.
2. Полезен, когда несколько потоков ждут разных условий.
### Пример
```java
synchronized (lock){
	condition = true;
	lock.notifyAll(); // Пробуждает все потоки
}
```
## Ключевые отличия
<table>
    <thead>
        <tr>
            <th>Метод</th>
            <th>Потоки</th>
            <th>Когда использовать</th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td>wait()</td>
            <td >-</td>
            <td >Когда поток должен ждать условия</td>
        </tr>
        <tr>
            <td >notify()</td>
            <td >1</td>
            <td >Когда нужно разбудить один поток (оптимизация)</td>
        </tr>
        <tr>
            <td >notifyAll()</td>
            <td >Все</td>
            <td >Когда условие изменилось для всех потоков</td>
        </tr>
</table>
## Важные правила
1. wait() всегда необходимо вызывать в цикле. Из-за ложных пробуждений (spurious wakeups) проверка условия должна быть в цикле:
```java
synchronized (lock){
	while (!condition){
		lock.wait();
	}
}
```
2. Необходимо использовать один и тот же объект для wait/notify. Монитор должен быть общим для всех взаимодействующих потоков.
3. notifyAll() безопаснее. notify() может привести к "зависанию" потоков, если разбудит не тот, который ждет нужного условия.
## Пример: Producer-Consumer
```java
class Buffer{
	private Queue<Integer> queue = new LinkedList<>();
	private int capacity = 10;

	public synchronized void produce(int item) throws InterruptedException {
		while (queue.size() == capacity){
			wait(); // Ждет, если очередь заполнена
		}
		queue.add(item);
		notifyAll(); // Уведомляет потребителей
	}

	public synchronized int consume() throws InterruptedException {
		while (queue.isEmpty()){
			wait(); // Ждет, если очередь пуста
		}
		int item = queue.remove();
		notifyAll(); // Уведомляет производителей
		return item;
	}
}
```
## Когда использовать?
wait()/notify() - низкоуровневая синхронизация (редко нужна в современной Java)
Альтернативы:
- java.util.concurrent (например, BlockingQueue, CountDownLatch)
- Lock и Condition из java.util.concurrent.locks
Методы wait/notify - это основа многопоточности в Java, но в реальных проектах чаще используют высокоуровневые инструменты.