# /create-component

Scaffold a new React component inside `packages/excalidraw/components/`.

## Input

The user provides: `<ComponentName>` (PascalCase).

## Steps

1. **Create the component file** at `packages/excalidraw/components/<ComponentName>.tsx`:

   ```tsx
   import { useAppStateValue } from "../hooks/useAppStateValue";

   import "./<ComponentName>.scss";

   interface <ComponentName>Props {
     // TODO: define props
   }

   export const <ComponentName> = (props: <ComponentName>Props) => {
     return <div className="<ComponentName>">TODO</div>;
   };
   ```

2. **Create the SCSS file** at `packages/excalidraw/components/<ComponentName>.scss`:

   ```scss
   .<ComponentName> {
     // TODO: add styles
   }
   ```

3. **Create the test file** at `packages/excalidraw/components/<ComponentName>.test.tsx`:

   ```tsx
   import { render } from "../tests/test-utils";
   import { <ComponentName> } from "./<ComponentName>";

   describe("<ComponentName>", () => {
     it("renders without crashing", () => {
       // TODO: add meaningful tests
     });
   });
   ```

4. **Report** the 3 created files and remind the user to:
   - Define the props interface
   - Add real styles and tests
   - Register the component where needed (action, sidebar, etc.)

## Rules

- Follow project naming: PascalCase for component, matching filenames.
- Always colocate SCSS with the component.
- Always create a test file.
- Use direct relative imports, never barrel imports.
