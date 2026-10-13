# Pipedrive

Query your Pipedrive sales data with SQL: deals, persons, organizations,
pipelines, and activities. Uses the Pipedrive v2 API with token
authentication via the `x-api-token` header.

Official docs: [Pipedrive API reference](https://developers.pipedrive.com).

## Setup

1. Find your company domain and API token under
   Settings → Personal preferences → API in Pipedrive.
2. Register the source:

```bash
coral source add --file sources/community/pipedrive/manifest.yaml
coral source test pipedrive
```

## Tables

| Table           | Description                  | Filters                    |
| --------------- | ---------------------------- | -------------------------- |
| `deals`         | Sales deals                  | `status`, `pipeline_id`, `stage_id` |
| `persons`       | People contacts              | `org_id`                   |
| `organizations` | Companies                    | —                          |
| `pipelines`     | Sales pipelines              | —                          |
| `activities`    | Calls, meetings, tasks       | `done`                     |

## Example queries

```sql
-- Open deals by value
SELECT title, value, currency FROM pipedrive.deals
WHERE status = 'open' ORDER BY value DESC LIMIT 10;

-- Contacts at one company
SELECT name FROM pipedrive.persons WHERE org_id = 123;

-- Overdue open activities
SELECT subject, due_date FROM pipedrive.activities WHERE done = false;
```

## Notes

- The v2 API paginates with a cursor (`additional_data.next_cursor`).
- Rate limits follow Pipedrive's standard per-token budget; the spec
  requests up to 500 rows per page to stay efficient.
