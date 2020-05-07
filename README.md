# Структуры данных: 10 типов структур данных, которые нужно знать

Структуры данных играют важную роль в процессе разработки ПО, а еще по ним часто задают вопросы на собеседованиях для разработчиков. Хорошая новость в том, что по сути они представляют собой всего лишь специальные форматы для организации и хранения данных.

### Очередь и стек

Начнем с этих двух, т.к. они немного схожи. 
По факту сами они не являются конкретными структурами данных, а объектами реализующий две операции: положить в очередь (push) и взять из очереди (pull). 
Для очереди порядок выдаются объекты в том же порядке — __FIFO__ (First In First Out), для стека в обратном — __LIFO__ (Last In First Out). Ну, есть ещё очередь с приоритетами — это когда первым выдаёт объект с наивысшим приоритетом.

---

#### Стек

Это коллекция, элементы которой получают по принципу «последний вошел, первый вышел» (Last-In-First-Out или __LIFO__). 
Это значит, что мы будем иметь доступ только к последнему добавленному элементу.

В отличие от списков, мы не можем получить доступ к произвольному элементу стека. 
Мы можем только добавлять или удалять элементы с помощью специальных методов. 
У стека нет также метода Contains, как у списков. 
Кроме того, у стека нет итератора. 
Для того, чтобы понимать, почему на стек накладываются такие ограничения, давайте посмотрим на то, как он работает и как используется.

Наиболее часто встречающаяся аналогия для объяснения стека — стопка тарелок. 
Вне зависимости от того, сколько тарелок в стопке, мы всегда можем снять верхнюю. 
Чистые тарелки точно так же кладутся на верх стопки, и мы всегда будем первой брать ту тарелку, которая была положена последней.

![stack](img/stack.jpg)

Если мы положим, например, красную тарелку, затем синюю, а затем зеленую, то сначала надо будет снять зеленую, потом синюю, и, наконец, красную. 
Главное, что надо запомнить — тарелки всегда ставятся и на верх стопки. 
Когда кто-то берет тарелку, он также снимает ее сверху. 
Получается, что тарелки разбираются в порядке, обратном тому, в котором ставились.

Существует три основных операции, которые могут выполняться в стеках: 
- вставка элемента в стек (называемый «push»);
- удаление элемента из стека (называемое «pop»);
- отображение содержимого стека (иногда называемого «peak»).

Теперь, когда мы понимаем, как работает стек, введем несколько терминов. 
Операция добавления элемента на стек называется «push», удаления — «pop». 
Последний добавленный элемент называется верхушкой стека, или «top», и его можно посмотреть с помощью операции «peek». 

**Реализация**
<details>
  <summary>Скрытый код</summary>

  ```go
  type Stack struct {
  	mt      sync.RWMutex
  	storage map[int]interface{}
  	count   int
  }
  
  func NewStack() *Stack {
  	return &Stack{
  		storage: map[int]interface{}{},
  		count:   0,
  	}
  }
  
  // Adds a value onto the end of the stack
  // Добавляет элемент на вершину стека.
  // Сложность: O(1).
  func (s *Stack) Push(value interface{}) {
  	s.mt.Lock()
  	defer s.mt.Unlock()
  	s.storage[s.count] = value
  	s.Inc()
  }
  
  // Removes and returns the value at the end of the stack
  // Удаляет элемент с вершины стека и возвращает его. Если стек пустой, возвращает nil
  // Т.к.`Push` добавляет элементы в конец списка, поэтому забирать их будет также с конца.
  // Сложность: O(1).
  func (s *Stack) Pop() interface{} {
  	s.mt.Lock()
  	defer s.mt.Unlock()
  	if s.Size() == 0 {
  		return nil
  	}
  
  	s.Dec()
  	result := s.storage[s.Size()]
  	delete(s.storage, s.Size())
  	return result
  }
  
  // Returns the value at the end of the stack
  // Возвращает верхний элемент стека, но не удаляет его.
  // Сложность: O(1).
  func (s *Stack) Peek() interface{} {
  	s.mt.RLock()
  	defer s.mt.RUnlock()
  
  	if s.Size() == 0 {
  		return nil
  	}
  
  	return s.storage[s.Size()-1]
  }
  
  // Get count elements in stack
  // Возвращает количество элементов в стеке.
  // Зачем нам знать, сколько элементов находится в стеке, если мы все равно не имеем к ним доступа?
  // С помощью этого поля мы можем проверить, есть ли элементы на стеке или он пуст.
  // Сложность: O(1).
  func (s *Stack) Size() int {
  	return s.count
  }
  
  func (s *Stack) Inc() {
  	s.count++
  }
  
  func (s *Stack) Dec() {
  	s.count--
  }
  ```
</details>

**Примнение**

<details>
  <summary>Обра́тная по́льская запись (англ. Reverse Polish notation, RPN)</summary>

  ```go
  // todo coming soon
  ```
</details>

**Сложность:**

Алгоритм | В среднем | Худший случай
--- | --- | ---
Space | O(n) | O(n) |
Search | O(n) | O(n) |
Insert | O(1) | O(1) |
Delete | O(1) | O(1) |

---

#### Очередь

Вы можете думать об этой структуре, как об очереди людей в продуктовом магазине. Стоящий первым будет обслужен первым. Также как очередь.
Если рассматривать очередь с точки доступа к данным, то она является __FIFO__ (First In First Out). 
Это означает, что после добавления нового элемента все элементы, которые были добавлены до этого, должны быть удалены до того, как новый элемент будет удален.

В очереди есть только две основные операции и две вспомогательные: 
- enqueue;
- dequeue;
- peek;
- count.
 
Enqueue означает вставить элемент в конец очереди, а dequeue означает удаление переднего элемента.

**Реализация**
<details>
  <summary>Скрытый код</summary>

  ```go
  type Queue struct {
  	storage []interface{}
  }
  
  // Добавляет элемент в очередь.
  // Новые элементы очереди можно добавлять как в начало списка, так и в конец.
  // Важно только, чтобы элементы доставались с противоположного края.
  // Сложность: O(1).
  func (q *Queue) Enqueue() {
  
  }
  
  // Удаляет первый помещенный элемент из очереди и возвращает его.
  // Если очередь пустая, возвращает nil
  // Поскольку мы вставляем элементы в начало списка, убирать мы их будем с конца.
  // Сложность: O(1).
  func (q *Queue) Dequeue() {
  
  }
  
  // Возвращает элемент, который вернет следующий вызов метода Dequeue.
  // Очередь остается без изменений. Если очередь пустая, возврщается nil
  // Сложность: O(1)
  func (q *Queue) Peek() {
  
  }

  // Возвращает количество элементов в очереди или 0, если очередь пустая.
  // Сложность: O(1).
  func (q *Queue) Count() int {
  	return len(q.storage)
  }
  ```
</details>

**Сложность:**

Алгоритм | В среднем | Худший случай
--- | --- | ---
Space | O(n) | O(n) |
Search | O(n) | O(n) |
Insert | O(1) | O(1) |
Delete | O(1) | O(1) |

---