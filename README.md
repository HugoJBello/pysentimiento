# PySentimiento: Sentiment Analysis in Spanish


![Tests](https://github.com/finiteautomata/pysentimiento/workflows/run_tests/badge.svg)

A Transformer-based library for SocialNLP classification tasks.

Currently supports:

- Sentiment Analysis (Spanish)
- Emotion Analysis (Spanish)


Just do `pip install pysentimiento` and start using it:

[![Test it in Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/drive/1ItS0-ZPXGcEeVmRmHaneX3w8eq6Vhdde?usp=sharing)

```python
from pysentimiento import SentimentAnalyzer
analyzer = SentimentAnalyzer()
analyzer.predict("Qué gran jugador es Messi")
# returns 'POS'
analyzer.predict("Esto es pésimo")
# returns 'NEG'
analyzer.predict("Qué es esto?")
# returns 'NEU'

analyzer.predict_probas("Dónde estamos?")
# returns {'NEG': 0.10235335677862167,
# 'NEU': 0.8503277897834778,
# 'POS': 0.04731876030564308}
```

Also, you might use pretrained models directly with [`transformers`](https://github.com/huggingface/transformers) library.

```python
from transformers import AutoTokenizer, AutoModelForSequenceClassification

tokenizer = AutoTokenizer.from_pretrained("finiteautomata/beto-sentiment-analysis")

model = AutoModelForSequenceClassification.from_pretrained("finiteautomata/beto-sentiment-analysis")
```

## Preprocessing

`pysentimiento` features a tweet preprocessor specially suited for tweet classification with transformer-based models.

```python
from pysentimiento.preprocessing import preprocess_tweet

# Replaces user handles and URLs by special tokens
preprocess_tweet("@perezjotaeme debería cambiar esto http://bit.ly/sarasa") # "[USER] debería cambiar esto [URL]"

# Shortens repeated characters
preprocess_tweet("no entiendo naaaaaaaadaaaaaaaa", shorten=2) # "no entiendo naadaa"

# Normalizes laughters
preprocess_tweet("jajajajaajjajaajajaja no lo puedo creer ajajaj") # "jaja no lo puedo creer jaja"

# Handles hashtags
preprocess_tweet("esto es #UnaGenialidad") # "esto es una genialidad"

```

## Trained models so far

- [`beto-sentiment-analysis`](https://huggingface.co/finiteautomata/beto-sentiment-analysis)
- [`beto-emotion-analysis`](https://huggingface.co/finiteautomata/beto-emotion-analysis)

## Instructions for developers

1. First, download TASS 2020 data to `data/tass2020` (you have to register [here](http://tass.sepln.org/2020/?page_id=74) to download the dataset)

Labels must be placed under `data/tass2020/test1.1/labels`

2. Run script to train models

```
# Sentiment
python bin/train_sentiment.py "dccuchile/bert-base-spanish-wwm-cased" models/beto-sentiment-analysis/ --epochs 5 --lang es


python bin/train_sentiment.py "bert-base-uncased" models/bert-base-sentiment-analysis/ --epochs 10 --lang en
python bin/train_sentiment.py "roberta-base" models/roberta-base-sentiment-analysis/ --epochs 10 --lang en

python bin/train_sentiment.py "vinai/bertweet-base" models/bertweet-base-sentiment-analysis/ --epochs 10 --lang en

# Emotion

python bin/train_emotion.py "dccuchile/bert-base-spanish-wwm-cased" models/beto-emotion-analysis/ --epochs 5 --lang es

python bin/train_emotion.py "bert-base-uncased" models/bert-base-emotion-analysis/ --epochs 5 --lang en
python bin/train_emotion.py "vinai/bertweet-base" models/bertweet-base-emotion-analysis/ --epochs 5 --lang es

```

3. Upload models to Huggingface's Model Hub

## Smoke test

```
python bin/train_sentiment.py "dccuchile/bert-base-spanish-wwm-cased" models/test/ --epochs 5 --limit 500 && python bin/train_sentiment.py "bert-base-uncased" models/test/ --epochs 5 --limit 500 --lang en && rm -Rf models/test/

# Emotion
python bin/train_emotion.py "dccuchile/bert-base-spanish-wwm-cased" models/test/ --lang es --epochs 3 --limit 500 && python bin/train_emotion.py "vinai/bertweet-base" models/test/ --lang en --epochs 3 --limit 500 && rm -Rf models/test/
```

## TODO:

* Upload some other models
* Train in other languages
* Write brief paper with description

## Suggestions and bugfixes

Please use the repository [issue tracker](https://github.com/finiteautomata/pysentimiento/issues) to point out bugs and make suggestions (new models, use another datasets, some other languages, etc)
