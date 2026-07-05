# Spring
### Задача 1
#### Условие:
Какие проблемы есть в коде? Почему в processInvoice() будет запущено 0 транзакций?
```java
@Entity  
public class Invoice {  
  
    @Id  
    @GeneratedValue    private Long id;  
  
    private String number;  
  
    @OneToMany(mappedBy = "invoice", fetch = FetchType.LAZY, cascade = CascadeType.ALL)  
    private List<InvoiceItem> items = new ArrayList<>();  
    // getters setters  
}  
  
@Entity  
public class InvoiceItem {  
  
    @Id  
    @GeneratedValue    private Long id;  
  
    private String productName;  
    private BigDecimal price;  
    @ManyToOne  
    @JoinColumn(name = "invoice_id")  
    private Invoice invoice;  
    // getters setters  
}  
  
@Service  
@RequiredArgsConstructor  
public class InvoiceService {  
  
    private final InvoiceRepository invoiceRepository;  
    private final EmailService emailService;  
    private final KafkaTemplate<String, String> kafkaTemplate;  
  
    @Transactional  
    public Invoice saveInvoiceAndNotify(Invoice invoice) {  
        Invoice saved = invoiceRepository.save(invoice);  
        System.out.println("Invoice saved with id: " + saved.getId());  
        emailService.saveInvoiceNotification(saved);  
        try {  
            kafkaTemplate.send("invoice-created-topic",  
                "Invoice created with id: " + saved.getId());  
        } catch (Exception ex) {  
            throw new RuntimeException("failed", ex);  
        }  
        return saved;  
    }  
  
    public void processInvoice(Long invoiceId) {  
        Invoice invoice = invoiceRepository.findById(invoiceId)  
            .orElseThrow(() -> new RuntimeException("Invoice not found"));  
        updateInvoiceNumber(invoice);  
        finalizeInvoice(invoice);  
    }  
  
    @Transactional  
    public void updateInvoiceNumber(Invoice invoice) {  
        //..  
    }  
  
    @Transactional  
    public void finalizeInvoice(Invoice invoice) {  
        //..  
    }   
}

@Repository  
public interface InvoiceRepository extends JpaRepository<Invoice, Long> {} 
```
#### Решение:
```java
code
```
### Задача 2
#### Условие:
Условие
```java
code
```
#### Решение:
```java
code
```
### Задача 3
#### Условие:
Условие
```java
code
```
#### Решение:
```java
code
```
### Задача 4
#### Условие:
Условие
```java
code
```
#### Решение:
```java
code
```
### Задача 5
#### Условие:
Условие
```java
code
```
#### Решение:
```java
code
```
### Задача 6
#### Условие:
Условие
```java
code
```
#### Решение:
```java
code
```