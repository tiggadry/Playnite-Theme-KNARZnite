# Copilot instructions for KNARZnite

## Project constraints

- This is a Playnite Desktop theme targeting .NET Framework 4.6.2.
- Keep the existing non-SDK project format. Do not migrate to SDK-style or introduce newer .NET/C# requirements.
- Build with MSBuild using `source\Theme.sln`; do not assume `dotnet build` can resolve the Playnite references.
- Playnite assemblies are referenced from outside the repository and require a local Playnite installation or matching development binaries.

## XAML and project conventions

- This is primarily a XAML project. If a new source file is added, add it explicitly to `source\Theme.csproj`.
- Preserve existing Playnite `PART_*` template-part names and Playnite-specific bindings, controls, converters, markup extensions, and resource keys.
- Prefer `{DynamicResource ...}` for resources that may be overridden at runtime; use `{StaticResource ...}` where the existing resource structure requires it.
- Use Playnite's `{Settings ...}` markup extension for theme settings. User-facing settings belong in `source\themeExtras.yaml` or the appropriate existing manifest configuration.
- Keep optional extension integrations optional; do not introduce hard dependencies on extensions such as ThemeExtras, ThemeModifier, Metadata Utilities, or Extra Metadata Loader.
- Follow the existing XAML Styler configuration in `Settings.XamlStyler`. Use two-space indentation and spaces, not tabs.

## Repository structure

- Reuse the existing `DefaultControls`, `CustomControls`, `DerivedStyles`, `Views`, and shared resource dictionaries instead of creating duplicate styles or unnecessary new files.
- Treat `source\App.xaml` and `source\GlobalResources.xaml` as Playnite-provided/non-theme files; change them only when required by the task.
- Keep theme assets under the existing `source\Images` and `source\Fonts` directories.

## Validation

- Do not perform unrelated formatting, rebuilding, or validation.
- For changes that affect build compatibility, run the appropriate MSBuild build.
- For changes affecting Playnite runtime behavior or visual layout, validate in Playnite when practical.
