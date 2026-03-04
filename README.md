# django-richenum

[![Latest PyPI Version](https://img.shields.io/pypi/v/django-richenum.svg)](https://pypi.python.org/pypi/django-richenum/)
[![Python versions](https://img.shields.io/pypi/pyversions/django-richenum.svg)](https://pypi.org/project/django-richenum/)
[![PyPI Downloads](https://img.shields.io/pypi/dm/django-richenum.svg)](https://pypi.org/project/django-richenum/)

## About

A Django extension of richenum for Python. If you're unfamiliar with richenums, please read up on them (see [Related Packages](#related-packages)) before using django-richenum.

### Model Fields

`IndexEnumField`  
Store ints in DB, but expose OrderedRichEnumValues in Python.

`CanonicalNameEnumField`  
Store varchar in DB, but expose RichEnumValues in Python.  
We recommend that you use `IndexEnumField` for storage and query efficiency.

`LaxIndexEnumField`  
Like `IndexEnumField`, but also allows casting to and from canonical names.  
Mainly used to help migrate existing code that uses strings as database values.

### Form Fields

`CanonicalEnumField`  
Uses the RichEnum/OrderedRichEnum `canonical_name` as form field values.

`IndexEnumField`  
Uses the OrderedRichEnum `index` as form field values.

### Django Admin

`RichEnumFieldListFilter`  
Enables filtering by RichEnum model fields in the Django admin UI.

## Links

- [GitHub: django-richenum](https://github.com/hearsaycorp/django-richenum)
- [PyPI: django-richenum](https://pypi.python.org/pypi/django-richenum/)

## Installation

```bash
pip install django-richenum
```

## Example Usage

### IndexEnumField

```python
>>> from richenum import OrderedRichEnum, OrderedRichEnumValue
>>> class MyOrderedRichEnum(OrderedRichEnum):
...    FOO = OrderedRichEnumValue(index=1, canonical_name="foo", display_name="Foo")
...    BAR = OrderedRichEnumValue(index=2, canonical_name="bar", display_name="Bar")
...
>>> from django.db import models
>>> from django_richenum.models import IndexEnumField
>>> class MyModel(models.Model):
...    my_enum = IndexEnumField(MyOrderedRichEnum, default=MyOrderedRichEnum.FOO)
...
>>> m = MyModel.objects.create(my_enum=MyOrderedRichEnum.BAR)
>>> m.save()
>>> m.my_enum
OrderedRichEnumValue - idx: 2  canonical_name: 'bar'  display_name: 'Bar'
>>> MyModel.objects.filter(my_enum=MyOrderedRichEnum.BAR)
```

### CanonicalNameEnumField

```python
>>> from richenum import RichEnum, RichEnumValue
>>> class MyRichEnum(RichEnum):
...    FOO = RichEnumValue(canonical_name="foo", display_name="Foo")
...    BAR = RichEnumValue(canonical_name="bar", display_name="Bar")
...
>>> from django.db import models
>>> from django_richenum.models import CanonicalNameEnumField
>>> class MyModel(models.Model):
...    my_enum = CanonicalNameEnumField(MyRichEnum, default=MyRichEnum.FOO)
...
>>> m = MyModel.objects.create(my_enum=MyRichEnum.BAR)
>>> m.save()
>>> m.my_enum
RichEnumValue - canonical_name: 'bar'  display_name: 'Bar'
>>> MyModel.objects.filter(my_enum=MyRichEnum.BAR)
```

### RichEnumFieldListFilter

```python
>>> from django_richenum.admin import register_admin_filters
>>> register_admin_filters()
```

## Related Packages

`richenum`  
Package implementing RichEnum and OrderedRichEnum that django-richenum depends on.

- [GitHub: richenum](https://github.com/hearsaycorp/richenum)
- [PyPI: richenum](https://pypi.python.org/pypi/richenum/)

## Notes

If you're using Django 1.7+, you'll need to use the `@deconstructible` decorator for your `RichEnumValue` and `OrderedRichEnumValue` classes so Django's migration framework knows how to serialize your `RichEnumValue` and `OrderedRichEnumValue`.

```python
>>> from django.utils.deconstruct import deconstructible
>>> from richenum import RichEnumValue, OrderedRichEnumValue
>>> @deconstructible
... class CustomRichEnumValue(RichEnumValue):
...     pass
...
>>> @deconstructible
... class CustomOrderedRichEnumValue(OrderedRichEnumValue):
...     pass
...
```

## Contributing

1. Fork the repo from [GitHub](https://github.com/hearsaycorp/django-richenum).
2. Make your changes.
3. Add unittests for your changes.
4. Run `make lint` and `make test`.
5. Add yourself to `AUTHORS.md` (in alphabetical order).
6. Send a pull request from your fork to the main repo.
