### Antiforgery Token Regeneration

| Request | Initialization GUID | Token SHA-256 | Evidence |
| --- | --- | --- | --- |
| Cold GET | `f3ed3841-b9ac-4426-9749-368fe67a584e` | `ca163e284a8c9df85826369ff3fa930e24493542bd25c695970af71531317f8d` | `Cold & Warm Antiforgery token` |
| Warm GET | `f3ed3841-b9ac-4426-9749-368fe67a584e` | `cfec0f6db4aa89be8ba4b5c4feed4c037f56fec92b6caf61d18dcda6a831502f` | `Cold & Warm Antiforgery token` |