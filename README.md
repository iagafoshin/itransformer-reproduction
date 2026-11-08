# Глубинное обучение 1. Проект D4: iTransformer

![HSE](https://img.shields.io/badge/University-HSE-blue)
![Course](https://img.shields.io/badge/Course-Deep%20Learning%201-8a2be2)
![Topic](https://img.shields.io/badge/Topic-D4%20iTransformer-orange)
![Stage](https://img.shields.io/badge/Stage-Pre--registration-yellow)

## Участники проекта

| ФИО | Группа | Email | Telegram |
| :--- | :--- | :--- | :--- |
| Агафошин Иван Андреевич | БКНАД241 | [iaagafoshin@edu.hse.ru](mailto:iaagafoshin@edu.hse.ru) | [@iagafoshin](https://t.me/iagafoshin) |

---

## Краткое описание идеи

Статья: **Liu et al., *iTransformer: Inverted Transformers Are Effective for Time Series Forecasting*, ICLR 2024** ([arXiv](https://arxiv.org/abs/2310.06625), [код](https://github.com/thuml/iTransformer))

Обычный трансформер для временных рядов делает токен из одного момента времени. iTransformer переворачивает: токен - это вся история одной переменной, а attention ищет связи между переменными

Авторы говорят, что так прогноз точнее, чем у DLinear, PatchTST и TimesNet. Но по их же Table 10 на ETTh1 и ETTm1 iTransformer не лучший - это и хотим проверить честно

## Что проверяем

- воспроизводится ли Table 10 на ETTh1, ETTm1 и Weather на 3 сидах
- одинаково ли посчитаны метрики у iTransformer и бейзлайнов - `drop_last` в тестовом лоадере и разная длина входа
- сколько качества дает сама инверсия, а сколько нормализация входа

Гипотеза, критерии и ставки - в [`PREREG.md`](PREREG.md), все решения по ходу - в [`DECISIONS.md`](DECISIONS.md)

## Репозиторий

| Путь | Описание |
| :--- | :--- |
| [`PREREG.md`](PREREG.md) | pre-registration |
| [`DECISIONS.md`](DECISIONS.md) | журнал решений |
| [`iTransformer/`](iTransformer/) | оригинальный код авторов, без изменений |
| [`logs/`](logs/) | запуск «как есть» на ETTh1 |

## Прогресс проекта

- [x] окружение с версиями ровно как у авторов
- [x] запуск «как есть» на ETTh1: средняя MSE **0.455** против 0.454 в статье ([лог](logs/asis_ETTh1_cpu.log))
- [x] pre-registration
- [ ] 3 сида и бейзлайны
- [ ] проверка `drop_last` и длины входа
- [ ] эксперименты с нормализацией

## Как повторить запуск

Mac, Python 3.11, версии из `requirements.txt` авторов, чтобы использовать те же библиотеки, что и авторы:

```bash
uv venv -p 3.11 .venv
uv pip install -p .venv/bin/python -r iTransformer/requirements.txt

mkdir -p iTransformer/dataset/ETT-small
curl -L -o iTransformer/dataset/ETT-small/ETTh1.csv \
  https://raw.githubusercontent.com/zhouhaoyi/ETDataset/main/ETT-small/ETTh1.csv

cd iTransformer
PATH=../.venv/bin:$PATH bash scripts/multivariate_forecasting/ETT/iTransformer_ETTh1.sh
cat result_long_term_forecast.txt
```

вывод:
```
ETTh1_96_96   mse:0.3859, mae:0.4046
ETTh1_96_192  mse:0.4395, mae:0.4347
ETTh1_96_336  mse:0.4917, mae:0.4627
ETTh1_96_720  mse:0.5016, mae:0.4903
```

На CPU это около 107 минут
