# Сравнить хэш-таблицы с различными способами разрешения коллизий.
_______________

# Содержание

* [Описание структуры](#op)
* [Метод цепочек](#cep)
* [Линейное опробование](#adr)
* [Двойное хеширование](#doub)
* [Сравнение и выводы](#cp)
*****  
<a name="op"></a>
## Описание структуры

  Этот шаблонный класс описывает объект, управляющий последовательностью элементов типа  
          `std::pair<const Key, Ty>`
  переменной длины. Последовательность слабо упорядочена хэш-функцией, которая разделяет последовательность в упорядоченный набор подпоследовательностей, называемых блоками. В каждом блоке функция сравнения определяет, упорядочена ли каждая пара элементов соответствующим образом. Каждый элемент содержит два объекта: ключ и значение. Последовательность представляется в виде, позволяющем выполнять поиск, вставку и удаление произвольного элемента несколькими операциями, которые могут не зависеть от числа элементов в последовательности (постоянное время), по крайней мере, когда все блоки имеют примерно одинаковую длину. В худшем случае, когда все элементы находятся в одном блоке, количество операций пропорционально количеству элементов в последовательности (линейное время). Кроме того, вставка элементов не делает итераторы недействительными, а при удалении элементов недействительными становятся только итераторы, указывающие на удаленный элемент.

  Хеш-функция должна иметь следующие свойства:

  * Всегда возвращать один и тот же адрес для одного и того же ключа;
  * Не обязательно возвращает разные адреса для разных ключей;
  * Использует все адресное пространство с одинаковой вероятностью;
  * Быстро вычислять адрес.
  
  При заполнении таблицы возникают ситуации, когда для двух неодинаковых ключей функция вычисляет один и тот же адрес. Данный случай носит название коллизия, а такие ключи называются ключи-синонимы.
  Для разрешения коллизий используются различные методы, которые в основном сводятся к методам цепочек и открытой адресации.

<a name="cep"></a>
 ## Метод цепочек 

  Каждая ячейка массива является указателем на связный список (цепочку) пар ключ-значение, соответствующих одному и тому же хеш-значению ключа. Коллизии просто приводят к тому, что появляются цепочки длиной более одного элемента.
  Если выбран метод цепочек, то вставка нового элемента происходит за O(1)
  Операции поиска или удаления данных требуют просмотра всех элементов соответствующей ему цепочки, чтобы найти в ней элемент с заданным ключом. Для добавления данных нужно добавить элемент в конец или начало соответствующего списка, и, в случае если коэффициент заполнения станет слишком велик, увеличить размер массива и перестроить таблицу.
  При предположении, что каждый элемент может попасть в любую позицию таблицы с равной вероятностью и независимо от того, куда попал любой другой элемент, среднее время работы операции поиска элемента составляет O(1+k), где k – коэффициент заполнения таблицы.
  Поиск в хеш-таблице с цепочками переполнения осуществляется следующим образом. Сначала вычисляется адрес по значению ключа. Затем осуществляется последовательный поиск в списке, связанном с вычисленным адресом.
  Процедура удаления из таблицы сводится к поиску элемента и его удалению из цепочки переполнения.


 ## Линейное опробование
<a name="adr"></a>

  В отличие от хеширования с цепочками, при открытой адресации никаких списков нет, а все записи хранятся в самой хеш-таблице. Каждая ячейка таблицы содержит либо элемент динамического множества, либо NULL.
  В этом случае, если ячейка с вычисленным индексом занята, то можно просто просматривать следующие записи таблицы по порядку до тех пор, пока не будет найден ключ K или пустая позиция в таблице. Для вычисления шага можно также применить формулу, которая и определит способ изменения шага. 
  Данный метод можно выполнять квадратичным,линейным и методом двойного хэширования. Последние 2 рассмотрим подробнее.
  Линейное опробование сводится к последовательному перебору элементов таблицы с некоторым фиксированным шагом
     
        `a=h(key) + c*i` 
     
  Опишем алгоритмы вставки и поиска для метода линейное опробование
  
  ### Вставка

        `i = 0
        a = h(key) + i*c
        Если t(a) = свободно, то t(a) = key, записать элемент, стоп элемент добавлен
        i = i + 1, перейти к шагу 2`
        
  ### Поиск

         `i = 0
         a = h(key) + i*c
         Если t(a) = key, то стоп элемент найден
         Если t(a) = свободно, то стоп элемент не найден
         i = i + 1, перейти к шагу 2`
  
  Аналогичным образом можно было бы сформулировать алгоритмы добавления и поиска элементов для любой схемы открытой адресации. Отличия будут только в выражении, используемом для вычисления адреса.
  
  С процедурой удаления дело обстоит не так просто, так как она в данном случае не будет являться обратной процедуре вставки.Дело в том, что элементы таблицы находятся в двух состояниях: свободно и занято. Если удалить элемент, переведя его в состояние свободно, то после такого удаления алгоритм поиска будет работать некорректно.Существует подход, который свободен от перечисленных недостатков. Его суть состоит в том, что для элементов хеш-таблицы добавляется состояние удалено. Данное состояние в процессе поиска интерпретируется, как занято, а в процессе записи как свободно.
  
 <a name="doub"></a> 
 ## Двойное хеширование 
  
  Двойное хеширование аналогично линейному опробованию, за исключением того, что здесь используется вторая хеш-функция — для определения шага поиска, используемого после каждой коллизии. Шаг поиска должен быть ненулевым, а размер таблицы и шаг поиска должны быть взаимно простыми числами. Удаление реализуется аналоогично линейному опробованию. На практике мы создадим массив булевых переменных, каждая из которых будет соответсвовать элементу таблицы. Теперь при удалении мы просто будем помечать наш объект как удалённый, а при добавлении как не удалённый и замещать новым добавляемым объектом. При поиске, помимо равенства ключей, мы будем смотреть, удалён ли элемент, если да, то идти дальше.
Вставка
  Выбор второй хеш-функции требует определенной осторожности, поскольку в противном случае программа может вообще не работать. Во-первых, необходимо исключить случай, когда вторая хеш-функция дает нулевое значение, поскольку при первой же коллизии это приведет к бесконечному циклу. Во-вторых, важно, чтобы значение второй хеш-функции и размер таблицы были взаимно простыми числами, т.к. иначе некоторые из последовательностей проб могут оказаться очень короткими (например, в случае, когда размер таблицы вдвое больше значения второй хеш-функции). Один из способов претворения этих правил в жизнь — выбор в качестве M простого числа и выбор второй хеш-функции, возвращающей значения, меньшие M. 
  
 <a name="cp"></a>
 #  Сравнение и выводы
 
 Для сравнения работы данных методов, реализуем их с помощью кода и средствами встроенных библиотек рассчитаем время, затраченное на разрешение коллизий в каждом случае. Кроме того, запишем математимаческую модель и явно покажем сожность операций каждого метода в худшем, среднем и лучшем случае. Далее получим затраты по памяти и на основе всех данных составим таблицу, демонстрирующую преимущества и недостатки каждого из методов.
