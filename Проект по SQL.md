# Проект по SQL

## Описание проекта

Коронавирус застал мир врасплох, изменив привычный порядок вещей. На какое-то время жители городов перестали выходить на улицу, посещать кафе и торговые центры. Зато стало больше времени для книг. Это заметили стартаперы — и бросились создавать приложения для тех, кто любит читать.

Наша компания решила быть на волне и купила крупный сервис для чтения книг по подписке. Ваша первая задача как аналитика — проанализировать базу данных.

В ней — информация о книгах, издательствах, авторах, а также пользовательские обзоры книг. Эти данные помогут сформулировать ценностное предложение для нового продукта.

### Описание данных

#### Таблица books. Данные о книгах

- **book_id** — идентификатор книги;
- **author_id** — идентификатор автора;
- **title** — название книги;
- **num_pages** — количество страниц;
- **publication_date** — дата публикации книги;
- **publisher_id** — идентификатор издателя.

#### Таблица authors. Данные об авторах

- **author_id** — идентификатор автора;
- **author** — имя автора.

#### Таблица publishers. Данные об издательствах

- **publisher_id** — идентификатор издательства;
- **publisher** — название издательства.

#### Таблица ratings. Данные о пользовательских оценках книг

- **rating_id** — идентификатор оценки;
- **book_id** — идентификатор книги;
- **username** — имя пользователя, оставившего оценку;
- **rating** — оценка книги.

#### Таблица reviews. Данные о пользовательских обзорах

- **review_id** — идентификатор обзора;
- **book_id** — идентификатор книги;
- **username** — имя автора обзора;
- **text** — текст обзора.

### Цель и задачи исследования

**Цель:**
- Проанализировать базу данных и сформулировать ценностное предложение для нового продукта.

**Задачи:**
1. Посчитать сколько книг вышло после 1 января 2000 года;


2. Для каждой книги посчитать количество обзоров и среднюю оценку;


3. Определить издательство, которое выпустило наибольшее число книг толще 50-ти страниц, с целью исключения из анализа брошюр;


4. Определить автора с самой высокой средней оценкой книг — учитывайте только книги с 50-ти и более оценками;


5. Посчитать среднее количество обзоров от пользователей, которые поставили больше 48 оценок.

## Доступ к базе данных

Импорт библиотек:


```python
import pandas as pd
from sqlalchemy import text, create_engine
```

Установка необходимых параметров:


```python
db_config = {'user': 'praktikum_student',
'pwd': 'Sdf4$2;d-d30pp',
'host': 'rc1b-wcoijxj3yxfsf3fs.mdb.yandexcloud.net',
'port': 6432,
'db': 'data-analyst-final-project-db'}
connection_string = 'postgresql://{user}:{pwd}@{host}:{port}/{db}'.format(**db_config)
```

Сохранение коннектора:


```python
engine = create_engine(connection_string, connect_args = {'sslmode':'require'})
```

*Выполнения SQL-запросов в Pandas (шаблон):*


