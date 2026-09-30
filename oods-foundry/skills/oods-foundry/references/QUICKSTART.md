# Bring your design system to a running screen

This walkthrough turns a team's colour tokens into a brand, registers a trait and object, substitutes a team Button, and produces a Warehouse screen. It also renders and certifies a chart. Chart certification does not certify the surrounding application.

Use Node.js 22.0.0 or newer on macOS or Linux and connect your MCP client as the package README describes. The calls below use the underscore names your client lists. Keep them in one server session: a `schemaRef` expires after 30 minutes. Before starting, choose a fresh user-data folder if you have already registered `Harbor`, `Stockable` or `Warehouse`; creation does not silently overwrite existing work.

## Get the editable inputs

In your project, install this version of Foundry and copy its examples outside the installed runtime:

```sh
npm install @oods/foundry@0.4.0
cp -R node_modules/@oods/foundry/quickstart ./harbor-design-system
cd harbor-design-system/team-components
npm install --ignore-scripts
npm pack --ignore-scripts
```

For a release candidate, install its supplied Foundry tarball in the first command. The remaining steps are identical. The last command creates `harbor-example-components-1.0.0.tgz`, used when installing the generated apps. The team package is an editable example, not a published library.

The input folder contains `harbor.tokens.json`, `Stockable.trait.yaml`, `Warehouse.object.yaml` and `team-components/`. Replace their example values with your design system's values as you go. The brand document maps colour values into OODS Foundry's named roles; it is not an automatic importer for every design-token format. Keep the base, dark and high-contrast documents and their role names.

In the calls below, replace `<harbor.tokens.json>` with the parsed JSON document, `<Stockable.trait.yaml>` and `<Warehouse.object.yaml>` with those files' complete text, and `<team-components>` with the copied package's absolute directory. Replace `<schemaRef>`, `<compositionId>` and `<normalizedSpec>` with values returned earlier in the walkthrough. These placeholders are values to substitute, not literal tool arguments.

## Register your brand

Ask your assistant to make each named call. Start with `health`:

<!-- quickstart: health health -->
```json
{}
```

Then `brand_intake` with `template` to inspect the roles and descriptions:

<!-- quickstart: template brand_intake -->
```json
{"action":"template"}
```

The supplied Harbor document has a complete set of values. Change values, then call `brand_intake` with `validate`. Fix each reported issue before creating the brand; a failed contrast check includes the pair, measured ratio and required floor.

<!-- quickstart: brand-validate brand_intake -->
```json
{"action":"validate","brand_id":"Harbor","documents":"<harbor.tokens.json>"}
```

When `valid` is true, create it:

<!-- quickstart: brand-create brand_intake -->
```json
{"action":"create","brand_id":"Harbor","documents":"<harbor.tokens.json>"}
```

The result names the written files and token build. Your brand lives outside the unpacked runtime and generated apps carry its stylesheet. Reusing an existing brand id is refused; use `brand_apply` for an intentional change.

## Register your trait and object

`Stockable` supplies stock fields and views; `Warehouse` combines it with shipped lifecycle traits and authors ten sample warehouses. Their authored values keep each site's operating status, status history, stock, last restock, manager and operating organization coherent, so the detail screen shows no placeholders. Start with `object` to validate the trait:

<!-- quickstart: trait-validate object -->
```json
{"action":"validate","yaml":"<Stockable.trait.yaml>"}
```

Register it only after `valid: true`:

<!-- quickstart: trait-register object -->
```json
{"action":"register","yaml":"<Stockable.trait.yaml>"}
```

Validate and register the object after its trait exists:

<!-- quickstart: object-validate object -->
```json
{"action":"validate","yaml":"<Warehouse.object.yaml>"}
```

<!-- quickstart: object-register object -->
```json
{"action":"register","yaml":"<Warehouse.object.yaml>"}
```

Read the returned context checks. A missing trait or invalid definition is reported with its cause and registration is refused. See `OBJECTS-AND-TRAITS.md` for field types, money semantics and name-collision rules.

## Substitute your component

The example Button expects `caption` and `appearance`, so this mapping translates OODS Foundry's `content` and `intent`. Call `map`:

