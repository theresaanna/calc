# CALC

[![Build Status](https://travis-ci.org/18F/calc.svg)](https://travis-ci.org/18F/calc)

CALC (formerly known as "Hourglass"), which stands for Contracts Awarded Labor Category, is a tool to help contracting personnel estimate their per-hour labor costs for a contract, based on historical pricing information. The tool is live at https://calc.gsa.gov. You can track our progress on our [trello board](https://trello.com/b/LjXJaVbZ/prices) or file an issue on this repo. 

## Setup

To install the requirements, use:

```sh
pip install -r requirements.txt
```


Currently, CALC is a basic [Django] project. You can get started by creating
a `local_settings.py` file (based off of `local_settings.example.py`) with your
local database configuration, and running:

```sh
./manage.py syncdb
```

to set up the database. After that, you can load all of the data by running:

```sh
./manage.py load_data
```

From there, you're just a hop, skip and a jump away from your own dev server:

```sh
./manage.py runserver
```

If you are managing https://calc.gsa.gov, see https://github.com/18F/calc/blob/master/deploy.md.


## API

If you're interested in the underlying data, please see https://github.com/18F/calc/blob/master/updating_data.md

### Rates API
You can access rate information at `http://localhost:8000/api/rates/`.

#### Labor Categories
You can search for prices of specific labor categories by using the `q`
parameter. For example:

```
http://localhost:8000/api/rates/?q=accountant
```

You can change the way that labor categories are searched by using the
`query_type` parameter, which can be either:

* `match_words` (the default), which matches all words in the query;
* `match_phrase`, which matches the query as a phrase; or
* `match_exact`, which matches labor categories exactly

All of the query types are case-insenstive.

#### Education and Experience Filters
###### Experience
You can also filter by the minimum years of
experience and maximum years of experience. For example:

```
http://localhost:8000/api/rates/?&min_experience=5&max_experience=10&q=technical
```

Or, you can filter with a single, comma-separated range.
For example, if you wanted results with more than five years and less 
than ten years of experience:

```
http://localhost:8000/api/rates/?experience_range=5,10
```

###### Education
The valid values for the education endpoints are `HS` (high school), `AA` (associates),
`BA` (bachelors), `MA` (masters), and `PHD` (Ph.D).

There are two ways to filter based on education, `min_education` and `education`.

To filter by specific education levels, use `education`. It accepts one or more
education values as defined above:

```
http://localhost:8000/api/rates/?education=AA,BA
```

You can also get all results that match and exceed the selected education level
by using `min_education`. The following example will return results that have
an education level of MA or PHD:

```
http://localhost:8000/api/rates/?min_education=MA
```

The default pagination is set to 200. You can paginate using the `page`
parameter:

```
http://localhost:8000/api/rates/?q=translator&page=2
```

#### Price Filters
You can filter by price with any of the `price` (exact match), `price__lte`
(price is less than or equal to) or `price__gte` (price is greater than or
equal to) parameters:

```
http://localhost:8000/api/rates/?price=95
http://localhost:8000/api/rates/?price__lte=95
http://localhost:8000/api/rates/?price__gte=95
```

The `price__lte` and `price__gte` parameters may be used together to search for
a price range:

```
http://localhost:8000/api/rates/?price__gte=95&price__lte=105
```

#### Excluding Records
You can also exclude specific records from the results by passing in an `exclude` parameter and a comma separated list of ids:
```
http://localhost:8000/api/rates/?q=environmental+technician&exclude=875173,875749
```

The `id` attribute is available in api response.

#### Other Filters
Other params allow you to filter by the contract schedule of the transaction,
whether or not the vendor is a small business (valid values: `s` [small] and
`o` [other]), and whether or not the vendor works on the contractor or customer
site.

Here is an example with all three parameters (`schedule`, `site`, and
`business_size`) included:

```
http://localhost:8000/api/rates/?schedule=mobis&site=customer&business_size=s
```

For schedules, there are 7 different values that will return results (case
insensitive):
 
 - Environmental
 - AIMS
 - Logistics
 - Language Services
 - PES
 - MOBIS
 - Consolidated

For site, there are only 3 values (also case insensitive):

 - Customer
 - Contractor
 - both

And the `small_business` parameter can be either `s` for small business, or `o`
for other than small business.

[Django]: https://www.djangoproject.com/
