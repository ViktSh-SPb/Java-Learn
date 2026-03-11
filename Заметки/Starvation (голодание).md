# Starvation (голодание)
Starvation - это ситуация в многопоточном программировании, когда один или несколько потоков не могут получить доступ к общим ресурсам или не могут выполнить работу из-за того, что другие потоки постоянно монополизируют эти ресурсы.
## Ключевые характеристики Starvation
- Поток жив и не заблокирован
- Поток хочет работать, но не может
- Нет прогресса в выполнении работы
- Ресурсы несправедливо распределяются
## Причины возникновения Starvation
- Высокоприоритетные потоки монополизируют ресурсы
- Неправильная настройка планировщика
- "Жадные" потоки не отпускают ресурсы
- Неправильная политика синхронизации
## Примеры Starvation
### 1. Низкоприоритетный поток голодает
```java
public class PriorityStarvation {  
  
    public static void main(String[] args) {  
        Thread highPriorityThread = new Thread(() -> {  
            while (true) {  
                // Монополизирует CPU  
                System.out.println("High priority working...");  
            }  
        });  
          
        Thread lowPriorityThread = new Thread(() -> {  
            while (true) {  
                // Этот поток почти никогда не выполняется  
                System.out.println("Low priority got chance!");  
            }  
        });  
          
        highPriorityThread.setPriority(Thread.MAX_PRIORITY);  
        lowPriorityThread.setPriority(Thread.MIN_PRIORITY);  
          
        highPriorityThread.start();  
        lowPriorityThread.start();  
    }  
}
```