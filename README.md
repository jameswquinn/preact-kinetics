# Preact Kinetics

Preact Kinetics is a powerful animation library for Preact, inspired by react-spring. It provides a set of hooks and utilities to create fluid, physics-based animations with ease.

## Table of Contents

- [Installation](#installation)
- [Quick Start](#quick-start)
- [API Reference](#api-reference)
  - [useSpring](#usespring)
  - [useTransition](#usetransition)
  - [useTrail](#usetrail)
  - [useChain](#usechain)
  - [animated](#animated)
  - [config](#config)
- [Examples](#examples)
- [TypeScript Support](#typescript-support)
- [Contributing](#contributing)
- [License](#license)

## Installation

You can install Preact Kinetics using npm:

```bash
npm install preact-kinetics
```

Or using yarn:

```bash
yarn add preact-kinetics
```

## Quick Start

Here's a simple example to get you started:

```jsx
import { h } from 'preact';
import { useSpring, animated } from 'preact-kinetics';

function AnimatedComponent() {
  const props = useSpring({
    from: { opacity: 0, transform: 'translate3d(0,-40px,0)' },
    to: { opacity: 1, transform: 'translate3d(0,0px,0)' },
  });

  return <animated.div style={props}>Hello, Preact Kinetics!</animated.div>;
}
```

## API Reference

### useSpring

Creates a spring animation.

```jsx
const props = useSpring({
  from: { opacity: 0 },
  to: { opacity: 1 },
  config: { tension: 300, friction: 10 },
});
```

### useTransition

Creates transitions for list items.

```jsx
const transitions = useTransition(items, {
  from: { opacity: 0 },
  enter: { opacity: 1 },
  leave: { opacity: 0 },
});
```

### useTrail

Creates a trail animation with multiple items.

```jsx
const trail = useTrail(3, {
  from: { opacity: 0, transform: 'scale(0.8)' },
  to: { opacity: 1, transform: 'scale(1)' },
});
```

### useChain

Chains multiple animations together.

```jsx
const springRef = useRef();
const transitionRef = useRef();

useChain([springRef, transitionRef], [0, 0.5]);
```

### animated

A higher-order component for creating animated elements.

```jsx
const AnimatedDiv = animated('div');
```

### config

Preset configurations for common animation types.

```jsx
import { config } from 'preact-kinetics';

useSpring({ config: config.wobbly });
```

## Examples

### Animated Button

```jsx
import { h } from 'preact';
import { useSpring, animated } from 'preact-kinetics';

function AnimatedButton() {
  const [props, set] = useSpring(() => ({ scale: 1 }));

  return (
    <animated.button
      style={{ transform: props.scale.to(s => `scale(${s})`) }}
      onMouseEnter={() => set({ scale: 1.1 })}
      onMouseLeave={() => set({ scale: 1 })}
    >
      Hover me!
    </animated.button>
  );
}
```

### List Transition

```jsx
import { h } from 'preact';
import { useTransition, animated } from 'preact-kinetics';

function TransitionList({ items }) {
  const transitions = useTransition(items, {
    from: { opacity: 0, height: 0 },
    enter: { opacity: 1, height: 50 },
    leave: { opacity: 0, height: 0 },
  });

  return transitions((style, item) => (
    <animated.div style={style}>{item}</animated.div>
  ));
}
```

## TypeScript Support

Preact Kinetics is written in TypeScript and provides type definitions out of the box. You can use it in your TypeScript projects without any additional setup.

## Contributing

We welcome contributions to Preact Kinetics! Please see our [CONTRIBUTING.md](CONTRIBUTING.md) file for details on how to contribute.

## License

Preact Kinetics is MIT licensed. See the [LICENSE](LICENSE) file for details.
