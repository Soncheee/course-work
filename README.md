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
