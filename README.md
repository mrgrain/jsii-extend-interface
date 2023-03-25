# jsii-struct-builder

Build jsii structs with ease.

Jsii doesn't support TypeScript [Utility Types](https://www.typescriptlang.org/docs/handbook/utility-types.html) like `Partial` or `Omit`, making it difficult to re-use existing [struct interfaces](https://aws.github.io/jsii/specification/2-type-system/#structs).
With this package, you can work around that limitation and create brand new struct interfaces based on the jsii specification of any existing structs, their parents, and your custom specification.

From jsii's perspective, these structs are completely new types.
From a maintainer's perspective, they require the same minimal effort as utility types do.
Everybody wins!

## Usage

Install with:

```console
npm install --save-dev @mrgrain/jsii-struct-builder
```

### Extending an Interface

Use the jsii FQN to extend an existing interface.
Use `omit` to remove any properties you are not interested in.

```ts
new JsiiInterface(project, {
  name: 'MyProjectOptions',
  extends: 'projen.typescript.TypeScriptProjectOptions',
  omit: ['sampleCode', 'projenrcTs', 'projenrcTsOptions']
});
```

### Adding new Properties

New properties can be added with a `@jsii/spec` definition.
Complex types can be used and will be imported using their FQN.
Any existing properties of the same name will be replaced.

```ts
new JsiiInterface(project, {
  name: 'MyProjectOptions',
  extends: 'projen.typescript.TypeScriptProjectOptions',
  props:{
    booleanSetting: {
      type: { primitive: jsii.PrimitiveType.Boolean }
    },
    complexSetting: {
      type: { fqn: "my_project.SomeEnum" }
    }
  }
});
```


### Updating existing Properties

Existing properties can be updated.
The provided partial `@jsii/spec` definition will be merged (non-recursively) with the existing spec.

```ts
new JsiiInterface(project, {
  name: 'MyProjectOptions',
  extends: 'projen.typescript.TypeScriptProjectOptions',
  updateProps:{
    typescriptVersion: {
      optional: false,
    },
    sampleCode: {
      docs: {
        summary: 'New summary',
        default: 'false',
      }
    }
  }
});
```

### Advanced usage

Extending an existing interface is optional.
The component can be used to generated interfaces from scratch.

```ts
new JsiiInterface(project, {
  name: 'MyProjectOptions',
  props:{
    complexSetting: {
      type: { fqn: "my_project.SomeEnum" }
    }
  }
});
```

The default configuration makes assumptions about the new interface that are usually okay.
For more complex scenarios `fqn`, `filePath` and `importLocations` can be used to influence the rendered output.

```ts
new JsiiInterface(project, {
  name: 'MyProjectOptions',
  extends: 'projen.typescript.TypeScriptProjectOptions',
  fqn: 'my_project.nested.location.MyProjectOptions',
  filePath: 'src/nested/my-project-options.ts',
  importLocations: {
    'my_project': '../enums'
  },
  props:{
    complexSetting: {
      type: { fqn: "my_project.SomeEnum" }
    }
  }
});
```
