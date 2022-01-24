pyppeteer
==========

[![PyPI](https://img.shields.io/pypi/v/pyppeteer.svg)](https://pypi.python.org/pypi/pyppeteer)
[![PyPI version](https://img.shields.io/pypi/pyversions/pyppeteer.svg)](https://pypi.python.org/pypi/pyppeteer)
[![Documentation](https://img.shields.io/badge/docs-latest-brightgreen.svg)](https://pyppeteer.github.io/pyppeteer/)
[![CircleCI](https://circleci.com/gh/pyppeteer/pyppeteer.svg?style=shield)](https://circleci.com/gh/pyppeteer/pyppeteer)
[![codecov](https://codecov.io/gh/pyppeteer/pyppeteer/branch/dev/graph/badge.svg)](https://codecov.io/gh/pyppeteer/pyppeteer)



_Note: This fork is for my personal Production codebase. Its base is presently from Pyppeteer 1.0.2. For official Pyppeter, please refer to the [Official project on PyPI](https://pypi.python.org/pypi/pyppeteer)


Included Changes:
* [Fix BIGINT Page.Events.Console Bug](https://github.com/pyppeteer/pyppeteer/pull/355/files)
* [Fix Error Running From IIS on Windows](https://github.com/pyppeteer/pyppeteer/pull/349/files)


##[Improving ConsoleMessage Object with jsFrame & Timestamp](https://github.com/pyppeteer/pyppeteer/issues/352)

Added JavaScript call frame details and parsed timestamp to the `ConsoleMessage` object. And exposed toString() api for printing out resuls including stringification.

Eg:
```python
# Optionally, alter the URL component of log output
def _url_fixer(url):
    return url.replace(base_url, "").split('?')[0]

# Optionally create your own custom stringify handlers
def h_console_dumper(msg: ConsoleMessage, _type: str, _subType: str, _data: Any):
    # print(f"h_console_dumper() called. _type: {_type}, _subType: {_subType}, _data: {_data}")
    if _type == 'table':
        return f"COOL - Table: {json.dumps(_data)}"

    elif _type == 'boolean':
        return "COOL - Boolean"

    return None # Null return will default to built in stringify function

# Sample console handler
def h_js_console(msg: ConsoleMessage):
    if not msg.type == 'endGroup':
        print(f"[JSC] {msg.toString(url_fixer=_url_fixer, dumper=h_console_dumper)}")

page.on(Page.Events.Console, h_js_console )

res: Response = await page.goto(page_url)

```

Sample Output:
```
[JSC] 220120-194805.822 /libs/bar.js:5289:20 test multiline
second line
third line
[JSC] 220120-194805.855 /libs/bar.js:5299:20 <Date> Sat Jan 22 2022 02:11:27 GMT-0500 (Eastern Standard Time)
[JSC] 220120-194805.885 /libs/bar.js:3353:20 COOL - Table: [["c1", "c2", "c3"], ["r1c1", "r1c3", "r1c3"], ["r2c1", "r2c3", "r2c3"], ["Sat Jan 22 2022 02:11:27 GMT-0500 (Eastern Standard Time)", "", "Window"]]
```