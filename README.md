# xfspell — the Transformer Spell Checker

![xfspell — the Transformer Spell Checker](xfspell.png)

This is a Transformer-based English spell checker trained on 7M+ generated parallel sentences. 

## Usage

- Clone this repository
- Create a virtual environment (e.g., `python3 -m venv .pyenv`)
- Activate the created virtual environment (e.g., `source .pyenv/bin/activate`)
- For Manjaro users, install aspell (`pacman -S aspell`), otherwise the aspell-python-py3 fails to find `aspell.h`
- If on Python 3.11, make sure that `requirements.txt` does not reference exact versions (`==`), but only minimal versions (`>=`), otherwise pip fails to build certain wheels with a `missing longintrepr.h` error message
- If on Python 3.11, comment out the `fairseq` dependency in `requirements.txt` and use specify instead `git+https://github.com/One-sixth/fairseq.git` (from https://github.com/facebookresearch/fairseq/issues/5012#issuecomment-1675400618): 3.11 introduced constraints on the use of `@dataclass` and the fix was slow to come for fairseq 12.2
- Install requirements (e.g., `pip install -r requirements.txt`)
- Download [the pretrained model](https://xfspell.s3.amazonaws.com/models/model7m.tar.gz) and extract the content (`tar zxvf model7m.tar.gz`)
- Run: 
```
$ echo "tisimptant too spll chck ths dcment." \
    | python src/tokenize.py \
    | fairseq-interactive model7m/ \
    --path model7m/checkpoint_best.pt \
    --source-lang fr --target-lang en --beam 10 \
   | python src/format_fairseq_output.py
It's important to spell check this document.
``` 

## Fun examples

This spell checker clearly understands the long-range structures of the language:

```
$ echo "The book Tom and Jerry put on the yellow desk yesterday war about NLP."
    | python src/tokenize.py \
    | fairseq-interactive model7m/ \
    --path model7m/checkpoint_best.pt \
    --source-lang fr --target-lang en \
   | python src/format_fairseq_output.py
The book Tom and Jerry put on the yellow desk yesterday was about NLP.
```

```
$ echo "The books Tom and Jerry put on the yellow desk yesterday war about NLP." \
    | python src/tokenize.py \
    | fairseq-interactive model7m/ \
    --path model7m/checkpoint_best.pt \
    --source-lang fr --target-lang en \
   | python src/format_fairseq_output.py
The books Tom and Jerry put on the yellow desk yesterday were about NLP.
```

## How it's built

See [The Unreasonable Effectiveness of the Transformer Spell Checker](http://www.realworldnlpbook.com/blog/unreasonable-effectiveness-of-transformer-spell-checker.html).
