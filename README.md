# Blazor CacheView Form and Interactivity Sample

This .NET 11 Blazor Web App validates that request-specific content is regenerated inside a cached static SSR section.

The sample uses `CacheView` with:

- A cached initialization `Guid`
- An antiforgery-protected `EditForm`
- Required-field validation
- A prerendered Interactive Server counter
- A prerendered Interactive WebAssembly counter

It verifies that warm requests reuse cached markup while generating fresh antiforgery tokens and interactive component markers. It also covers form submission, validation, component hydration, repeated warm reloads, and cache expiration.

## Run the sample

```bash
dotnet run --project CacheViewFormComp.csproj
