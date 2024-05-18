## Реализация операций
Все операции работают за гарантированный логарифм.
  
- std::pair<iterator, bool> ***insert***(const pair<key, value>&); 
(bool - произошла ли вставка. **Можно вставлять только по новому ключу**)

- ***erase***(const key&);     ***erase***(iterator)

- Value& ***at***(const key&);
(если есть элемент, возвращает его, **иначе кидает исключение**)

- Value& operator\[]\(const key&);
**нет константной версии**, т.к. создает элемент со значением по умолчанию при отсутствии элемента

- iterator find(const key&);
 ```c++
if (auto iter = m.find(key); iter != m.end()) {
   it->second = 2;
}
// эффективный поиск элемента
```

begin -  самый нижний левый лист
fake_node - корень
```c++
struct BaseNode {
  BaseNode* left;
  BaseNode* right;
  BaseNode* parent;
}

struct Node : BaseNode {
  pair<const key, value> kv; // при изменении ключа дерево перестает быть упорядоченным
  bool red;
}
```

Весь обход дерева работает за O(n) (посещаем каждую вершину не больше 3-х раз)
![[map.png]]
## Set
- все то же самое, только вместо пары хранится значение

## Multimap
- полезны lower_bound upper_bound
- equal_range возвращает два итератора на lower_bound и upper_bound