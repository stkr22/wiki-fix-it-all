# Python Snippets

## How do I get a counter in a for loop?

Use enumerate:

``` py
for idx, row in enumerate(all_rows):
  if idx >= 12:
    something
```

## How to transform a list of dicts to dict?

use Chainmap:

``` py
from collections import ChainMap

list_of_dict = [{1:2}, {2: 3}]
dict = dict(ChainMap(*list_of_dict))
```

Sources:

- <https://stackoverflow.com/questions/5236296/how-to-convert-list-of-dict-to-dict>

## How to build nested list comprehensions?

Sources:

- <https://stackoverflow.com/questions/18072759/list-comprehension-on-a-nested-list>

## Transform json to yaml and back

``` bash
python -c 'import json; import yaml; print(yaml.dump(json.load(open("{{ PATH_TO_JSON }}"))))'
```

Sources:

- <https://stackoverflow.com/a/55924692>
