# 📊 Аналіз продажів мережі магазинів в Power BI
Даний проєкт спрямований на детальний аналіз продажів роздрібної мережі магазинів за період 2022-2024 років. Метою проєкту є розробка дашборду у Power BI, який дозволить відстежувати тенденції продажів, порівнювати показники окремих магазинів і регіонів, а також контролювати виконання планових цілей.
## 📌 Основні етапи
### Реалізація проєкту включала наступні етапи:
1. Для аналізу я використав дані платформи Laba, які містять інформацію про діяльність мережі з 12 магазинів у трьох містах (Київ, Дніпро, Харків) за період з початку 2022 року до кінця 2024 року. Дані були представлені у форматі Excel-файлів та містили таблиці фактів (ПланПродажів, ПланСобівартість, рЗакупки, рОперВитрати, рПродажіФакт) і довідники (довМагазини, довПокупці, довТовари, Календар). Я здійснив первинну очистку та перевірку даних, щоб забезпечити їхню якість і цілісність. Зокрема, це передбачало видалення нульових значень та змінення типу даних, де це було необхідно.

2. Сформував модель даних із використанням функціоналу реляційного моделювання. Таблиці пов’язав через ключові поля (Дата, ID-магазинів, ID-покупців).

![Модель даних](Images/Модель%20даних.png)

3. На першій сторінці звіту створив інтерактивний дашборд, що відображає ключові показники: план-факт продажів, маржинальний прибуток та динаміку продажів за обраний період. Візуалізація «Фактичні продажі та середній чек» дозволяє оцінити ефективність магазинів, а «Структура продажів по номенклатурі» демонструє розподіл продажів за категоріями товарів. Блок «Топ-10 номенклатури» надає інформацію про найбільш популярні товари. Панель фільтрів забезпечує деталізований аналіз даних за роками, датами та магазинами.

![Аналіз продажів. Частина 1](Images/Аналіз%20продажів.%20Частина%201.png)

Додав функцію порівняння фактичних продажів з аналогічним періодом минулого року через інтерактивну підказку у вигляді діаграми, що дозволяє швидко оцінити зміни та відхилення у динаміці продажів.

![Підказка](Images/Підказка.png)

Окрім цього, у візуалізацію «Топ-10 номенклатури» я інтегрував опцію деталізації для обраного товару, що дає можливість детально дослідити його динаміку продажів, структуру, а також проаналізувати кількість та суму реалізованої продукції.

![Картка номенклатури](Images/Картка%20номенклатури.png)

4. У другій частині звіту я додав матрицю з умовним форматуванням, яка відображає продажі та чистий прибуток залежно від дати, міста та магазину. Також включив до даного дашборду інтерактивну географічну візуалізацію, що дозволяє наочно відстежувати, який асортимент товарів має найкращі продажі в кожному конкретному магазині.

![Аналіз продажів. Частина 2](Images/Аналіз%20продажів.%20Частина%202.png)

Для поглибленого дослідження продажів та середнього чека у візуалізації «Продажі та середній чек» я реалізував функцію деталізації даних з переходом на щомісячний звіт, де інформація представлена поденно для обраного місяця. Це дозволяє точніше аналізувати результати за конкретний період, виявляти пікові дні продажів, а також оцінювати ефективність окремих днів чи акцій.