<!-- quickstart: mapping map -->
```json
{
  "action":"create","apply":true,"externalSystem":"harbor",
  "externalComponent":"TeamButton","oodsTraits":["Stateful"],
  "substitution":{
    "component":"Button",
    "react":{
      "package":"@harbor/example-components/react","version":"1.0.0","export":"TeamButton","localPath":"<team-components>",
      "props":{"content":{"name":"caption"},"intent":{"name":"appearance","values":{"neutral":"quiet","primary":"prominent","danger":"danger"}}}
    },
    "vue":{
      "package":"@harbor/example-components/vue","version":"1.0.0","export":"TeamButton","localPath":"<team-components>",
      "props":{"content":{"name":"caption"},"intent":{"name":"appearance","values":{"neutral":"quiet","primary":"prominent","danger":"danger"}}}
    }
  }
}
```

This replaces a shipped identity; it does not add a new catalog identity. The package's name, version and exports must match. Local paths help the preview find the package and are not emitted as consumer dependencies. Component reports distinguish met, unmet and not-checked obligations; a mapped name is not proof of semantic compatibility.

## Compose the Warehouse screen

Call `design_compose` and retain its `schemaRef`, `compositionId` and version:

<!-- quickstart: compose design_compose -->
```json
{"object":"Warehouse","context":"detail","preferences":{"brand":"Harbor","theme":"light"}}
```

The result contains the composed schema, object and trait provenance, selections and validation findings. Check `status` and warnings. Composition uses declared objects and keyword rules; missing selection confidence is not inferred.

## Render and certify a chart

Call `viz_render` with explicit rows from the example stock records:

<!-- quickstart: chart viz_render -->
```json
{"chartType":"bar","brand":"Harbor","theme":"light","rows":[{"warehouse":"Lakeside Distribution","pallets":640},{"warehouse":"North Yard","pallets":760},{"warehouse":"Harbor Cold Store","pallets":2400}],"encodings":{"x":{"field":"warehouse","type":"nominal"},"y":{"field":"pallets","type":"quantitative"}},"output":{"includeNormalizedSpec":true,"includeA11y":true}}
```

Then call `artifact_certify` with that returned normalized specification and the same brand/theme:

<!-- quickstart: certify artifact_certify -->
```json
{"spec":"<normalizedSpec>","brand":"Harbor","theme":"light"}
```

Read `conformant`, each pillar, and its findings. For this Cartesian chart, certification reads the specification's data. ECharts-primary charts also need the same data operand used for rendering. High contrast has a forced-colour exemption, not a numeric contrast pass. This chart is a separate artifact; this walkthrough does not insert it into the Warehouse screen.

## Preview and generate

Call `design_preview` for the saved composition:

<!-- quickstart: preview design_preview -->
```json
{"compositionId":"<compositionId>","version":1}
```

Open the returned React or Vue `appUrl`. Inspect the Warehouse name, stock status and team Button. The app is served locally by default. Inline presentation depends on the MCP host; the browser link is the portable route. An absent contract browser leaves browser obligations not checked, with reasons.

Call `code_generate` to receive a complete single-screen app rather than only a component:

<!-- quickstart: react code_generate -->
```json
{"schemaRef":"<schemaRef>","framework":"react","profile":"build","options":{"output":"application","brand":"Harbor","theme":"light","payloadMode":"file"}}
```

<!-- quickstart: vue code_generate -->
```json
{"schemaRef":"<schemaRef>","framework":"vue","profile":"build","options":{"output":"application","brand":"Harbor","theme":"light","payloadMode":"file"}}
```

Each result names its payload directory, artifact content hash, dependencies and `validationReceipt`. The build profile checks source statically; read `notChecked` for compilation and browser work that has not run. Copy each artifact's files into a separate app folder. Its package.json pins the OODS packages, which install from npm, and the example team package at exact versions. The example package is on no registry, so first install the local `harbor-example-components-1.0.0.tgz` by its path, then follow the install block (`npm install`, then `npm run build`) and run `npm run dev`. For an unpublished release candidate, install its supplied OODS tarballs the same way.

The screen labels its sample data. Connect actions to your application before shipping: the preview's integration notice means that no domain record was changed. Request `context: "workflow"` when you want multiple screens with a local sample store; that store does not supply production persistence.

All OODS packages here use the Apache License 2.0. Generated output belongs to you; installed dependencies keep their licenses. Retain the returned hashes and receipts when reviewing what you built.
