# lab6

ОТЧЕТ ПО ТЕСТИРОВАНИЮ ПРОГРАММЫ «КОТ МЯУКАЕТ»
1. ОБЩИЕ СВЕДЕНИЯ
Название программы: Система учета мяуканий котов
Язык программирования: C#
Платформа: .NET
Тип приложения: Консольное
Дата тестирования: [Текущая дата]

2. СЦЕНАРИИ ТЕСТИРОВАНИЯ
2.1. Тест 1: Базовый функционал класса Cat
Описание: Проверка создания кота и его мяуканья

Шаги выполнения:

Создать кота с именем "Барсик"

Вызвать Meow() один раз

Вызвать Meow(3) для тройного мяуканья

Ожидаемый результат:

text
-----1-----
Барсик: мяу!
Барсик: мяу-мяу-мяу!
Фактический результат: ✅ Совпадает

Проблемы: Нет

2.2. Тест 2: Работа с интерфейсом MAAAO
Описание: Проверка полиморфизма через статический метод

Шаги выполнения:

Создать двух котов: "Барсик" и "Мурзик"

Создать объект KEsha

Передать всех в метод MeowsCare()

Ожидаемый результат:

text
-----2-----
Барсик: мяу!
Мурзик: мяу!
Иннокентий : мяяууууу...
Фактический результат: ✅ Совпадает

Замечания:

Кот "Барсик" мяукает повторно (уже мяукал в тесте 1)

Следовало бы создать нового кота для чистоты теста

2.3. Тест 3: Подсчет мяуканий через декоратор
Описание: Проверка работы паттерна Decorator

Шаги выполнения:

Создать кота "Снежок"

Обернуть в MeowCounter

Передать 5 одинаковых ссылок в MeowsCare

Вывести счетчик

Ожидаемый результат:

text
-----3-----
Снежок: мяу!
Снежок: мяу!
Снежок: мяу!
Снежок: мяу!
Снежок: мяу!
Кот мяукал 5 раз
Фактический результат: ✅ Совпадает

Важное замечание:

В список передаются 5 ссылок на один и тот же объект counter

Каждая ссылка вызывает Meow() у одного объекта

Counter.Count увеличивается на 1 при каждом вызове

Итого: 5 вызовов = Count = 5

3. ДЕТАЛЬНЫЙ АНАЛИЗ РАБОТЫ ПРОГРАММЫ
3.1. Последовательность выполнения
text
1. Запуск программы
2. Вывод "-----1-----"
3. Создание Cat("Барсик")
4. barsik.Meow() → "Барсик: мяу!"
5. barsik.Meow(3) → "Барсик: мяу-мяу-мяу!"
6. Вывод "-----2-----"
7. Создание Cat("Мурзик")
8. Создание KEsha()
9. Funs.MeowsCare([barsik, murzik, kesha]):
   - barsik.Meow() → "Барсик: мяу!" (повторно)
   - murzik.Meow() → "Мурзик: мяу!"
   - kesha.Meow() → "Иннокентий : мяяууууу..."
10. Вывод "-----3-----"
11. Создание Cat("Снежок")
12. Обертывание в MeowCounter(snow)
13. Создание Enumerable.Repeat(counter, 5) - 5 одинаковых ссылок
14. Funs.MeowsCare([counter, counter, counter, counter, counter]):
    - counter.Meow() → Count=1, snow.Meow() → "Снежок: мяу!"
    - counter.Meow() → Count=2, snow.Meow() → "Снежок: мяу!"
    - counter.Meow() → Count=3, snow.Meow() → "Снежок: мяу!"
    - counter.Meow() → Count=4, snow.Meow() → "Снежок: мяу!"
    - counter.Meow() → Count=5, snow.Meow() → "Снежок: мяу!"
15. Вывод "Кот мяукал 5 раз"
3.2. Ключевые моменты реализации
1. Работа Enumerable.Repeat:

csharp
Enumerable.Repeat(counter, 5)
Создает последовательность из 5 элементов, где каждый элемент - та же самая ссылка на объект counter. Не создает 5 разных объектов!

2. Цепочка вызовов в декораторе:

csharp
// При вызове counter.Meow():
1. Count++           // увеличиваем счетчик декоратора
2. target.Meow()     // вызываем Meow() у оригинального кота
   → Console.WriteLine($"{name}: мяу!")
3. Null-безопасность в MeowsCare:

csharp
foreach (var m in list)
    m?.Meow();  // безопасный вызов через null-conditional operator
4. ТЕСТ-КЕЙСЫ ДЛЯ ДОПОЛНИТЕЛЬНОГО ТЕСТИРОВАНИЯ
4.1. Граничные случаи (не покрыты в программе)
Тест A: Передача null в конструктор Cat

csharp
try 
{
    var cat = new Cat(null);
    Console.WriteLine("FAIL: Должно быть исключение");
}
catch (ArgumentException)
{
    Console.WriteLine("PASS: Исключение поймано");
}
Тест B: Meow с некорректным параметром

csharp
try 
{
    barsik.Meow(0);
    Console.WriteLine("FAIL: Должно быть исключение");
}
catch (ArgumentException)
{
    Console.WriteLine("PASS: Исключение поймано");
}
Тест C: Пустая коллекция в MeowsCare

csharp
Funs.MeowsCare(new List<MAAAO>());  // Не должно быть ошибок
Тест D: Коллекция с null элементами

csharp
Funs.MeowsCare(new MAAAO[] { barsik, null, murzik });
// Ожидается: два мяуканья, без ошибок
4.2. Тесты производительности
Тест E: Множественные вызовы

csharp
var counter = new MeowCounter(new Cat("Тестовый"));
for (int i = 0; i < 10000; i++)
    counter.Meow();
Console.WriteLine($"Счетчик: {counter.Count}");  // Должно быть 10000
Тест F: Вложенные декораторы

csharp
var cat = new Cat("Мурка");
var counter1 = new MeowCounter(cat);
var counter2 = new MeowCounter(counter1);
counter2.Meow();
Console.WriteLine($"Counter1: {counter1.Count}, Counter2: {counter2.Count}");
// Ожидается: 1 и 1 (оба увеличиваются)
5. НАЙДЕННЫЕ ПРОБЛЕМЫ И РЕКОМЕНДАЦИИ
5.1. Потенциальные проблемы
1. Повторное использование объектов:
В тесте 2 используется уже "использованный" кот barsik. Рекомендуется создавать новые объекты для каждого теста.

2. Отсутствие проверки в MeowCounter:
Конструктор MeowCounter не проверяет target на null:

csharp
public MeowCounter(MAAAO target)
{
    this.target = target;  // должно быть: target ?? throw new ArgumentNullException(...)
}
3. Неочевидное поведение Enumerable.Repeat:
Новички могут ожидать, что Repeat создает разные объекты, а не ссылки на один.

5.2. Рекомендации по улучшению
Добавить валидацию в MeowCounter:

csharp
public MeowCounter(MAAAO target)
{
    this.target = target ?? throw new ArgumentNullException(nameof(target));
}
Добавить метод Reset() в MeowCounter:

csharp
public void Reset() => Count = 0;
Добавить логирование:

csharp
public void Meow()
{
    Count++;
    Console.WriteLine($"[DEBUG] Вызов #{Count}");
    target.Meow();
}
Создать фабричный метод:

csharp
public static MeowCounter CreateCounter(Cat cat)
{
    return new MeowCounter(cat);
}
