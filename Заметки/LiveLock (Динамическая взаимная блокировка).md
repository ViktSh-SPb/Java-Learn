# LiveLock (Динамическая взаимная блокировка)
Livelock - это ситуация в многопоточном программировании, когда потоки не заблокированы, но не могут прогрессировать, потому что постоянно реагируют на действия друг друга, не выполняя полезной работы.
## Ключевые характеристики LiveLock
- Потоки активны и не заблокированы
- Потоки реагируют на изменения состояния
- Нет прогресса в выполнении работы
- Напоминает бесконечный цикл вежливости
## Классическая аналогия
Два человека встречаются в узком корридоре:
- Один двигается влево, другой вправо - все еще блокировка
- Оба двигаются в одну сторону - снова блокировка
- Процесс повторяется бесконечно
## Пример LiveLock
```java
public class LiveLockExample {  
  
    static class Spoon {  
  
        private Dinner owner;  
  
        public Spoon(Dinner d) {  
            owner = d;  
        }  
  
        public synchronized void setOwner(Dinner d) {  
            owner = d;  
        }  
  
        public synchronized Dinner getOwner() {  
            return owner;  
        }  
  
        public synchronized void use() {  
            System.out.println(owner.name + " is eating!");  
        }  
    }  
  
    static class Dinner {  
  
        private String name;  
        private boolean isHungry;  
  
        public Dinner(String n) {  
            name = n;  
            isHungry = true;  
        }  
  
        public void eatWith(Spoon spoon, Dinner spouce) {  
            while (isHungry && !Thread.currentThread().isInterrupted()) {  
                // Если ложка не у меня, жду  
                if (spoon.getOwner() != this) {  
                    try {  
                        Thread.sleep(1);  
                    } catch (InterruptedException e) {  
                        break;  
                    }  
                }  
  
                // Если супруг голоден, отдаю ложку  
                if (spouce.isHungry) {  
                    System.out.println(name + ": You eat first, " + spouce.name);  
                    spoon.setOwner(spouce);  
                    continue;  
                }  
  
                // Ем  
                spoon.use();  
                isHungry = false;  
                System.out.println(name + "I'm done, " + spouce.name);  
                spoon.setOwner(spouce);  
            }  
        }  
    }  
  
    public static void main(String[] args) {  
        final Dinner husband = new Dinner("Husband");  
        final Dinner wife = new Dinner("Wife");  
        final Spoon spoon = new Spoon(husband);  
  
        Thread husbandThread = new Thread(() -> {  
           husband.eatWith(spoon, wife);  
        });  
  
        Thread wifeThread = new Thread(() -> {  
            wife.eatWith(spoon, husband);  
        });  
  
        husbandThread.start();  
        wifeThread.start();  
  
        // Через время остановим, чтобы не бежало вечно  
        try {  
            Thread.sleep(5000);  
            husbandThread.interrupt();  
            wifeThread.interrupt();  
            System.out.println("LiveLock detected! Thread interrupted.");  
        } catch (InterruptedException e) {  
            e.printStackTrace();  
        }  
    }  
}
```
## Разница между DeadLock и LiveLock

| Аспект            | DeadLock          | LiveLock                     |
| ----------------- | ----------------- | ---------------------------- |
| Состояние потоков | Заблокированы     | Активны, но не прогрессируют |
| Использование CPU | Нет               | Да (100% загрузка)           |
| Поведение         | Ничего не делают  | Бесполезная работа           |
| Причина           | Ожидание ресурсов | Чрезмерная "вежливость"      |
## Реальные сценарии LiveLock
### Сетевые протоколы
