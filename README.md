# Blazor CacheView Form and Interactivity Sample

This .NET 11 Blazor Web App validates that request-specific content is regenerated inside a cached static SSR section.

The sample uses `CacheView` with:

- A cached initialization `Guid`
- An antiforgery-protected `EditForm`
- Required-field validation
- A prerendered Interactive Server counter
- A prerendered Interactive WebAssembly counter

It verifies that warm requests reuse cached markup while generating fresh antiforgery tokens and interactive component markers. It also covers form submission, validation, component hydration, repeated warm reloads, and cache expiration.

## Requirements

- .NET SDK `11.0.100-rc.1.26431.118`
- A browser with developer tools

The required SDK is pinned by `CacheViewFormComp/global.json`. Confirm that it is selected from the repository root:

```bash
dotnet --version
```

The command should report `11.0.100-rc.1.26431.118`.

## Restore, build, and run

Run these commands from the repository root:

```bash
dotnet restore CacheViewFormComp/CacheViewFormComp.sln
dotnet build CacheViewFormComp/CacheViewFormComp.sln --no-restore
dotnet run --project CacheViewFormComp/CacheViewFormComp/CacheViewFormComp.csproj --launch-profile http
```

Open `http://localhost:5243` if the browser doesn't open automatically.

## Architecture

The page and cache boundary use static server-side rendering. No global render mode is assigned to `App`, `Routes`, the page, or `CacheView`.

One `CacheView` contains a cacheable child that provides:

- An initialization `Guid` and UTC creation time that identify the cache entry
- A static SSR `EditForm` that posts to the same page
- A required field with data annotation validation
- A child rendered with `InteractiveServer`
- A child rendered with `InteractiveWebAssembly`

Both interactive children use the default prerendering behavior. The server project registers Interactive Server and Interactive WebAssembly, while the client project contains the WebAssembly component.

The cache entry uses a two-minute absolute expiration. This allows a cold/warm comparison before deliberately testing expiration.

## Test procedure

### 1. Capture a cold request

1. Open browser developer tools and select **Network**.
2. Enable **Preserve log** and filter requests to **Document**.
3. Load `/` and wait until both counter buttons respond.
4. Record the displayed initialization `Guid` and creation time.
5. Open the document response and record:
	- The `__RequestVerificationToken` hidden input value or a non-reversible hash of it.
	- The Server component's `prerenderId`.
	- The WebAssembly component's `prerenderId`.

Each `prerenderId` can appear twice because it identifies the start and end markers for one prerendered component.

### 2. Capture a warm request

1. Reload the page before the two-minute expiration.
2. Wait until both interactive children are ready.
3. Record the same values captured for the cold request.
4. Confirm that:
	- The initialization `Guid` is unchanged.
	- The antiforgery token is different.
	- Both `prerenderId` values are different.

Compare per-render `prerenderId` values rather than stable component type or location fields. Server and WebAssembly markers can use different formats.

### 3. Test the form on a warm page

1. Enter `Warm submission` in the **Name** field and select **Submit**.
2. Confirm that `Accepted: Warm submission` appears without an error page.
3. Clear the **Name** field and submit again.
4. Confirm that `Name is required.` appears beside the field.
5. Repeat a valid submission to confirm that subsequent posts still succeed.

### 4. Test both interactive children

1. On a warm page, click the Server counter three times and confirm that it changes from `0` to `3`.
2. Click the WebAssembly counter three times and confirm that it changes from `0` to `3`.
3. Reload before expiration, wait for both children to become interactive, and click each counter again.

A cold WebAssembly visit can require a runtime download. Record when each component becomes ready, but don't require either render mode to initialize faster.

### 5. Test expiration and recreation

1. Record the current initialization `Guid` and creation time.
2. Leave the page open for longer than two minutes from the displayed creation time.
3. Reload and confirm that a new initialization `Guid` and creation time appear.
4. Submit valid and empty form values again.
5. Click both counters several times.
6. Reload the recreated entry several times before it expires and repeat the form and counter checks.

All submissions and clicks should continue to work after expiration and on subsequent warm reloads.

## Expected results

| Check | Expected result |
| --- | --- |
| Warm cache reuse | Cold and warm initialization `Guid` values match. |
| Antiforgery regeneration | Cold and warm antiforgery token values differ. |
| Marker regeneration | Each child's cold and warm `prerenderId` values differ. |
| Valid form post | The submitted value appears in the accepted result. |
| Invalid form post | `Name is required.` appears without an error page. |
| Interactive Server | The Server counter increments on every click. |
| Interactive WebAssembly | The WebAssembly counter increments on every click. |
| Expiration | A new `Guid` appears, and forms and counters continue working. |

## Documentation

The [.NET 11 CacheView documentation](https://learn.microsoft.com/aspnet/core/blazor/state-management/cacheview-component?view=aspnetcore-11.0) states that antiforgery tokens and interactive render mode boundaries render fresh on every request while surrounding content remains cached.
