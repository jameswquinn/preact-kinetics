# Preact Kinetics: Complete Setup and Development Guide

This document provides a comprehensive guide for setting up and developing Preact Kinetics, a powerful animation library for Preact inspired by react-spring, with full TypeScript support.

## Table of Contents

1. [Project Structure](#project-structure)
2. [File Contents](#file-contents)
3. [Setup Instructions](#setup-instructions)
4. [Development Workflow](#development-workflow)
5. [Publishing to npm](#publishing-to-npm)
6. [Summary](#summary)

## Project Structure

```
preact-kinetics/
├── src/
│   ├── index.ts
│   ├── useSpring.ts
│   ├── useTransition.ts
│   ├── useTrail.ts
│   ├── useChain.ts
│   ├── animated.ts
│   └── config.ts
├── vite.config.ts
├── tsconfig.json
├── tsconfig.node.json
├── package.json
├── .gitignore
├── .npmignore
└── README.md
```

## File Contents

### vite.config.ts

```typescript
import { defineConfig } from 'vite';
import preact from '@preact/preset-vite';
import { resolve } from 'path';
import dts from 'vite-plugin-dts';

export default defineConfig({
  plugins: [
    preact(),
    dts({
      insertTypesEntry: true,
    }),
  ],
  build: {
    lib: {
      entry: resolve(__dirname, 'src/index.ts'),
      name: 'PreactKinetics',
      fileName: (format) => `preact-kinetics.${format}.js`,
    },
    rollupOptions: {
      external: ['preact'],
      output: {
        globals: {
          preact: 'Preact',
        },
      },
    },
  },
});
```

### tsconfig.json

```json
{
  "compilerOptions": {
    "target": "ES2020",
    "useDefineForClassFields": true,
    "module": "ESNext",
    "lib": ["ES2020", "DOM", "DOM.Iterable"],
    "skipLibCheck": true,

    "moduleResolution": "bundler",
    "allowImportingTsExtensions": true,
    "resolveJsonModule": true,
    "isolatedModules": true,
    "noEmit": true,
    "jsx": "react-jsx",
    "jsxImportSource": "preact",

    "strict": true,
    "noUnusedLocals": true,
    "noUnusedParameters": true,
    "noFallthroughCasesInSwitch": true,

    "declaration": true,
    "declarationDir": "./dist",
    "emitDeclarationOnly": true
  },
  "include": ["src"],
  "references": [{ "path": "./tsconfig.node.json" }]
}
```

### tsconfig.node.json

```json
{
  "compilerOptions": {
    "composite": true,
    "skipLibCheck": true,
    "module": "ESNext",
    "moduleResolution": "bundler",
    "allowSyntheticDefaultImports": true
  },
  "include": ["vite.config.ts"]
}
```

### package.json

```json
{
  "name": "preact-kinetics",
  "version": "1.0.0",
  "type": "module",
  "files": ["dist"],
  "main": "./dist/preact-kinetics.umd.js",
  "module": "./dist/preact-kinetics.es.js",
  "types": "./dist/index.d.ts",
  "exports": {
    ".": {
      "import": "./dist/preact-kinetics.es.js",
      "require": "./dist/preact-kinetics.umd.js",
      "types": "./dist/index.d.ts"
    }
  },
  "scripts": {
    "dev": "vite",
    "build": "tsc && vite build",
    "preview": "vite preview"
  },
  "dependencies": {
    "preact": "^10.5.13"
  },
  "devDependencies": {
    "@preact/preset-vite": "^2.5.0",
    "typescript": "^5.0.2",
    "vite": "^4.3.9",
    "vite-plugin-dts": "^2.3.0"
  },
  "peerDependencies": {
    "preact": "^10.5.13"
  }
}
```

### src/index.ts

```typescript
export { useSpring } from './useSpring';
export { useTransition } from './useTransition';
export { useTrail } from './useTrail';
export { useChain } from './useChain';
export { animated } from './animated';
export { config } from './config';
```

### src/useSpring.ts

```typescript
import { useState, useEffect } from 'preact/hooks';

interface SpringConfig {
  tension?: number;
  friction?: number;
  mass?: number;
}

interface SpringProps {
  from: Record<string, number>;
  to: Record<string, number>;
  config?: SpringConfig;
}

export function useSpring(props: SpringProps) {
  const [springProps, setSpringProps] = useState(props.from);

  useEffect(() => {
    // Simple linear interpolation for demonstration
    // In a real implementation, this would use spring physics
    const animate = () => {
      const newProps = {} as Record<string, number>;
      let isAnimating = false;

      for (const key in props.to) {
        const current = springProps[key];
        const target = props.to[key];
        if (current !== target) {
          isAnimating = true;
          newProps[key] = current + (target - current) * 0.1;
        } else {
          newProps[key] = current;
        }
      }

      setSpringProps(newProps);

      if (isAnimating) {
        requestAnimationFrame(animate);
      }
    };

    requestAnimationFrame(animate);
  }, [props.to]);

  return springProps;
}
```

### src/useTransition.ts

```typescript
import { useState, useEffect } from 'preact/hooks';

interface TransitionProps<T> {
  items: T[];
  from: Record<string, number>;
  enter: Record<string, number>;
  leave: Record<string, number>;
}

export function useTransition<T>(props: TransitionProps<T>) {
  const [transitions, setTransitions] = useState<Array<{ item: T; props: Record<string, number> }>>([]);

  useEffect(() => {
    // Simple implementation for demonstration
    // In a real implementation, this would handle enter/leave animations
    setTransitions(props.items.map(item => ({ item, props: props.enter })));
  }, [props.items]);

  return transitions;
}
```

### src/useTrail.ts

```typescript
import { useState, useEffect } from 'preact/hooks';
import { useSpring } from './useSpring';

interface TrailProps {
  length: number;
  from: Record<string, number>;
  to: Record<string, number>;
}

export function useTrail(props: TrailProps) {
  const [trail, setTrail] = useState<Array<Record<string, number>>>([]);

  useEffect(() => {
    const newTrail = Array(props.length).fill(null).map((_, index) => {
      const delay = index * 100; // Simple delay for demonstration
      return useSpring({
        from: props.from,
        to: props.to,
        config: { delay },
      });
    });
    setTrail(newTrail);
  }, [props.length, props.from, props.to]);

  return trail;
}
```

### src/useChain.ts

```typescript
import { useEffect, useRef } from 'preact/hooks';

type ChainRef = { current: { start: () => void } };

export function useChain(refs: ChainRef[], delays: number[] = []) {
  const timeoutRefs = useRef<number[]>([]);

  useEffect(() => {
    refs.forEach((ref, index) => {
      const timeoutId = setTimeout(() => {
        ref.current.start();
      }, delays[index] || index * 100); // Default delay if not provided

      timeoutRefs.current.push(timeoutId);
    });

    return () => {
      timeoutRefs.current.forEach(clearTimeout);
    };
  }, [refs, delays]);
}
```

### src/animated.ts

```typescript
import { h, ComponentType } from 'preact';

type AnimatedProps = Record<string, any>;

export function animated<P extends AnimatedProps>(Component: ComponentType<P>) {
  return (props: P) => {
    // In a real implementation, this would interpolate animated values
    return h(Component, props);
  };
}
```

### src/config.ts

```typescript
export const config = {
  default: { tension: 170, friction: 26 },
  gentle: { tension: 120, friction: 14 },
  wobbly: { tension: 180, friction: 12 },
  stiff: { tension: 210, friction: 20 },
  slow: { tension: 280, friction: 60 },
  molasses: { tension: 280, friction: 120 },
};
```

### .gitignore

```
node_modules
dist
```

### .npmignore

```
# Source files
src/

# Configuration files
vite.config.ts
tsconfig.json
tsconfig.node.json

# Development files
.gitignore
.npmignore

# Build artifacts
*.log

# Editor directories
.vscode/
.idea/

# Test files
__tests__/
*.test.ts
*.spec.ts

# Documentation files (if you want to exclude them)
# docs/

# Example files or directories (if you have any)
# examples/

# Any other files or directories you don't want to publish
```

### README.md

```markdown
# Preact Kinetics

A powerful animation library for Preact, inspired by react-spring, with full TypeScript support.

## Installation

```bash
npm install preact-kinetics
```

## Usage

```tsx
import { h } from 'preact';
import { useSpring, animated } from 'preact-kinetics';

function AnimatedComponent() {
  const props = useSpring({
    from: { opacity: 0 },
    to: { opacity: 1 },
  });

  return <animated.div style={props}>Hello, Preact Kinetics!</animated.div>;
}
```

For more detailed usage and API documentation, please refer to our [full README](https://github.com/yourusername/preact-kinetics).

## License

MIT
```

## Setup Instructions

1. Create a new directory for your project:
   ```
   mkdir preact-kinetics
   cd preact-kinetics
   ```

2. Initialize a new npm project:
   ```
   npm init -y
   ```

3. Install the required dependencies:
   ```
   npm install preact
   npm install --save-dev vite @preact/preset-vite vite-plugin-dts typescript
   ```

4. Create the project structure and files as shown in the [Project Structure](#project-structure) section.

5. Copy the contents of each file into their respective locations in your project.

## Development Workflow

1. Implement your library code in the `src` directory. Start with the files mentioned in `src/index.ts`.

2. To start the development server:
   ```
   npm run dev
   ```

3. To build your library:
   ```
   npm run build
   ```

4. The bundled files will be in the `dist` directory.

## Publishing to npm

1. Make sure you're logged in to npm:
   ```
   npm login
   ```

2. Update the version number in `package.json`:
   ```
   npm version patch  # or minor, or major
   ```

3. Publish your package:
   ```
   npm publish
   ```

Remember to update the version number before each new publish. Consider using `npm version` to manage this automatically.

## Summary

This setup provides a solid foundation for developing and publishing Preact Kinetics with full TypeScript support. Key features include:

1. **TypeScript Support**: Configured for strict type checking and JSX handling.
2. **Vite Configuration**: Efficiently bundles the library and generates type declarations.
3. **npm Package Configuration**: Correctly points to bundled files and type definitions.
4. **Git and npm Ignore**: Ensures only necessary files are tracked and published.
5. **Basic Implementation**: Includes simplified versions of core animations hooks (useSpring, useTransition, useTrail, useChain) and utilities (animated, config).

This configuration enables you to create a high-quality, type-safe animation library for Preact. It supports both UMD and ES module formats, making it versatile for different usage scenarios.

To get started, follow the setup instructions, implement your animation logic in the `src` directory, and use the provided development workflow. When you're ready to share your work, follow the publishing instructions to make your package available on npm.

Note that the current implementations in the `src` directory are simplified for demonstration purposes. In a production-ready library, you would need to implement proper spring physics and more robust functionality.

Happy coding with Preact Kinetics!
