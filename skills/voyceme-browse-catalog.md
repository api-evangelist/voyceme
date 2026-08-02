---
name: Browse the VoyceMe catalog
description: Discover manga/webtoon/web-novel series on VoyceMe and drill into a title's chapters using the public GraphQL API.
api: graphql/voyceme-graphql.graphql
operations: [voyce_series, voyce_series_by_pk, voyce_chapters]
generated: '2026-07-21'
method: generated
---

# Browse the VoyceMe catalog

VoyceMe exposes a public Hasura GraphQL endpoint at `https://graphql.voyce.me/v1/graphql`.
No authentication is required for reads (anonymous Hasura role). Send `POST` with
`Content-Type: application/json` and a `{ "query": "..." }` body.

## Steps

1. **List series.** Query `voyce_series` with `limit`, `offset`, and optional `where`
   (a `voyce_series_bool_exp`) and `order_by`. Select the fields you need — GraphQL is
   sparse by default.
   ```graphql
   query { voyce_series(limit: 20, order_by: {id: desc}) { id title description } }
   ```
2. **Get one series.** Fetch a single title by primary key with `voyce_series_by_pk(id: Int!)`,
   traversing its `author` (→ `voyce_users`) and `chapters` relationships inline.
   ```graphql
   query { voyce_series_by_pk(id: 4024) { id title author { username } } }
   ```
3. **List chapters.** Query `voyce_chapters` filtered by `where: {series_id: {_eq: $id}}`,
   ordered by chapter number.
   ```graphql
   query { voyce_chapters(where: {series_id: {_eq: 4024}}, order_by: {id: asc}) { id title } }
   ```

## Rules

- **Pagination:** always page with `limit`/`offset`; there is no cursor pagination. Use
  `voyce_series_aggregate { aggregate { count } }` for totals.
- **Errors:** responses are HTTP 200 with a top-level `errors[]` array on failure; check
  `errors[].extensions.code` (see `errors/voyceme-problem-types.yml`).
- **Permissions:** only public reads and whitelisted insert mutations run anonymously; do not
  attempt admin fields.
