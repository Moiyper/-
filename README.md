# Гайд по решению задач на Python + код на C++

---

##  ОБЩИЙ ГАЙД (Python)

### Задачи 7 и 12 — «Неизвестная цифра x в системе счисления»

**Идея:** Перебираем все возможные значения `x` (от 0 до base-1), вычисляем значение выражения в десятичной системе, проверяем делимость.

```python
# Шаблон для задач типа 7 и 12
base = 94  # основание системы
for x in range(base - 1, -1, -1):  # от наибольшего к наименьшему
    # Раскладываем каждое число по разрядам
    # число = d4*base^4 + d3*base^3 + d2*base^2 + d1*base + d0
    val1 = 4*base**4 + 5*base**3 + 0*base**2 + x*base + 93
    val2 = 8*base**4 + 7*base**3 + 9*base**2 + x*base + 37
    val3 = 2*base**4 + 8*base**3 + 5*base**2 + x*base + 56
    total = val1 + val2 + val3
    if total % 93 == 0:  # проверяем кратность
        print(x, total // 31)
        break
```

**Ключевые моменты:**
- Буквы в алфавите: A=10, B=11, C=12, D=13, E=14, F=15, G=16, H=17, I=18
- Идём от наибольшего x к наименьшему — первый найденный и есть ответ
- В конце делим на указанное число

---

### Задача 6 — «Нули в системе счисления»

**Идея:** Вычисляем выражение, переводим в нужную систему, считаем нули.

```python
# Перевод в систему счисления
def to_base(n, base):
    if n == 0: return "0"
    digits = []
    while n > 0:
        digits.append(n % base)
        n //= base
    return digits[::-1]  # список цифр от старшего к младшему

for x in range(3999, 0, -1):  # от наибольшего
    val = 5*13**7 + 2*13**5 + 3*13**3 - x
    digits = to_base(val, 13)
    zeros = digits.count(0)
    if zeros % 2 == 1:  # нечётное количество нулей
        print(x)
        break
```

---

### Задачи на алгоритмы (22457, 28926, 27760)

**Идея:** Перебираем N, применяем алгоритм шаг за шагом, проверяем условие.

```python
# Перевод в систему счисления (строка)
def to_base_str(n, base):
    if n == 0: return "0"
    digits = []
    while n > 0:
        digits.append(n % base)
        n //= base
    return ''.join(map(str, digits[::-1]))

# Перевод из строки в десятичное
def from_base_str(s, base):
    return int(s, base)  # работает для base <= 36
    # или вручную:
    # result = 0
    # for ch in s:
    #     result = result * base + int(ch)
    # return result
```

**Общий шаблон:**
```python
for N in range(1, 100000):
    # Шаг 1: перевод в нужную систему
    s = to_base_str(N, base)
    
    # Шаг 2: обработка по правилу
    if условие_а:
        s = ...  # модификация строки
    else:
        s = ...  # другая модификация
    
    # Шаг 3: перевод обратно
    R = int(s, base)
    
    # Проверка условия
    if R < 12717:
        print(N, R)
```

---

## 💻 КОД НА C++ (все задачи)

### Задача 7

```cpp
#include <iostream>
using namespace std;

int main() {
    long long base = 94;
    for (long long x = 93; x >= 0; x--) {
        long long v1 = 4*base*base*base*base + 5*base*base*base + 0*base*base + x*base + 93;
        long long v2 = 8*base*base*base*base + 7*base*base*base + 9*base*base + x*base + 37;
        long long v3 = 2*base*base*base*base + 8*base*base*base + 5*base*base + x*base + 56;
        long long total = v1 + v2 + v3;
        if (total % 93 == 0) {
            cout << total / 31 << endl;
            break;
        }
    }
    return 0;
}
```

---

### Задача 12

```cpp
#include <iostream>
using namespace std;

int main() {
    long long base = 19;
    // 2ExG8: 2, E=14, x, G=16, 8
    // 6FxBG: 6, F=15, x, B=11, G=16
    for (long long x = 18; x >= 0; x--) {
        long long v1 = 2*base*base*base*base + 14*base*base*base + x*base*base + 16*base + 8;
        long long v2 = 6*base*base*base*base + 15*base*base*base + x*base*base + 11*base + 16;
        long long total = v1 + v2;
        if (total % 18 == 0) {
            cout << total / 9 << endl;
            break;
        }
    }
    return 0;
}
```

---

### Задача 6

```cpp
#include <iostream>
#include <vector>
using namespace std;

vector<int> toBase(long long n, int base) {
    vector<int> digits;
    if (n == 0) return {0};
    while (n > 0) {
        digits.push_back(n % base);
        n /= base;
    }
    // разворачиваем: от старшего к младшему
    vector<int> res(digits.rbegin(), digits.rend());
    return res;
}

int main() {
    long long expr = 5LL*13*13*13*13*13*13*13 + 2LL*13*13*13*13*13 + 3LL*13*13*13;
    for (long long x = 3999; x >= 1; x--) {
        long long val = expr - x;
        vector<int> digits = toBase(val, 13);
        int zeros = 0;
        for (int d : digits) if (d == 0) zeros++;
        if (zeros % 2 == 1) {
            cout << x << endl;
            break;
        }
    }
    return 0;
}
```

---

