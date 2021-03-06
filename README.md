# Nestees

Provides a simple set of utility functions for dealing with nested dictionaries - a.k.a. dict of dicts.
Inspired by the 'field path' concept in Google's superb [Firebase](https://firebase.google.com/) product, 
these functions fully support the field path syntax and DELETE_FIELD sentinel to allow values nested 
arbitrarily deep in standard Python dictionaries to be get, set, updated and deleted.

## Example Usage: deep_get

 ```python

from nestees import deep_get

data = {
    'top': {
        'mid_leaf': 'magic',
        'middle': {
            'leafA': 20,
            'leafB.has.dots': 'its real!!!',
        },
    }
}

deep_get(data, 'top.mid_leaf')
>>> 'magic'

deep_get(data, 'top.middle.leafA')
>>> 20

# field path syntax supports backtics
deep_get(data, 'top.middle.`leafB.has.dots`')
>>> 'its real!!!'

# Get supports default values
deep_get(data, 'my.road.to.nowhere`', 'some default')
>>> 'some default'
```


## Example Usage: deep_set and deep_delete

 ```python

from nestees import deep_set, deep_delete

data = {}

deep_set(data, 'top.mid_leaf', 'magic')
repr(data)
>>>  {
    'top': {
        'mid_leaf': 'magic',
    }
}

deep_set(data, 'top.middle.leafA', 20)
repr(data)
>>> {
    'top': {
        'mid_leaf': 'magic',
        'middle': {
            'leafA': 20
        },
    }
}

# To delete a value, use the DELETE_SENTINEL
deep_set(data, 'top.mid_leaf.leafA', DELETE_SENTINEL)
repr(data)
>>> {
    'top': {
        'middle': {
            'leafA': 20
        }
    }
}

# or just use deep_delete
deep_delete(data, 'top.middle.leafA')
repr(data)
>>> {
    'top': {
        'middle': {}
    }
}
```

# Example of deep_update
deep_update works like Python's built-in dict update method, in that it allows values to be merged at the leaf level, rather than set, if the existing leaf is a dict...
```python
from nestees import deep_update

data = {
    'top': 53
}

# Updating an integer lead replaces the integer with a dict...
deep_update(data, 'top.middle', {'name':'John', 'occupation':'CEO'})
repr(data)

>>> {
    'top': {
        'middle': {
            'name':'John',
            'occupation':'CEO'
        }
    }
}

>>> deep_update(data, 'top.middle', {'gender':'non-binary'})
>>> data
{
    'top': {
        'middle': {
            'name':'John',
            'occupation':'CEO',
            'gender':'non-binary'
        }
    }
}

```