```python
query = '''
SELECT *
FROM books
LIMIT 5
'''
con = engine.connect()
pd.io.sql.read_sql(sql = text(query), con = con)
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>book_id</th>
      <th>author_id</th>
      <th>title</th>
      <th>num_pages</th>
      <th>publication_date</th>
      <th>publisher_id</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>1</td>
      <td>546</td>
      <td>'Salem's Lot</td>
      <td>594</td>
      <td>2005-11-01</td>
      <td>93</td>
    </tr>
    <tr>
      <th>1</th>
      <td>2</td>
      <td>465</td>
      <td>1 000 Places to See Before You Die</td>
      <td>992</td>
      <td>2003-05-22</td>
      <td>336</td>
    </tr>
    <tr>
      <th>2</th>
      <td>3</td>
      <td>407</td>
      <td>13 Little Blue Envelopes (Little Blue Envelope...</td>
      <td>322</td>
      <td>2010-12-21</td>
      <td>135</td>
    </tr>
    <tr>
      <th>3</th>
      <td>4</td>
      <td>82</td>
      <td>1491: New Revelations of the Americas Before C...</td>
      <td>541</td>
      <td>2006-10-10</td>
      <td>309</td>
    </tr>
    <tr>
      <th>4</th>
      <td>5</td>
      <td>125</td>
      <td>1776</td>
      <td>386</td>
      <td>2006-07-04</td>
      <td>268</td>
    </tr>
  </tbody>
</table>
</div>



## Исследование таблиц

Выведем первые строки всех таблиц из базы данных.

### Таблица books


```python
books = '''
SELECT *
FROM books
LIMIT 10
'''
pd.io.sql.read_sql(sql = text(books), con = con)
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>book_id</th>
      <th>author_id</th>
      <th>title</th>
      <th>num_pages</th>
      <th>publication_date</th>
      <th>publisher_id</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>1</td>
      <td>546</td>
      <td>'Salem's Lot</td>
      <td>594</td>
      <td>2005-11-01</td>
      <td>93</td>
    </tr>
    <tr>
      <th>1</th>
      <td>2</td>
      <td>465</td>
      <td>1 000 Places to See Before You Die</td>
      <td>992</td>
      <td>2003-05-22</td>
      <td>336</td>
    </tr>
    <tr>
      <th>2</th>
      <td>3</td>
      <td>407</td>
      <td>13 Little Blue Envelopes (Little Blue Envelope...</td>
      <td>322</td>
      <td>2010-12-21</td>
      <td>135</td>
    </tr>
    <tr>
      <th>3</th>
      <td>4</td>
      <td>82</td>
      <td>1491: New Revelations of the Americas Before C...</td>
      <td>541</td>
      <td>2006-10-10</td>
      <td>309</td>
    </tr>
    <tr>
      <th>4</th>
      <td>5</td>
      <td>125</td>
      <td>1776</td>
      <td>386</td>
      <td>2006-07-04</td>
      <td>268</td>
    </tr>
    <tr>
      <th>5</th>
      <td>6</td>
      <td>257</td>
      <td>1st to Die (Women's Murder Club  #1)</td>
      <td>424</td>
      <td>2005-05-20</td>
      <td>116</td>
    </tr>
    <tr>
      <th>6</th>
      <td>7</td>
      <td>258</td>
      <td>2nd Chance (Women's Murder Club  #2)</td>
      <td>400</td>
      <td>2005-05-20</td>
      <td>116</td>
    </tr>
    <tr>
      <th>7</th>
      <td>8</td>
      <td>260</td>
      <td>4th of July (Women's Murder Club  #4)</td>
      <td>448</td>
      <td>2006-06-01</td>
      <td>318</td>
    </tr>
    <tr>
      <th>8</th>
      <td>9</td>
      <td>563</td>
      <td>A Beautiful Mind</td>
      <td>461</td>
      <td>2002-02-04</td>
      <td>104</td>
    </tr>
    <tr>
      <th>9</th>
      <td>10</td>
      <td>445</td>
      <td>A Bend in the Road</td>
      <td>341</td>
      <td>2005-04-01</td>
      <td>116</td>
    </tr>
  </tbody>
</table>
</div>



### Таблица authors


```python
authors = '''
SELECT *
FROM authors
LIMIT 10
'''
pd.io.sql.read_sql(sql = text(authors), con = con)
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>author_id</th>
      <th>author</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>1</td>
      <td>A.S. Byatt</td>
    </tr>
    <tr>
      <th>1</th>
      <td>2</td>
      <td>Aesop/Laura Harris/Laura Gibbs</td>
    </tr>
    <tr>
      <th>2</th>
      <td>3</td>
      <td>Agatha Christie</td>
    </tr>
    <tr>
      <th>3</th>
      <td>4</td>
      <td>Alan Brennert</td>
    </tr>
    <tr>
      <th>4</th>
      <td>5</td>
      <td>Alan Moore/David   Lloyd</td>
    </tr>
    <tr>
      <th>5</th>
      <td>6</td>
      <td>Alan Paton</td>
    </tr>
    <tr>
      <th>6</th>
      <td>7</td>
      <td>Albert Camus/Justin O'Brien</td>
    </tr>
    <tr>
      <th>7</th>
      <td>8</td>
      <td>Aldous Huxley</td>
    </tr>
    <tr>
      <th>8</th>
      <td>9</td>
      <td>Aldous Huxley/Christopher Hitchens</td>
    </tr>
    <tr>
      <th>9</th>
      <td>10</td>
      <td>Aleksandr Solzhenitsyn/H.T. Willetts</td>
    </tr>
  </tbody>
</table>
</div>



### Таблица publishers


```python
publishers = '''
SELECT *
FROM publishers
LIMIT 10
'''
pd.io.sql.read_sql(sql = text(publishers), con = con)
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>publisher_id</th>
      <th>publisher</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>1</td>
      <td>Ace</td>
    </tr>
    <tr>
      <th>1</th>
      <td>2</td>
      <td>Ace Book</td>
    </tr>
    <tr>
      <th>2</th>
      <td>3</td>
      <td>Ace Books</td>
    </tr>
    <tr>
      <th>3</th>
      <td>4</td>
      <td>Ace Hardcover</td>
    </tr>
    <tr>
      <th>4</th>
      <td>5</td>
      <td>Addison Wesley Publishing Company</td>
    </tr>
    <tr>
      <th>5</th>
      <td>6</td>
      <td>Aladdin</td>
    </tr>
    <tr>
      <th>6</th>
      <td>7</td>
      <td>Aladdin Paperbacks</td>
    </tr>
    <tr>
      <th>7</th>
      <td>8</td>
      <td>Albin Michel</td>
    </tr>
    <tr>
      <th>8</th>
      <td>9</td>
      <td>Alfred A. Knopf</td>
    </tr>
    <tr>
      <th>9</th>
      <td>10</td>
      <td>Alfred A. Knopf Books for Young Readers</td>
    </tr>
  </tbody>
</table>
</div>



### Таблица ratings


```python
ratings = '''
SELECT *
FROM ratings
LIMIT 10
'''
pd.io.sql.read_sql(sql = text(ratings), con = con)
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>rating_id</th>
      <th>book_id</th>
      <th>username</th>
      <th>rating</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>1</td>
      <td>1</td>
      <td>ryanfranco</td>
      <td>4</td>
    </tr>
    <tr>
      <th>1</th>
      <td>2</td>
      <td>1</td>
      <td>grantpatricia</td>
      <td>2</td>
    </tr>
    <tr>
      <th>2</th>
      <td>3</td>
      <td>1</td>
      <td>brandtandrea</td>
      <td>5</td>
    </tr>
    <tr>
      <th>3</th>
      <td>4</td>
      <td>2</td>
      <td>lorichen</td>
      <td>3</td>
    </tr>
    <tr>
      <th>4</th>
      <td>5</td>
      <td>2</td>
      <td>mariokeller</td>
      <td>2</td>
    </tr>
    <tr>
      <th>5</th>
      <td>6</td>
      <td>3</td>
      <td>johnsonamanda</td>
      <td>4</td>
    </tr>
    <tr>
      <th>6</th>
      <td>7</td>
      <td>3</td>
      <td>scotttamara</td>
      <td>5</td>
    </tr>
    <tr>
      <th>7</th>
      <td>8</td>
      <td>3</td>
      <td>lesliegibbs</td>
      <td>5</td>
    </tr>
    <tr>
      <th>8</th>
      <td>9</td>
      <td>4</td>
      <td>abbottjames</td>
      <td>5</td>
    </tr>
    <tr>
      <th>9</th>
      <td>10</td>
      <td>4</td>
      <td>valenciaanne</td>
      <td>4</td>
    </tr>
  </tbody>
</table>
</div>



### Таблица reviews


```python
reviews = '''
SELECT *
FROM reviews
LIMIT 10
'''
pd.io.sql.read_sql(sql = text(reviews), con = con)
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>review_id</th>
      <th>book_id</th>
      <th>username</th>
      <th>text</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>1</td>
      <td>1</td>
      <td>brandtandrea</td>
      <td>Mention society tell send professor analysis. ...</td>
    </tr>
    <tr>
      <th>1</th>
      <td>2</td>
      <td>1</td>
      <td>ryanfranco</td>
      <td>Foot glass pretty audience hit themselves. Amo...</td>
    </tr>
    <tr>
      <th>2</th>
      <td>3</td>
      <td>2</td>
      <td>lorichen</td>
      <td>Listen treat keep worry. Miss husband tax but ...</td>
    </tr>
    <tr>
      <th>3</th>
      <td>4</td>
      <td>3</td>
      <td>johnsonamanda</td>
      <td>Finally month interesting blue could nature cu...</td>
    </tr>
    <tr>
      <th>4</th>
      <td>5</td>
      <td>3</td>
      <td>scotttamara</td>
      <td>Nation purpose heavy give wait song will. List...</td>
    </tr>
    <tr>
      <th>5</th>
      <td>6</td>
      <td>3</td>
      <td>lesliegibbs</td>
      <td>Analysis no several cause international.</td>
    </tr>
    <tr>
      <th>6</th>
      <td>7</td>
      <td>4</td>
      <td>valenciaanne</td>
      <td>One there cost another. Say type save. With pe...</td>
    </tr>
    <tr>
      <th>7</th>
      <td>8</td>
      <td>4</td>
      <td>abbottjames</td>
      <td>Within enough mother. There at system full rec...</td>
    </tr>
    <tr>
      <th>8</th>
      <td>9</td>
      <td>5</td>
      <td>npowers</td>
      <td>Thank now focus realize economy focus fly. Ite...</td>
    </tr>
    <tr>
      <th>9</th>
      <td>10</td>
      <td>5</td>
      <td>staylor</td>
      <td>Game push lot reduce where remember. Including...</td>
    </tr>
  </tbody>
</table>
</div>



**Вывод:**

Так как задачи по предобработки нет, то мы заочно предполагаем, что все данные полностью подготовленны для решения задач.

Вся необходимая информация по таблицам есть в разделе "Описание данных".

Все внешние ключи имеют целочисленный тип данных без пропусков. С таблицами можно работать.

## Решение задач

### Посчитать сколько книг вышло после 1 января 2000 года

Для решения задачи необходимо:
- Указать условие для таблицы **books**, согласно которому все книги, выпущенные до 1 января 2000 года исключаются из таблицы;
- После фильтрации данных посчитать общее количество книг;
- Отобразить данную информацию.


```python
task_1 = '''
SELECT COUNT (title) AS total_number_books
FROM books
WHERE publication_date > '2000-01-01'
'''
pd.io.sql.read_sql(sql = text(task_1), con = con)
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>total_number_books</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>819</td>
    </tr>
  </tbody>
</table>
</div>



**Вывод:**

В результате решения данной задачи мы выяснили, что после 1 января 2000 года вышло 819 книг.

### Для каждой книги посчитать количество обзоров и среднюю оценку

Для решения задачи необходимо:
- На основе данных таблицы **reviews** создать отдельную таблицу с информацией о количестве обзоров книг, сгруппировав их по book_id;
- На основе данных таблицы **ratings**, создать отдельную таблицу с информацией о средних оценках, сгруппировав их по book_id;
- Объединить ранее созданные таблицы в одну по общему ключу book_id;
- На основе данных полученной таблицы, создать отдельную таблицу с наименованием книг, взятой из таблицы **books**, а также с количеством обзоров книг и информацией о средних оценках;
- Отсортировать таблицу по убыванию средней оценки книг;
- Отобразить данную информацию.


```python
task_2 = '''
WITH
n_reviews AS (SELECT book_id,
                     COUNT(username) AS number_reviews
              FROM reviews
              GROUP BY book_id),
av_rating AS (SELECT book_id,
                     AVG(rating) AS average_rating
              FROM ratings
              GROUP BY book_id)
SELECT books.title,
       n_reviews.number_reviews,
       av_rating.average_rating
FROM books
LEFT JOIN n_reviews ON books.book_id = n_reviews.book_id
LEFT JOIN av_rating ON books.book_id = av_rating.book_id
ORDER BY average_rating DESC
'''
pd.io.sql.read_sql(sql = text(task_2), con = con)
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>title</th>
      <th>number_reviews</th>
      <th>average_rating</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>Pop Goes the Weasel (Alex Cross  #5)</td>
      <td>2.0</td>
      <td>5.00</td>
    </tr>
    <tr>
      <th>1</th>
      <td>The Ghost Map: The Story of London's Most Terr...</td>
      <td>2.0</td>
      <td>5.00</td>
    </tr>
    <tr>
      <th>2</th>
      <td>In the Hand of the Goddess (Song of the Liones...</td>
      <td>2.0</td>
      <td>5.00</td>
    </tr>
    <tr>
      <th>3</th>
      <td>Tai-Pan (Asian Saga  #2)</td>
      <td>2.0</td>
      <td>5.00</td>
    </tr>
    <tr>
      <th>4</th>
      <td>How to Be a Domestic Goddess: Baking and the A...</td>
      <td>1.0</td>
      <td>5.00</td>
    </tr>
    <tr>
      <th>...</th>
      <td>...</td>
      <td>...</td>
      <td>...</td>
    </tr>
    <tr>
      <th>995</th>
      <td>The World Is Flat: A Brief History of the Twen...</td>
      <td>3.0</td>
      <td>2.25</td>
    </tr>
    <tr>
      <th>996</th>
      <td>Junky</td>
      <td>2.0</td>
      <td>2.00</td>
    </tr>
    <tr>
      <th>997</th>
      <td>His Excellency: George Washington</td>
      <td>2.0</td>
      <td>2.00</td>
    </tr>
    <tr>
      <th>998</th>
      <td>Drowning Ruth</td>
      <td>3.0</td>
      <td>2.00</td>
    </tr>
    <tr>
      <th>999</th>
      <td>Harvesting the Heart</td>
      <td>2.0</td>
      <td>1.50</td>
    </tr>
  </tbody>
</table>
<p>1000 rows × 3 columns</p>
</div>



В результате решения данной задачи мы составили таблицу, в которой отображена информация по каждой из 1000 книг, а именно:
- Количество обзоров;
- Средняя оценка.

### Определить издательство, которое выпустило наибольшее число книг толще 50-ти страниц, с целью исключения из анализа брошюр

Для решения задачи необходимо:
- Объединить таблицы **books** и **publishers** в одну по ключу publisher_id, оставив в таблице только те издательства, у которых выпущенные книг имеют свыше 50-ти страниц;
- На основе данных полученной таблицы, создать отдельную таблицу с наименованием издательств, а также суммарным количеством их книг;
- Сгруппировать таблицу по убыванию количества книг и составить ТОП-3 издательств по данному значению;
- Отобразить данную информацию.


```python
task_3 = '''
SELECT publishers.publisher AS publisher,
       COUNT(DISTINCT books.title) AS number_books
FROM books
LEFT JOIN publishers ON publishers.publisher_id = books.publisher_id
WHERE books.num_pages > 50
GROUP BY publisher
ORDER BY number_books DESC
LIMIT 3;
'''
pd.io.sql.read_sql(sql = text(task_3), con = con)
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>publisher</th>
      <th>number_books</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>Penguin Books</td>
      <td>42</td>
    </tr>
    <tr>
      <th>1</th>
      <td>Vintage</td>
      <td>31</td>
    </tr>
    <tr>
      <th>2</th>
      <td>Grand Central Publishing</td>
      <td>25</td>
    </tr>
  </tbody>
</table>
</div>



**Вывод:**

В результате решения данной задачи мы выяснили, что наибольшее количество книг, превышающее 50 страниц, принадлежит издательству Penguin Books.

### Определить автора с самой высокой средней оценкой книг — учитывайте только книги с 50-ти и более оценками

Для решения задачи необходимо:
- Объединить таблицы **ratings** и **books** по ключу book_id;
- Объединить таблицы **books** и **authors** по ключу author_id;
- Вывести авторов и среднюю оценку их книг в отдельную таблицу, учитывая только те книги, которые имеют по 50 и более оценок;
- Отсортировать по убыванию средней оценки и оставить ТОП-3;
- Отобразить данную информацию.


```python
task_4 = '''
SELECT authors.author,
       AVG(ratings.rating) AS average_book_rating
FROM authors
RIGHT JOIN books ON books.author_id = authors.author_id
LEFT JOIN ratings ON ratings.book_id = books.book_id
GROUP BY author
HAVING COUNT(ratings.book_id) > 50
ORDER BY AVG(ratings.rating) DESC
LIMIT 3;
'''
pd.io.sql.read_sql(sql = text(task_4), con = con)
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>author</th>
      <th>average_book_rating</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>J.K. Rowling/Mary GrandPré</td>
      <td>4.288462</td>
    </tr>
    <tr>
      <th>1</th>
      <td>Agatha Christie</td>
      <td>4.283019</td>
    </tr>
    <tr>
      <th>2</th>
      <td>Markus Zusak/Cao Xuân Việt Khương</td>
      <td>4.264151</td>
    </tr>
  </tbody>
</table>
</div>



**Вывод:**

В результате решения данной задачи мы выяснили, что самая высокая средняя оценка книг у автора J.K. Rowling/Mary GrandPré.

### Посчитать среднее количество обзоров от пользователей, которые поставили больше 48 оценок

Для решения задачи необходимо:
- На основе данных таблицы **ratings**, создать отдельную таблицу с информацией о пользователях, которые поставили более 48 оценок;
- На основе данных таблицы **reviews**, а также ранее созданой таблицы, создать отдельную таблицу с информацией о количестве обзоров пользователей;
- На основе данных созданной таблицы вывести среднее количество обзоров от пользователей;
- Отобразить данную информацию.


```python
task_5 = '''
SELECT AVG(review.number_reviews) AS average_number_reviews
FROM (SELECT COUNT(review_id) AS number_reviews
      FROM reviews
      WHERE username IN (SELECT username
                         FROM ratings
                         GROUP BY username
                         HAVING COUNT(ratings) > 48)
      GROUP BY username) AS review;
'''
pd.io.sql.read_sql(sql = text(task_5), con = con)
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>average_number_reviews</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>24.0</td>
    </tr>
  </tbody>
</table>
</div>



**Вывод:**

В результате решения данной задачи мы выяснили, что среднее количество обзоров пользователей, которые поставили больше 48 оценок, равно 24.

## Вывод

В результате решения задач :
- После 1 января 2000 года вышло 819 книг;
- Наибольшее количество книг, превышающее 50 страниц, принадлежит издательству Penguin Books;
- Самая высокая средняя оценка книг у автора J.K. Rowling/Mary GrandPré;
- Среднее количество обзоров пользователей, которые поставили больше 48 оценок, равно 24.

Также была составлена таблица, в которой отображена информация по каждой из 1000 книг, а именно:
- Количество обзоров;
- Средняя оценка.

На основе данной информации нелегко составить ценностное предложение для нового продукта, т.к. данных недостаточно для объективного анализа.

Единственное, что могу могу предложить, так это то, что нужно чаще выставлять на сервис книги автора J.K. Rowling/Mary GrandPré и просить пользователей оставлять больше обзоров на книги, т.к. многие ориентируются на отзывы, и хороший отзыв может послужить неплохим триггером к прочтению.

*Ну и наверное закончу задание цитатой Великого классика, Фёдора Михайловича:*

"***Учитесь и читайте. Читайте книги серьезные. Жизнь сделает остальное.***"