![image](https://github.com/user-attachments/assets/f10b2747-de17-45b6-89de-82452a580e1d)

**Середній чек був розрахований через міру:**
```DAX
Середній чек = [Продажі міра] / DISTINCTCOUNT('рПродажіФакт'[№ чека])
```

**Кількість товарів в чеку визначалася за рахунок додавання такої міри:**
```DAX
Кількість товарів в чеку = AVERAGEX(
    SUMMARIZE('рПродажіФакт', 'рПродажіФакт'[№ чека], "Н", SUM('рПродажіФакт'[К-ть])),
    [Н]
)
```

5. Третя сторінка звіту доповнює аналіз продажів, фокусуючись на порівнянні планових та фактичних показників, метою якого є виявлення відхилень та оцінка ефективності виконання планів.

![image](https://github.com/user-attachments/assets/82ac70d9-52e0-4de1-aaec-2bea92f47d20)

6. На заключній сторінці звіту представив компактні матриці з додаванням мір, які допомогли дослідити середні продажі за 3 місяці, порівняти продажі з попереднім періодом, визначити долю продажів жіночих, чоловічих та унісекс товарів, простежити % зростання та падіння за досліджуваний період, а також проконтролювати залишок товарів.

![image](https://github.com/user-attachments/assets/8ce53615-4444-426c-a1f6-2d0c559f76a5)

**Для розрахунку середніх продажів за 3 місяці я використав наступну міру:**
```DAX
Середні продажі за 3 місяці = 
(
    CALCULATE([Продажі міра], DATEADD('Календар'[Дата], -3, MONTH)) +
    CALCULATE([Продажі міра], DATEADD('Календар'[Дата], -2, MONTH)) +
    CALCULATE([Продажі міра], DATEADD('Календар'[Дата], -1, MONTH))
) / 3
```

**Міра для порівняння продажів з попереднім періодом:**
```DAX
Продажі факт SPLY = CALCULATE([Продажі міра], SAMEPERIODLASTYEAR('Календар'[Дата]))
```

**Долю продажів жіночих, чоловічих та унісекс товарів я визначав за рахунок таких мір:**
```DAX
Доля продажів жіночих товарів = 
CALCULATE([Продажі міра], FILTER('довТовари', 'довТовари'[Стать] = "Жін.")) / 
CALCULATE([Продажі міра], ALL('довПокупці'[Стать]))
```

```DAX
Доля продажів чоловічих товарів = 
CALCULATE([Продажі міра], FILTER('довТовари', 'довТовари'[Стать] = "Чол.")) / 
CALCULATE([Продажі міра], ALL('довПокупці'[Стать]))
```

```DAX
Доля продажів унісекс товарів = 
CALCULATE([Продажі міра], FILTER('довТовари', 'довТовари'[Стать] = "Унісекс")) / 
CALCULATE([Продажі міра], ALL('довПокупці'[Стать]))
```

**% зростання та падіння за досліджуваний період я обчислював наступним чином:**
```DAX
Продажі факт Попередній місяць = CALCULATE([Продажі міра], PREVIOUSMONTH('Календар'[Дата]))
```

```DAX
% зростання/падіння = 
IF(
    DIVIDE([Продажі міра], [Продажі факт Попередній місяць]) - 1 = -1, 
    BLANK(), 
    DIVIDE([Продажі міра], [Продажі факт Попередній місяць]) - 1
)
```

**Залишок товарів я розрахував на основі такого комплексу мір:**
```DAX
К-ть продано накопичувально, од. = 
CALCULATE(
    SUM('рПродажіФакт'[К-ть]), 
    FILTER(
        ALL('Календар'[Дата]), 
        'Календар'[Дата] <= MAX('Календар'[Дата])
    )
)
```

```DAX
К-ть закуплено накопичувально, од. = 
CALCULATE(
    SUM('рЗакупки'[К-ть]), 
    FILTER(
        ALL('Календар'[Дата]), 
        'Календар'[Дата] <= MAX('Календар'[Дата])
    )
)
```

```DAX
Залишок товарів, од. = 
[К-ть закуплено накопичувально, од.] - [К-ть продано накопичувально, од.]
```

## 📝 Висновки та результати проєкту

Реалізований проєкт дозволив створити аналітичний інструмент для дослідження продажів мережі магазинів, який надає всебічну оцінку результативності діяльності. Розроблений дашборд у Power BI забезпечує можливість багатовимірного аналізу продажів з різними рівнями деталізації та інтерактивними фільтрами.

**Ключові функції та можливості дашборду:**

- Моніторинг ефективності продажів у розрізі магазинів, міст та періодів.
- Автоматизований розрахунок ключових показників та порівняння фактичних результатів з плановими.
- Інтерактивна візуалізація даних, що дозволяє швидко та наочно оцінювати комерційні результати.
- Поглиблений аналіз структури продажів за номенклатурою, статтю товарів та іншими параметрами.

Розроблений дашборд може бути корисним для менеджменту компаній при ухваленні управлінських рішень, плануванні та стратегічному розвитку мережі роздрібних магазинів.

