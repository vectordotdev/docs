---
description: Integrate Timber with Django
---

# Django

Timber is commonly used with the [Django Python framework](https://www.djangoproject.com/). This document will outline installation and usage within that environment.

## Installation

Timber's python package, [`timber`](https://pypi.org/project/timber/), interfaces with the [built-in `logging` module](https://docs.python.org/3.7/library/logging.html) just like Django does, which means that integration is a matter of configuration.  [Django defaults to a dictionary-based configuration method](https://docs.djangoproject.com/en/2.1/topics/logging/#configuring-logging), but allows users to disable that and enable logging in code if they'd prefer. Timber works with both methods. Because of this, the default Timber Python installation instructions apply. Please see the default Timber Python installation instructions:

{% page-ref page="../" %}

## Usage

### Logging Events

Regardless of code-based or dictionary-based configuration, the logging experience is exactly the same:

{% code-tabs %}
{% code-tabs-item title="myapp/views.py" %}
```python
import logging
import timber
from django.shortcuts import render
from django.http import HttpResponse

# __name__ == "myapp.views"; because of logging event propagation,
# events will be handled by the "myapp" logger, which is what we've configured
# in the settings above. If you're having trouble sending your events, check the
# name of the logger being used in your code and the names of the loggers you've
# configured in your settings.
logger = logging.getLogger(__name__)

def index(request):
    logger.info("served request")
    return HttpResponse("Hello, world. You're at the polls index.")

def vote(request, myapp):
    # The `timber.context` helper works exactly as you'd expect.
    with timber.context(myapp=myapp.to_dict()):
        logger.info("user voted")
    return HttpResponse("Thanks for voting.")
```
{% endcode-tabs-item %}
{% endcode-tabs %}



