# ИДЗ4, Карпова Софья Саналовна БПИ218

## Вариант 32.
## Задание:
Пляшущие человечки. На тайном собрании глав преступного мира города Лондона председатель собрания профессор Мориарти постановил: отныне
вся переписка между преступниками должна вестись тайнописью. В качестве
стандарта были выбраны «пляшущие человечки», шифр, в котором каждой
букве латинского алфавита соответствует хитроумный значок. Реализовать
многопоточное приложение, шифрующее исходный текст (в качестве ключа используется кодовая таблица, устанавливающая однозначное сoответствие между каждой буквой и каким-нибудь числом). Каждый поток шифрует свои кусочки текста. При решении использовать парадигму портфеля задач.

## Модель параллельных вычислений.
Модель параллеельных вычислений - парадигма портфель задач. То есть у нас есть некий портфель из которого разбираются задачи. Это мне и нужно реализовать


## Отчет на 8 баллов:
- 4 -5 баллов
Условия выполнены

- 6 - 8 баллов (выполнено)
1) Был реализован метод, который запускается при создании потока
2) Были реализованы ввод из строки (рандом) и чтение из файла.
По умолчанию запускается ввод с консоли.

## Входные данные:
Входные данные одно число, которое определяет количество потоков и слов в словаре.
## C++

Получилось реализовать то, что и было описаны. Каждый поток разбирает свою задачу и кодирует слово. Однако кодирование занимает не так много времени, поэтому всё происходит очень быстро.

```Cpp
#include <fstream>
#include <pthread.h>
#include <iostream>

// В переменных у нас имеется:
// Позиция в словаре, которую нужно зашифровать
// Наш тред мьютекс, чтобы потоки не накладывались друг на друга
// Счётчик наших людей и сам словарь
int nRow = 0;
const int n = 20;
std::string codeWords[n];
pthread_mutex_t mutex;
int cntPeople = 0;

// Шифровка слов
void *codingWord(void *param)
{
  // Закрываем потом, чтобы другие в это время не перезаписывали
  pthread_mutex_lock(&mutex);
  std::string word = codeWords[nRow];
  word[0] += 60;
  word[1] += 60;
  word[2] += 60;
  word[3] += 60;
  std::cout << "Old word: " << codeWords[nRow] << " | New word: " << word << '\n';
  codeWords[nRow] = word;
  nRow++;
  cntPeople++;
  pthread_mutex_unlock(&mutex);
}

int main(int argc, char** argv)
{
  // Тут мы будем понимать с чем нам надо работать, либо с рандомным выводом, либо с вводом из консоли
  int typeOfCode;
  if (argc == 2) {
    // Генерация рандомного числа на которое мы будем прибавлять
    char* arg = argv[1];
    int seed = atoi(arg);
    srand(seed);
    typeOfCode = rand() % 32;
    std::string word = "AAA";
    for (auto & codeWord : codeWords) {
      codeWord = word;
      word[0] += typeOfCode;
      word[1] += typeOfCode;
      word[2] += typeOfCode;
      word[3] += typeOfCode;
    }
    // Чтение из файла
  } else if (argc == 3) {
    std::ifstream ifstream(argv[1]);
    ifstream >> typeOfCode;
  } else {
    // Ввод из консоли
    std::cout << "Enter n:" << '\n';
    std::cin >> typeOfCode;
  }

  // Ресайзим массив до того значения, которое получили
  codeWords->resize(typeOfCode);
  std::string firstWord = "AAAA";
  // Заполняем наш массив
  for (int i = 0; i < n; ++i) {
    codeWords[i] = firstWord;
    firstWord[0] += 1;
    firstWord[1] += 1;
    firstWord[2] += 1;
    firstWord[3] += 1;
  }
  // Поток кодировщиков и инициализация мьютекса
  pthread_t peoples[typeOfCode];
  pthread_mutex_init(&mutex,0);

  // Портфель задач. Каждый человек берет по задаче из портфеля и выполняет ее.
  for (int i = 0; i < n; ++i) {
    pthread_create(&peoples[i], 0, codingWord, (void *) cntPeople);
  }
  // Тут происходит джоин
  for (int i = 0; i < n; ++i) {
    pthread_join(peoples[i], 0);
    std::cout << "People - " << i << " finished his work" << '\n';
  }
  pthread_mutex_destroy(&mutex);
  return 0;
}
```


## Вывод:
Вот аткой получился вывод. Некоторые английские буквы кодируются на русские.
```
Enter n:
10
Old word: AAAA | New word: }}}}
Old word: BBBB | New word: ~~~~
People - Old word: CCCC | New word: ⌂⌂⌂⌂
0 finished his work
Old word: DDDD | New word: АААА
People - 1 finished his work
Old word: EEEE | New word: ББББ
Old word: FFFF | New word: ВВВВ
Old word: GGGG | New word: ГГГГ
Old word: HHHH | New word: ДДДД
Old word: IIII | New word: ЕЕЕЕ
Old word: JJJJ | New word: ЖЖЖЖ
Old word: KKKK | New word: ЗЗЗЗ
Old word: LLLL | New word: ИИИИ
Old word: MMMM | New word: ЙЙЙЙ
Old word: NNNN | New word: КККК
Old word: OOOO | New word: ЛЛЛЛ
People - 2 finished his work
Old word: PPPP | New word: ММММ
People - 3 finished his work
Old word: QQQQ | New word: НННН
People - 4 finished his work
Old word: RRRR | New word: ОООО
People - 5 finished his work
Old word: SSSS | New word: ПППП
People - 6 finished his work
Old word: TTTT | New word: РРРР
People - 7 finished his work
People - 8 finished his work
People - 9 finished his work
People - 10 finished his work
People - 11 finished his work
People - 12 finished his work
People - 13 finished his work
People - 14 finished his work
People - 15 finished his work
People - 16 finished his work
People - 17 finished his work
People - 18 finished his work
People - 19 finished his work
```