# Contiguous View

В данном задании вам предстоит написать класс `contiguous_view`.

Он предоставляет интерфейс, схожий с контейнером `std::vector`, но, в отличие
от него, не владеет и, соответственно, не управляет памятью, в которой хранятся
элементы.

При этом ему требуется, чтобы все элементы лежали в памяти последовательно,
поэтому и "contiguous".

У `contiguous_view<T, Extent>` есть два шаблонных параметра:

- `T` &mdash; тип элемента, на который ссылается view (может быть `const`, тогда
  view предоставляет read-only доступ к данным, т.е. иммутабелен);
- `Extent` &mdash; размер view, если он известен на этапе компиляции, иначе
  константа `dynamic_extent`, соответствующая динамическому размеру.

Такой подход позволяет в случае статического размера сэкономить память, не храня
размер дополнительным полем.

### Описание методов

- `contiguous_view(const contiguous_view<U, N>& other)` &mdash; конвертирующий
  конструктор. Имеет смысл только в случае `U = T` или `const U = T`. **Его не
  должно быть возможно использовать для конвертации из одного статического
  размера в другой.**

- `subview(size_t offset, size_t count = dynamic_extent)` &mdash; получить view
  на часть текущего view. Если `count = dynamic_extent`, берётся суффикс.

- `subview<Offset, Count>()` &mdash; то же самое, но параметры задаются
  шаблонными параметрами, а значит известны на этапе компиляции.

- Аналогично, по две версии `first` и `last` &mdash; возвращают префикс или
  суффикс заданной длины. В отличие от `subview`, `Count` и `count` должны быть
  точным значением `size()` возвращаемого view (т.е. например запрещён
  `contiguous_view<T, 10>(a, b).first<dynamic_extent>()`).

- `size_bytes()` &mdash; размер view в байтах.

- `as_bytes()` &mdash; получить view на ту же память, что и оригинальный view,
  но в качестве типа выступает `std::byte` или `const std::byte`, в зависимости
  от того, был ли оригинальный view мутабельным или иммутабельным.

В интерфейсе задания есть некоторые placeholder'ы, которые вам надо заполнить:

- В строках вида `using X = void;` нужно заменить `void` на осмысленный тип.
- Если в возвращаемом типе в качестве `Extent` указан `0`, надо подумать, какой
  действительно будет `Extent`. **При этом он должен быть статическим во всех
  случаях, когда это возможно.** Если возвращаемый тип получается очень длинным,
  пожалуйста, не надо пытаться вместить всю сигнатуру функции на одной строке
  &mdash; вы можете вынести куда-нибудь весь возвращаемый тип или его отдельные
  части, или же воспользоваться `auto`.
- Некоторые конструкторы имеет смысл делать или не делать `explicit`, в
  зависимости от значений шаблонных параметров. В частности, опасно создавать
  статический view из динамического, т.к. размер может не соответствовать.
  Поэтому желание вызвать такой конструктор стоит проявлять в коде явно. В то же
  время, преобразование в обратную сторону совершенно безобидное. Чтобы задавать
  такие условия, используется конструкция `explicit(условие)`. Сейчас в коде
  можно встретить `explicit(true)`, но вам нужно подумать над тем, какие там
  действительно стоит задать условия.

### Ассерты

Все предусловия, озвученные здесь или додуманные вами, должны проверяться, и
по возможности приводить к ошибкам компиляции. В случае ошибок, обнаруживаемых
лишь в рантайме, необходимо использовать `runtime_assert`.

**Эта часть задания лишь частично проверяется тестами, поэтому не стоит ориентироваться только на них при продумывании проверяемых предусловий &mdash; думайте своей головой.**

### std::string_view

Для `contiguous_view<const char, ...>` (и только для него) должно быть доступно явное преобразование в `std::string_view`.