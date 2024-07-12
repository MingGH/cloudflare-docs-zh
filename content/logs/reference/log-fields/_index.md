---
title: Log fields
pcx_content_type: navigation
weight: 51
---

# Log fields

## Datasets

The datasets below describe the fields available by log category:

- [Zone-scoped datasets](/logs/reference/log-fields/zone/)
- [Account-scoped datasets](/logs/reference/log-fields/account/)

## API

The list of fields can also be accessed directly from the API using the following endpoints:

- For zone-scoped datasets: `https://api.cloudflare.com/client/v4/zones/<ZONE_ID>/logpush/datasets/<DATASET>/fields`

- For account-scoped datasets: `https://api.cloudflare.com/client/v4/accounts/<ACCOUNT_ID>/logpush/datasets/<DATASET>/fields`

The `<DATASET>` argument indicates the log category. For example, `http_requests`, `spectrum_events`, `firewall_events`, `nel_reports`, or `dns_logs`.

## Availability

- Zone-scoped HTTP requests are available in both Logpush and Logpull.
- [Custom fields](/logs/reference/custom-fields/) for HTTP requests are only available in Logpush.
- All other datasets are only available through Logpush.

## Deprecation

Deprecated fields remain available to prevent breaking existing jobs. They may eventually become empty values if completely removed. Customers are encouraged to migrate away from deprecated fields if they are using them.

## Recommendation

For log field **ClientIPClass**, Cloudflare recommends using [Bot Tags](/bots/concepts/cloudflare-bot-tags/) to classify IPs.

## Additional resources

For more information on logs available in Cloudflare Zero Trust, refer to [Zero Trust logs](/cloudflare-one/insights/logs/).