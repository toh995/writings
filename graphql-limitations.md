---
date: 2023-11-07
---
DRAWBACKS OF GRAPHQL
1. Cachability
    - GraphQL uses POST requests
    - Cannot cache these - the request body is hidden/encrypted, until reaching the final server
2. Scoping/routing
    - nginx can inspect the URL/query params, and route to a different service
    - POST requests cannot do this (it's all a single endpoint)
3. Flexibility
    - GraphQL is inflexible and hard to implement (it's TOO opinionated)
    - JSON:API accepts multiple formats
        - Allows for incremental adoption


STRENGTHS OF GRAPHQL
- Good DX, easy-to-understand DSL to request exactly the data we need
