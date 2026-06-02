# HashMap
**HashMap** - это структура данных для хранения пар: **ключ → значение**.
**HashMap** позволяет очень быстро <u>добавлять / искать / удалять</u> элементы по ключу. Средняя сложность **О(1)**
Пример:
```java
Map<String, Integer> map = new HashMap<>();

map.put("Alice", 25);
map.put("Bob", 30);
```
## Устройство HashMap
```mermaid
graph LR
    subgraph Keys["Keys"]
        K1["John Smith"]
        K2["Lisa Smith"]
        K3["Sam Doe"]
        K4["Sandra Dee"]
        K5["Ted Baker"]
    end
    subgraph Buckets[" "]
        B1["bucket0"]
        B2["bucket1"]
        B3["bucket2"]
        B4["bucket3"]
        B5["bucket4"]
        B6["bucket5"]
    end
    subgraph Entries[" "]
	    subgraph E1
	    direction LR
		    E1K["John Smith"]  
		    E1V["521-1234"]  
		end
		subgraph E2  
		direction LR  
		    E2K["Lisa Smith"]  
		    E2V["521-8976"]  
		end
		subgraph E3  
		direction LR  
		    E3K["Sam Doe"]  
		    E3V["521-5030"]  
		end
		subgraph E4  
		direction LR  
		    E4K["Sandra Dee"]  
		    E4V["521-9655"]  
		end
		subgraph E5  
		direction LR  
		    E5K["Lisa Smith"]  
		    E5V["521-8976"]  
		end
    end
    K1 -->|hash| B2
    K2 -->|hash| B1
    K3 -->|hash| B5
    K4 -->|hash| B2
    K5 -->|hash| B4
    B1 --> E2K
    B2 --> E1K
    E1K --> E4K
    B4 --> E5K
    B5 --> E3K
    classDef keysStyle stroke:#818cf8,fill:#eef2ff
    classDef bucketsStyle stroke:#2dd4bf,fill:#f0fdfa
    classDef entriesStyle stroke:#a78bfa,fill:#f5f3ff
    class K1,K2,K3,K4,K5 keysStyle
    class B1,B2,B3,B4,B5,B6 bucketsStyle
    class E1,E2,E3,E4,E5 entriesStyle
```