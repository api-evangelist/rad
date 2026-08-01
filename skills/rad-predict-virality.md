---
name: Predict content virality before publishing
description: Score a thumbnail and title for predicted click-through and viral potential, with actionable recommendations, before making content public.
api: graphql/rad-api.graphql
operations: [verifyConnection, predictVirality]
---

# Predict content virality before publishing

Rad TV's virality prediction scores content for predicted click-through and viral potential
before you publish. Available on the GraphQL API (`POST https://api.rad.live/graphql`) and as the
`predict_virality` MCP tool.

## Steps
1. Confirm access with the `verifyConnection` query (Creator+ features).
2. Call the `predictVirality` mutation with the thumbnail image and title inputs.
3. Read the returned predictive score (0–100) plus the recommendation payload: overall assessment,
   strengths, and specific improvements.
4. A/B test: score multiple thumbnail/title options and pick the highest-scoring variant, then
   proceed to publish (see the `Publish an AI-enhanced video` skill).

## Rules
- Send `Authorization: Bearer <API_KEY>`; virality prediction requires a Creator+ subscription.
- On `INVALID_INPUT` / `FIELD_REQUIRED`, check the `extensions.field` and `suggestedFix` metadata
  in the GraphQL error envelope.
