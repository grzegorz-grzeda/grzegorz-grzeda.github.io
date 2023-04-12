---
layout: post
title: "VT100 shell coloring for python logging"
date: 2023-03-11 07:00:00 -0000
categories: python
---

```python
import logging

LOGGER:logging.Logger

class VT100Formatter(logging.Formatter):

    green = "\x1b[32;20m"
    yellow = "\x1b[33;20m"
    red = "\x1b[31;20m"
    reset = "\x1b[0m"
    format = "%(asctime)s [%(name)s][%(levelname)s] %(message)s"

    FORMATS = {
        logging.INFO: green + format + reset,
        logging.WARNING: yellow + format + reset,
        logging.ERROR: red + format + reset,
    }

    def format(self, record):
        log_fmt = self.FORMATS.get(record.levelno)
        formatter = logging.Formatter(log_fmt)
        return formatter.format(record)


def initialize_logging(name):
    global LOGGER
    LOGGER = logging.getLogger(name)
    LOGGER.setLevel(logging.INFO)
    ch = logging.StreamHandler()
    ch.setLevel(logging.DEBUG)
    ch.setFormatter(VT100Formatter())
    LOGGER.addHandler(ch)


def I(text):
    LOGGER.info(text)


def W(text):
    LOGGER.warning(text)


def E(text):
    LOGGER.error(text)
```