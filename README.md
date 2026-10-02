# humanize

[![PyPI version](https://img.shields.io/pypi/v/humanize.svg?logo=pypi&logoColor=FFE873)](https://pypi.org/project/humanize/)
[![Supported Python versions](https://img.shields.io/pypi/pyversions/humanize.svg?logo=python&logoColor=FFE873)](https://pypi.org/project/humanize/)
[![Documentation Status](https://readthedocs.org/projects/python-humanize/badge/?version=latest)](https://humanize.readthedocs.io/en/latest/?badge=latest)
[![PyPI downloads](https://img.shields.io/pypi/dm/humanize.svg)](https://pypistats.org/packages/humanize)
[![GitHub Actions status](https://github.com/talonwr/humanize/workflows/Test/badge.svg)](https://github.com/talonwr/humanize/actions)
[![codecov](https://codecov.io/gh/talonwr/humanize/branch/main/graph/badge.svg)](https://codecov.io/gh/talonwr/humanize)
[![MIT License](https://img.shields.io/github/license/talonwr/humanize.svg)](LICENCE)
[![Tidelift](https://tidelift.com/badges/package/pypi/humanize)](https://tidelift.com/subscription/pkg/pypi-humanize?utm_source=pypi-humanize&utm_medium=badge)

This modest package contains various common humanization utilities, like turning a
number into a fuzzy human-readable duration ("3 minutes ago") or into a human-readable
size or throughput. It is localized to:

- Arabic
- Basque
- Bengali
- Brazilian Portuguese
- Catalan
- Danish
- Dutch
- Esperanto
- European Portuguese
- Finnish
- French
- German
- Greek
- Hebrew
- Indonesian
- Italian
- Japanese
- Klingon
- Korean
- Latvian
- Norwegian
- Persian
- Polish
- Russian
- Simplified Chinese
- Sinhala
- Slovak
- Slovenian
- Spanish
- Swedish
- Turkish
- Ukrainian
- Uzbek
- Vietnamese

## API reference

[https://humanize.readthedocs.io](https://humanize.readthedocs.io/)

<!-- usage-start -->

## Installation

### From PyPI

```bash
python3 -m pip install --upgrade humanize
```

> **Note:** the PyPI `humanize` package is the upstream release and does not
> include this fork's fixes. To use the fork, install [from source](#from-source).

### From source

```bash
git clone https://github.com/talonwr/humanize
cd humanize
python3 -m pip install -e .
```

## Testing

The test suite runs on a source checkout. Install the test extras, then run
pytest (use `--benchmark-disable` to skip the noisy benchmark table; note that
`-p no:benchmark` breaks the benchmark tests instead):

```bash
python3 -m pip install -e ".[tests]"
python3 -m pytest --benchmark-disable
```

> **Note:** in zsh (the default macOS shell) the extras must be quoted —
> unquoted, `.[tests]` fails with `no matches found`.

Localization tests are skipped unless the compiled translation catalogs exist.
Generate them first (run from the repository root; requires gettext's `msgfmt`
on `PATH` — install with `brew install gettext` on macOS or
`apt install gettext` on Debian/Ubuntu; note the Homebrew formula may not add
`msgfmt` to `PATH` by itself):

```bash
scripts/generate-translation-binaries.sh
```

Without the catalogs a run looks green but is not full coverage: expect
764 passed / 110 skipped. With the catalogs generated, expect 874 passed.

## Usage

### Integer humanization

```pycon
>>> import humanize
>>> humanize.intcomma(12345)
'12,345'
>>> humanize.intword(123455913)
'123.5 million'
>>> humanize.intword(12345591313)
'12.3 billion'
>>> humanize.apnumber(4)
'four'
>>> humanize.apnumber(41)
'41'
```

### Date & time humanization

```pycon
>>> import humanize
>>> import datetime as dt
>>> humanize.naturalday(dt.datetime.now())
'today'
>>> humanize.naturaldelta(dt.timedelta(seconds=1001))
'17 minutes'
>>> humanize.naturalday(dt.datetime.now() - dt.timedelta(days=1))
'yesterday'
>>> humanize.naturalday(dt.date(2007, 6, 5))
'Jun 05'
>>> humanize.naturaldate(dt.date(2007, 6, 5))
'Jun 05 2007'
>>> humanize.naturaltime(dt.datetime.now() - dt.timedelta(seconds=1))
'a second ago'
>>> humanize.naturaltime(dt.datetime.now() - dt.timedelta(seconds=3600))
'an hour ago'
```

### Precise time delta

```pycon
>>> import humanize
>>> import datetime as dt
>>> delta = dt.timedelta(seconds=3633, days=2, microseconds=123000)
>>> humanize.precisedelta(delta)
'2 days, 1 hour and 33.12 seconds'
>>> humanize.precisedelta(delta, minimum_unit="microseconds")
'2 days, 1 hour, 33 seconds and 123 milliseconds'
>>> humanize.precisedelta(delta, suppress=["days"], format="%0.4f")
'49 hours and 33.1230 seconds'
```

#### Smaller units

If seconds are too large, set `minimum_unit` to milliseconds or microseconds:

```pycon
>>> import humanize
>>> import datetime as dt
>>> humanize.naturaldelta(dt.timedelta(seconds=2))
'2 seconds'
```

```pycon
>>> delta = dt.timedelta(milliseconds=4)
>>> humanize.naturaldelta(delta)
'a moment'
>>> humanize.naturaldelta(delta, minimum_unit="milliseconds")
'4 milliseconds'
>>> humanize.naturaldelta(delta, minimum_unit="microseconds")
'4 milliseconds'
```

```pycon
>>> humanize.naturaltime(delta)
'now'
>>> humanize.naturaltime(delta, minimum_unit="milliseconds")
'4 milliseconds ago'
>>> humanize.naturaltime(delta, minimum_unit="microseconds")
'4 milliseconds ago'
```

### File size humanization

```pycon
>>> import humanize
>>> humanize.naturalsize(1_000_000)
'1.0 MB'
>>> humanize.naturalsize(1_000_000, binary=True)
'976.6 KiB'
>>> humanize.naturalsize(1_000_000, gnu=True)
'976.6K'
```

### Human-readable floating point numbers

```pycon
>>> import humanize
>>> humanize.fractional(1/3)
'1/3'
>>> humanize.fractional(1.5)
'1 1/2'
>>> humanize.fractional(0.3)
'3/10'
>>> humanize.fractional(0.333)
'333/1000'
>>> humanize.fractional(1)
'1'
```

### Scientific notation

```pycon
>>> import humanize
>>> humanize.scientific(0.3)
'3.00 x 10⁻¹'
>>> humanize.scientific(500)
'5.00 x 10²'
>>> humanize.scientific("20000")
'2.00 x 10⁴'
>>> humanize.scientific(1**10)
'1.00 x 10⁰'
>>> humanize.scientific(1**10, precision=1)
'1.0 x 10⁰'
>>> humanize.scientific(1**10, precision=0)
'1 x 10⁰'
```

## Localization

> **Note:** a source checkout ships only `.po` translation sources; the compiled
> `.mo` catalogs that `i18n.activate()` needs are not committed (the PyPI
> wheel does ship compiled `.mo` catalogs). On a fresh clone, generate them
> first, from the repository root (requires gettext's `msgfmt` on `PATH` —
> `brew install gettext` on macOS, `apt install gettext` on Debian/Ubuntu):
>
> ```bash
> scripts/generate-translation-binaries.sh
> ```
>
> Without this, `activate()` raises
> `FileNotFoundError: No translation file found for domain: 'humanize'`.

How to change locale at runtime:

```pycon
>>> import humanize
>>> import datetime as dt
>>> humanize.naturaltime(dt.timedelta(seconds=3))
'3 seconds ago'
>>> _t = humanize.i18n.activate("ru_RU")
>>> humanize.naturaltime(dt.timedelta(seconds=3))
'3 секунды назад'
>>> humanize.i18n.deactivate()
>>> humanize.naturaltime(dt.timedelta(seconds=3))
'3 seconds ago'
```

You can pass additional parameter `path` to `activate` to specify a path to search
locales in.

```pycon
>>> import humanize
>>> humanize.i18n.activate("xx_XX")
<...>
FileNotFoundError: [Errno 2] No translation file found for domain: 'humanize'
>>> humanize.i18n.activate("pt_BR", path="path/to/my/own/translation/")
<gettext.GNUTranslations instance ...>
```

<!-- usage-end -->

How to add new phrases to existing locale files:

```sh
scripts/update-translations.sh
```

This extracts new phrases from the source code into `humanize.pot`, merges them into
each locale's `.po` file and compiles the binary `.mo` catalogs.

How to add a new locale (run from the repository root; `humanize.pot` is created
at the root by `scripts/update-translations.sh`):

```sh
msginit -i humanize.pot -o src/humanize/locale/<locale name>/LC_MESSAGES/humanize.po --locale <locale name>
```

Where `<locale name>` is a locale abbreviation, eg. `en_GB`, `pt_BR` or just `ru`, `fr`
etc.

List the language at the top of this README.
