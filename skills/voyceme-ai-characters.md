---
name: Explore VoyceMe AI characters
description: Discover VoyceMe's storypack AI-character chatbots and their public plays/galleries via the GraphQL API.
api: graphql/voyceme-graphql.graphql
operations: [storypack_chatbots, storypack_chatbots_by_pk, storypack_chatbot_plays]
generated: '2026-07-21'
method: generated
---

# Explore VoyceMe AI characters

VoyceMe's AI-character product ("storypack" / Play Engine) is exposed through the same public
Hasura GraphQL endpoint `https://graphql.voyce.me/v1/graphql` (no auth for reads).

## Steps

1. **List chatbots.** Query `storypack_chatbots` with `limit`/`offset`/`where`/`order_by`.
   ```graphql
   query { storypack_chatbots(limit: 20, order_by: {id: desc}) { id name description } }
   ```
2. **Get one chatbot.** Use `storypack_chatbots_by_pk(id: Int!)` and traverse its labels,
   tags, and plays relationships inline.
3. **Read public plays.** Query `storypack_chatbot_plays` (or `storypack_chatbot_plays_count`)
   to see engagement, filtered by chatbot with a `where` bool_exp.

## Rules

- Pagination is `limit`/`offset`; totals via `storypack_chatbots_aggregate`.
- Errors follow the GraphQL `errors[]` envelope (`errors/voyceme-problem-types.yml`).
- Only anonymous-role reads and the whitelisted `insert_storytech_chatbot_conversations` /
  `insert_storytech_chatbot_messages` mutations are available without credentials.
