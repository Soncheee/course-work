# Курсовая работа
## Пакеты: src, tests, папка data
### Реализованы функции для простого поиска, трат по категориям и главной страницы
### *Функция трат по категориям*
```
    def spending_by_category(transactions: pd.DataFrame, category: str, date: Optional[str] = None) -> dict:
    """Принимает на вход список транзакций, категорию и дату в формате %Y-%m-%d
    и выводит отфильтрованный список с нужными категориями и с начала введённой даты до 3 месяцев вперёд"""
    logger.info("Проверяем дана ли дата и переводим её в нужный формат")
    if date is None:
        date = datetime.datetime.now()
    else:
        date = datetime.datetime.strptime(date, "%Y-%m-%d")

    date_2 = date - timedelta(days=90)
    logger.info(f"Получаем что дата через 3 месяца от даты {date} равна {date_2}")
    transactions["Дата платежа"] = pd.to_datetime(transactions["Дата платежа"], format="%d.%m.%Y", dayfirst=True)
    logger.info("Фильтруем список транзакций по дате и категории")
    filter_transactions = transactions[
        (category == transactions["Категория"])
        & (transactions["Дата платежа"] >= date_2)
        & (transactions["Дата платежа"] <= date)
    ]
    summ = 0
    for i in filter_transactions["Сумма операции"]:
        if i < 0:
            summ += i
    logger.info(f"Находим сумму трат за 3 месяца от даты {date} с нужной категорией")
    result = {category: round(summ, 2)}
    return result
```

### В файле user_settings.json задаются валюты и акции для отображения на веб-страницах 
### В reports.py содержится декоратор для сохранения результата функции-отчета в указанный файл

```
def report_file_write(filename: str = "report.txt"):
    """Декоратор для сохранения результата функции-отчета в указанный файл."""

    def decorator(func):
        @wraps(func)
        def wrapper(*args, **kwargs):
            result = func(*args, **kwargs)
            base_dir = os.path.dirname(os.path.abspath(__file__))
            report_dir = os.path.join(base_dir, "..", "reports")
            report_file_path = os.path.join(report_dir, filename)
            logger.info(
                f"Создаём и открываем файл по пути {report_file_path} и "
                f"записываем результат функции {func.__name__} = {result}"
            )
            with open(report_file_path, "w", encoding="utf-8") as file:
                file.write(str(result))
            return result

        return wrapper

    return decorator
```
### Также в reports.py есть функция, которая фильтрует список по категориям и возвращает его
```
@report_file_write(filename="my_report.txt")
def spending_by_category(transactions: pd.DataFrame, category: str, date: Optional[str] = None) -> dict:
    """Принимает на вход список транзакций, категорию и дату в формате %Y-%m-%d
    и выводит отфильтрованный список с нужными категориями и с начала введённой даты до 3 месяцев вперёд"""
    logger.info("Проверяем дана ли дата и переводим её в нужный формат")
    if date is None:
        date = datetime.datetime.now()
    else:
        date = datetime.datetime.strptime(date, "%Y-%m-%d")

    date_2 = date - timedelta(days=90)
    logger.info(f"Получаем что дата через 3 месяца от даты {date} равна {date_2}")
    transactions["Дата платежа"] = pd.to_datetime(transactions["Дата платежа"], format="%d.%m.%Y", dayfirst=True)
    logger.info("Фильтруем список транзакций по дате и категории")
    filter_transactions = transactions[
        (category == transactions["Категория"])
        & (transactions["Дата платежа"] >= date_2)
        & (transactions["Дата платежа"] <= date)
    ]
    summ = 0
    for i in filter_transactions["Сумма операции"]:
        if i < 0:
            summ += i
    logger.info(f"Находим сумму трат за 3 месяца от даты {date} с нужной категорией")
    result = {category: round(summ, 2)}
    return result

```
![](https://i.pinimg.com/736x/cf/97/ac/cf97acff7ee12739b9ab5e142e38d442.jpg)