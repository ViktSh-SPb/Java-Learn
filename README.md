# Лабораторная работа №3. Работа со ссылками, вложенные классы
## 1. Односвязный список
Реализуйте класс, хранящий набор значений при помощи односвязного списка. Напишите программу, иллюстрирующую использование класса.
Односвязный список - это структура, хранящая данные в виде цепочки, каждый узел которой хранит очередное значение списка и ссылку на следующий узел (см. рис.). Ссылка на следующий узел последнего элемента списка равна null.

```mermaid
%%{init: {'config': {'width': '100%', 'useMaxWidth': true}}}%%
flowchart LR
    subgraph NodeA["Head"]
        direction LR
        A_val[data] ~~~ A_next[next]
    end
    subgraph NodeB[" "]
        direction LR
        B_val[data] ~~~ B_next[next]
    end
    subgraph NodeC["Tail"]
        direction LR
        C_val[data] ~~~ C_next[next]
    end
    A_next --> NodeB
    B_next --> NodeC
    C_next --> NodeD@{ shape: text, label: "Null" }

```