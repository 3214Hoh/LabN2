## Отчет по лабораторной работе № 1

#### № группы: `ПМ-2502`

#### Выполнил: `Филиппов Данил Константинович`

#### Вариант: `22`

### Cодержание:

- [Алгоритм для задачи №1](#1-алгоритм-для-задачи-1)
- [Программа для задачи №1](#2-программа-для-задачи-1)
- [Анализ правильности решения для задачи №1](#3-анализ-правильности-решения-для-задачи-1)
- [Алгоритм для задачи №2](#4-алгоритм-для-задачи-2)
- [Программа для задачи №2](#5-программа-для-задачи-2)
- [Ответ для задачи №2](#6-ответ-для-задачи-2)
- [Программа для задачи №3](#7-программа-для-задачи-3)
- [Тесты для задачи №3](#8-тесты-для-задачи-3)
- [Программа для задачи №4](#9-программа-для-задачи-4)
- [Тесты для задачи №4](#10-тесты-для-задачи-4)

### 1. Алгоритм для задачи №1

#### Алгоритм выполнения программы:

1. **Ввод данных:**
  Программа считывает два целых числа: `a0`, `n`.

2. **Проверка на коректность:**  
  Программа проверяет, что `0` < `n` < `1000`. 

3. **Подсчет и вывод результата:**
  Программа обращяется к функции `sum_zf`, которая подсчитывает
  сумму цифр преведущего члена и умножает на 7, далее выводит на экран.

### 2. Программа для задачи №1

```java
import java.io.PrintStream;
import java.util.Scanner;

public class j_1 {
    public static Scanner in = new Scanner(System.in);
    public static PrintStream out = System.out;
    //функция для вычисления суммы цифр числа
    private static int sum_zf(int n) {
        int sum = 0;
        while (n > 0) {
            sum += n % 10;
            n /= 10;
        }
        return sum;
    }
    public static void main(String[] args) {
        //ввод данных
        out.println("a₀ (нулевой член)");
        int a0 = in.nextInt();
        out.println("n (число следующих членов)\nНатуральное, 0 < n < 1000");
        int n = in.nextInt();
        
        // проверка на коректность данных
        if (n < 0 || n > 1000){
            out.print("Выход за диапозон");
            return;
        }

        //вводная информация
        out.println("\n$a_0: " + a0);
        out.print("a_1...a_" + n + ":\n");

        int an = a0;
        for (int i = 1; i <= n; i++) {
            // Вычисляем следующий элемент: 7 * (сумма цифр текущего числа)
            an = 7 * sum_zf(an);
            //вывод
            out.print(an);
            if (i < n) {
                out.print(", ");
            }
        }
    }
}
```
### 3. Анализ правильности решения для задачи №1

Программа работает корректно на всем множестве решений с учетом ограничений,
в этом можно убедиться, если сравнить последовательности с сайта: https://entropycat.ru/variant?v=22.

### 4. Алгоритм для задачи №2

#### Алгоритм выполнения программы:

1. **Создание переменных:**<br>
  Программа создает трехмерных массив `dp`,
  в котором на первой позии длина числа (от 0 до 12)
  на второй позиции остаток от деления на 7 (если 0, то число делится на 7)
  на третьей позии состояния поиска.

2. **Заполнение `dp`:** 
  Программа переберает все коэциенты, проверяя, что число не 0 и не начинается с 0
  далее обновляется статус поиска
  как работает обновление поиска:<br>
  0: ещё не нашли начало паттерна или сбросили поиск<br>
  1: только что нашли цифру 5 (начало паттерна)<br>
  2: нашли 5 и следующую цифру (это ?)<br>
  3: успешно нашли полный паттерн 5?3<br>
   
  Текущее состояние → Новая цифра → Новое состояние<br>
  0 → 5 → 1        // начало паттерна<br>
  1 → любая → 2    // нашли '5?'<br>
  2 → 3 → 3        // нашли '5?3'<br>
  2 → не 3 → 0     // сброс (паттерн нарушен)<br>
  3 → любая → 3    // паттерн уже найден, остаёмся в 3. <br>

4. **Подсчет и вывод результата:**<br>
   Программа преберает все длины, числа которых делятся на 7 и есть маска `3?5`.

### 5. Программа для задачи №2

```java
import java.io.PrintStream;
import java.util.Scanner;

public class j_1 {
    public static Scanner in = new Scanner(System.in);
    public static PrintStream out = System.out;
    public static void main(String[] args) {

        //ввод переменных
        long[][][] dp = new long[13][7][4];
        dp[0][0][0] = 1;

        //заполнение массива ответов dp
        for (int len = 0; len < 13; len++) {
            for (int mod = 0; mod < 7; mod++) {
                for (int var = 0; var < 4; var++) {
                    long val = dp[len][mod][var];
                    if (val == 0) continue; // не начинаем с 0

                    for (int d = 0; d < 10; d++) {
                        if (len == 0 && d == 0) continue; // не начинаем с 0
                        
                        //переменные для проверки маски
                        int newMod = (mod + d) % 7;
                        int newvar = var;

                        //проверка маски *5?3*
                        if (var == 0 && d == 5) newvar = 1;       //нашли 5
                        else if (var == 1) newvar = 2;           //нашли 5?
                        else if (var == 2 && d == 3) newvar = 3; //нашли 5?3
                        else if (var != 3) newvar = (d == 5) ? 1 : 0; //сброс заново

                        dp[len+1][newMod][newvar] += val;
                    }
                }
            }
        }
        long ans = 0;
        for (int len = 1; len <= 13; len++) {
            ans += dp[len][0][3]; // сумма цифр ≡ 0 mod 7, паттерн найден
        }
        System.out.println(ans);
    }
}
```

### 6. Ответ для задачи №2

12761637892

### 7. Программа для задачи №3

```java
import java.io.PrintStream;
import java.util.ArrayList;
import java.util.List;
import java.util.Scanner;

public class j_1 {
    public static Scanner in = new Scanner(System.in);
    public static PrintStream out = System.out;
    public static void main(String[] args) {
        List<Integer> n = new ArrayList<>();
        int num = 1;

        //Чтение последовательности
        while (num >= 0) {
            num = in.nextInt();
            n.add(num);
        }

        //Находим индексы первого и последнего нуля
        int id1 = -1;
        int id2 = -1;
        for (int i = 0; i < n.size(); i++) {
            if (n.get(i) == 0) {
                if (id1 == -1) {
                    id1 = i;
                }
                id2 = i;
            }
        }
        //Если нет хотя бы двух нулей, выводим 0
        if (id1 == -1 || id1 == id2) {
            out.println(0);
            return;
        }
        int max = 0;
        int id_vr = id1;
        //Проходим по последовательности между первым и последним нулем
        for (int i = id1 + 1; i <= id2; i++) {
            if (n.get(i) == 0) {
                // Вычисляем длину подпоследовательности между двумя нулями
                int len = i - id_vr - 1;
                //Сравниваем с максимальной длиной
                max = Math.max(len, max);
                //Начинаем отсчет для следующей подпоследовательности
                id_vr = i;
            }
        }
        out.println(max);
    }
}
```

### 8. Тесты для задачи №3

1. Тест:
    - **Input**:
        ```
        1
        2
        3
        4
        0
        1
        12
        3
        4
        4
        1
        0
        -1
        ```

    - **Output**:
        ```
        6
        ```
2. Тест:
   - **Input**:
        ```
        1
        0
        -1
        ```

    - **Output**:
        ```
        0
        ```

### 9. Программа для задачи №4

```java
import java.io.PrintStream;
import java.util.Scanner;

public class j_1 {
    public static Scanner in = new Scanner(System.in);
    public static PrintStream out = System.out;
    public static void main(String[] args) {
        Scanner scanner = new Scanner(System.in);

        //Чтение данных
        int n = in.nextInt();
        int d = in.nextInt();
        int[] arr = new int[n];

        for (int i = 0; i < n; i++) {
            arr[i] = in.nextInt();
        }

        int cnt = 0;
        //Перебираем все возможные пары
        for (int i = 0; i < n; i++) {
            for (int j = i + 1; j < n; j++) {
                // Вычисляем модуль разности
                int diff = Math.abs(arr[i] - arr[j]);
                if (diff <= d) {
                    cnt++;
                }
            }
        }
        out.println(cnt);
    }
}
```

### 10. Тесты для задачи №4
1. Тест:
   - **Input**:
        ```
        5 2
        1 3 5 7 9
        ```

    - **Output**:
        ```
        4
        ```
2. Тест:
   - **Input**:
        ```
        2 10
        1 2 10 12 60 12 123 443 123 12412 123 123 123 123 123 12312 3 123 12 3 12 312
        ```

    - **Output**:
        ```
        1
        ``` 
