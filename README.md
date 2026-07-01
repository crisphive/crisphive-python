# CrispHive Python

The official Python SDK for the
[CrispHive API](https://docs.crisphive.com/).

Typed access to the public `/v1` API — customers, bookings, catalog, team and
fleet.

## Requirements

Python 3.8+.

## Installation

```sh
pip install crisphive
```

## Authentication

Every request is authenticated with a secret API key sent as a bearer token.
Create keys from your CrispHive business dashboard. **The key prefix selects the
data environment:**

- `chsk_live_…` → live (production) data
- `chsk_test_…` → sandbox (isolated test) data

Load keys from the environment — never commit them.

## Usage

```python
import os
import crisphive
from crisphive.rest import ApiException

config = crisphive.Configuration(
    access_token=os.environ["CRISPHIVE_API_KEY"],  # SDK adds the "Bearer " prefix
)

with crisphive.ApiClient(config) as api_client:
    customers = crisphive.CustomerApi(api_client)

    # List customers.
    res = customers.list_customers(limit=20)
    for c in (res.data.customers or []):
        print(c.id, c.full_name)

    # Create a customer.
    try:
        created = customers.create_customer(
            crisphive.CustomerCreateRequest(full_name="Ada Lovelace", email="ada@example.com")
        )
        print("created", created.data.customer_id)
    except ApiException as e:
        print("error", e.status, e.body)
```

## Pagination

List methods accept `page` / `limit` and return a `meta` object (`total`,
`count`, `per_page`, `current_page`, `total_pages`).

## Idempotency

`create_*` calls (customers, bookings) accept an `Idempotency-Key` header so
retries never create a duplicate.

## Errors

Non-2xx responses raise `crisphive.rest.ApiException`; inspect `e.status` and
`e.body` for the CrispHive error code.

## Documentation

- Docs: https://docs.crisphive.com
- API reference: https://docs.crisphive.com/technical-reference
- Webhooks: https://docs.crisphive.com/webhook

## License

[MIT](LICENSE)
