# Представление текста в виде тензоров

## [Тест перед лекцией](https://red-field-0a6ddfd03.1.azurestaticapps.net/quiz/113)

## Классификация текста

В первой части этого раздела мы сосредоточимся на задаче **классификации текста**. Мы будем использовать набор данных [AG News](https://www.kaggle.com/amananandrai/ag-news-classification-dataset), который содержит новостные статьи, такие как следующая:

* Категория: Наука/Технологии
* Заголовок: Компания Кентукки получает грант на изучение пептидов (AP)
* Текст: AP - Компания, основанная исследователем в области химии в Университете Луисвилла, получила грант на разработку...

Наша цель будет заключаться в том, чтобы классифицировать новость в одну из категорий на основе текста.

## Представление текста

Если мы хотим решать задачи обработки естественного языка (NLP) с помощью нейронных сетей, нам нужно найти способ представления текста в виде тензоров. Компьютеры уже представляют текстовые символы в виде чисел, которые отображаются на экране с использованием кодировок, таких как ASCII или UTF-8.

<img alt="Изображение, показывающее диаграмму, сопоставляющую символ с ASCII и двоичным представлением" src="images/ascii-character-map.png" width="50%"/>

> [Источник изображения](https://www.seobility.net/en/wiki/ASCII)

Как люди, мы понимаем, что каждая буква **представляет**, и как все символы соединяются, чтобы образовать слова предложения. Однако компьютеры сами по себе не обладают таким пониманием, и нейронной сети нужно учиться значению во время обучения.

Таким образом, мы можем использовать различные подходы для представления текста:

* **Представление на уровне символов**, когда мы представляем текст, рассматривая каждый символ как число. Если в нашем текстовом корпусе есть *C* различных символов, слово *Hello* будет представлено тензором размером 5x*C. Каждая буква будет соответствовать столбцу тензора в кодировке one-hot.
* **Представление на уровне слов**, при котором мы создаем **словарь** всех слов в нашем тексте, а затем представляем слова с помощью кодировки one-hot. Этот подход в какой-то мере лучше, потому что каждая буква сама по себе не имеет большого значения, и, используя более высокоуровневые семантические концепции - слова - мы упрощаем задачу для нейронной сети. Однако, учитывая большой размер словаря, нам нужно иметь дело с высокоразмерными разреженными тензорами.

Независимо от представления, сначала нам нужно преобразовать текст в последовательность **токенов**, где один токен может быть символом, словом или иногда даже частью слова. Затем мы преобразуем токен в число, обычно используя **словарь**, и это число может быть подано в нейронную сеть с использованием кодировки one-hot.

## N-граммы

В естественном языке точное значение слов можно определить только в контексте. Например, значения *нейронной сети* и *рыболовной сети* совершенно разные. Один из способов учесть это - строить нашу модель на парах слов, рассматривая пары слов как отдельные токены словаря. Таким образом, предложение *Мне нравится ходить на рыбалку* будет представлено следующей последовательностью токенов: *Мне нравится*, *нравится ходить*, *ходить на*, *на рыбалку*. Проблема с этим подходом заключается в том, что размер словаря значительно увеличивается, и такие комбинации, как *идти на рыбалку* и *идти за покупками*, представлены различными токенами, которые не имеют семантического сходства, несмотря на один и тот же глагол.

В некоторых случаях мы также можем рассмотреть использование триграмм - комбинаций из трех слов. Таким образом, этот подход часто называют **n-граммами**. Также имеет смысл использовать n-граммы с представлением на уровне символов, в этом случае n-граммы будут примерно соответствовать различным слогам.

## Мешок слов и TF/IDF

При решении задач, таких как классификация текста, нам нужно уметь представлять текст в виде одного вектора фиксированного размера, который мы будем использовать в качестве входа для финального плотного классификатора. Один из самых простых способов сделать это - объединить все индивидуальные представления слов, например, добавив их. Если мы сложим кодировки one-hot каждого слова, мы получим вектор частот, показывающий, сколько раз каждое слово встречается в тексте. Такое представление текста называется **мешок слов** (BoW).

<img src="images/bow.png" width="90%"/>

> Изображение автора

BoW в основном представляет, какие слова появляются в тексте и в каких количествах, что действительно может быть хорошим индикатором того, о чем текст. Например, новостная статья о политике, вероятно, будет содержать слова, такие как *президент* и *страна*, в то время как научная публикация будет иметь что-то вроде *коллайдер*, *открыто*, и т.д. Таким образом, частоты слов могут во многих случаях быть хорошим индикатором содержания текста.

Проблема с BoW заключается в том, что определенные общие слова, такие как *и*, *является* и т.д., появляются в большинстве текстов и имеют самые высокие частоты, затмевая слова, которые действительно важны. Мы можем снизить важность этих слов, принимая во внимание частоту, с которой слова встречаются в всей коллекции документов. Это основная идея подхода TF/IDF, который рассматривается более подробно в ноутбуках, приложенных к этому уроку.

Тем не менее, ни один из этих подходов не может полностью учитывать **семантику** текста. Нам нужны более мощные модели нейронных сетей для этого, о которых мы поговорим позже в этом разделе.

## ✍️ Упражнения: Представление текста

Продолжите обучение в следующих ноутбуках:

* [Представление текста с помощью PyTorch](../../../../../lessons/5-NLP/13-TextRep/TextRepresentationPyTorch.ipynb)
* [Представление текста с помощью TensorFlow](../../../../../lessons/5-NLP/13-TextRep/TextRepresentationTF.ipynb)

## Заключение

На данный момент мы изучили техники, которые могут добавлять вес частоты к различным словам. Однако они не способны представлять значение или порядок. Как сказал знаменитый лингвист Дж. Р. Фирт в 1935 году: "Полное значение слова всегда контекстуально, и никакое изучение значения вне контекста не может восприниматься всерьез." Позже в курсе мы научимся захватывать контекстуальную информацию из текста с помощью языкового моделирования.

## 🚀 Задача

Попробуйте выполнить некоторые другие упражнения, используя мешок слов и различные модели данных. Возможно, вас вдохновит этот [конкурс на Kaggle](https://www.kaggle.com/competitions/word2vec-nlp-tutorial/overview/part-1-for-beginners-bag-of-words)

## [Тест после лекции](https://red-field-0a6ddfd03.1.azurestaticapps.net/quiz/213)

## Обзор и самообучение

Практикуйте свои навыки с использованием встраиваний текста и техник мешка слов на [Microsoft Learn](https://docs.microsoft.com/learn/modules/intro-natural-language-processing-pytorch/?WT.mc_id=academic-77998-cacaste)

## [Задание: Ноутбуки](assignment.md)

**Отказ от ответственности**:  
Этот документ был переведен с использованием машинных AI-сервисов перевода. Хотя мы стремимся к точности, пожалуйста, имейте в виду, что автоматические переводы могут содержать ошибки или неточности. Оригинальный документ на его родном языке следует считать авторитетным источником. Для критически важной информации рекомендуется профессиональный человеческий перевод. Мы не несем ответственности за любые недоразумения или неправильные интерпретации, возникающие в результате использования этого перевода.