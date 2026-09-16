### Interactive Marker Regeneration

| Component | Cold `prerenderId` | Warm `prerenderId` | Changed |
| --- | --- | --- | --- |
| Interactive Server | `5c118fd0ea3849fc8595729dd48a8a4b` | `1e9f5e90a9534da88ac63974cb199b6c` | Yes |
| Interactive WebAssembly | `f40d266a136f4eb39992e81176b53be9` | `d3ed1f59cf3343bab437b8828105a548` | Yes |

| Request | Cached GUID |
| --- | --- |
| Cold GET | `a91177f6-bed8-4865-8792-94a3580802d2` |
| Warm GET | `a91177f6-bed8-4865-8792-94a3580802d2` |

### Comparison Methodology

Only per-render `prerenderId` values were compared. Stable component type and location fields were ignored.

Each `prerenderId` may appear twice because it identifies the start and end markers of the same prerendered component. Each unique identifier was recorded once.

Server and WebAssembly markers use different formats by design. Each component's cold identifier was compared only with the same component's warm identifier.

**Result: PASS** - The cached GUID remained unchanged while both interactive children received new per-render `prerenderId` values on the warm GET.