### Задача 22457 (семеричная, R < 12717)

```cpp
#include <iostream>
#include <string>
#include <algorithm>
using namespace std;

string toBase7(long long n) {
    if (n == 0) return "0";
    string s = "";
    while (n > 0) {
        s += to_string(n % 7);
        n /= 7;
    }
    reverse(s.begin(), s.end());
    return s;
}

long long fromBase7(const string& s) {
    long long res = 0;
    for (char c : s) {
        res = res * 7 + (c - '0');
    }
    return res;
}

int main() {
    long long maxN = 0;
    for (long long N = 1; N < 100000; N++) {
        string s = toBase7(N);
        int sum = 0;
        for (char c : s) sum += (c - '0');
        if (sum % 2 == 0) {
            s = s + "555";
        } else {
            s = "33" + s + "6";
        }
        long long R = fromBase7(s);
        if (R < 12717) {
            maxN = N;
        }
    }
    cout << maxN << endl;
    return 0;
}
```

---

### Задача 28926 (троичная, минимальное нечётное R > 520)

```cpp
#include <iostream>
#include <string>
#include <algorithm>
#include <climits>
using namespace std;

string toBase3(long long n) {
    if (n == 0) return "0";
    string s = "";
    while (n > 0) {
        s += to_string(n % 3);
        n /= 3;
    }
    reverse(s.begin(), s.end());
    return s;
}

long long fromBase3(const string& s) {
    long long res = 0;
    for (char c : s) {
        res = res * 3 + (c - '0');
    }
    return res;
}

int main() {
    long long minR = LLONG_MAX;
    for (long long N = 1; N < 100000; N++) {
        string s = toBase3(N);
        if (N % 3 == 0) {
            // дописываем две последние троичные цифры
            int len = s.length();
            s = s + s[len-2] + s[len-1];  // последние две цифры
            // но нужно быть осторожным: если s.length() < 2
            // для N=1: s="1", последние две... берём "11"? 
            // По примеру: 8=22_3 -> 2222_3, дописали "22" (последние две цифры "22")
            // 9=100_3 -> 10000_3, дописали "00"
        } else {
            int sum = 0;
            for (char c : s) sum += (c - '0');
            long long doubled = sum * 2;
            string add = toBase3(doubled);
            s = s + add;
        }
        long long R = fromBase3(s);
        if (R > 520 && R % 2 == 1 && R < minR) {
            minR = R;
        }
    }
    cout << minR << endl;
    return 0;
}
```

**Исправленная версия** (последние две цифры корректно):

```cpp
#include <iostream>
#include <string>
#include <algorithm>
#include <climits>
using namespace std;

string toBase3(long long n) {
    if (n == 0) return "0";
    string s = "";
    while (n > 0) {
        s += to_string(n % 3);
        n /= 3;
    }
    reverse(s.begin(), s.end());
    return s;
}

long long fromBase3(const string& s) {
    long long res = 0;
    for (char c : s) {
        res = res * 3 + (c - '0');
    }
    return res;
}

int main() {
    long long minR = LLONG_MAX;
    for (long long N = 1; N < 100000; N++) {
        string s = toBase3(N);
        if (N % 3 == 0) {
            // две последние цифры троичной записи N
            string last2 = s.substr(s.length() - 2);
            s = s + last2;
        } else {
            int sum = 0;
            for (char c : s) sum += (c - '0');
            long long doubled = sum * 2;
            string add = toBase3(doubled);
            s = s + add;
        }
        long long R = fromBase3(s);
        if (R > 520 && R % 2 == 1 && R < minR) {
            minR = R;
        }
    }
    cout << minR << endl;
    return 0;
}
```

---

### Задача 27760 (двоичная, N > 18, минимальное R)

```cpp
#include <iostream>
#include <string>
#include <algorithm>
#include <climits>
using namespace std;

string toBase2(long long n) {
    if (n == 0) return "0";
    string s = "";
    while (n > 0) {
        s += to_string(n % 2);
        n /= 2;
    }
    reverse(s.begin(), s.end());
    return s;
}

long long fromBase2(const string& s) {
    long long res = 0;
    for (char c : s) {
        res = res * 2 + (c - '0');
    }
    return res;
}

int main() {
    long long minR = LLONG_MAX;
    for (long long N = 19; N < 100000; N++) {
        string s = toBase2(N);
        if (N % 2 == 0) {
            s = "10" + s;
        } else {
            s = "1" + s + "01";
        }
        long long R = fromBase2(s);
        if (R < minR) {
            minR = R;
        }
    }
    cout << minR << endl;
    return 0;
}
```

---

## 📋 Сводная таблица

| Задача | Тип | Ключевая операция |
|--------|-----|-------------------|
| 7 | Перебор x, делимость | Раскладка по разрядам base=94 |
| 12 | Перебор x, делимость | Раскладка по разрядам base=19 |
| 6 | Перебор x, подсчёт нулей | Перевод в base=13 |
| 22457 | Перебор N, алгоритм | base=7, модификация строки |
| 28926 | Перебор N, алгоритм | base=3, модификация строки |
| 27760 | Перебор N, алгоритм | base=2, модификация строки |

**Общий принцип:** все задачи решаются **полным перебором** с проверкой условия. Диапазон перебора обычно не превышает 100 000, что выполняется мгновенно.
