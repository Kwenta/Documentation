---
description: Contributing to the Kwenta frontend
---

# Frontend

### Introduction

To ensure that contributing to the Kwenta UI remains simple and straightforward, this document aims to provide some standards and guidelines around how the codebase is structured, and how various components interact with one another.

Please note:
1. There are areas this document does not cover. Please feel free to reach out to any of the core contributors on Discord, or make a pull request to this document, to address them.
2. The codebase code that does not adhere to these guidelines yet. We are in the process of a refactoring. Feel free to contribute to these efforts.
3. This document is new, and evolving.

With these guidelines are in place, we should aim for the following:

- Modularity and composablity: All code should be reusable and extendable. When adding new features, existing code should not be duplicated. In the situation that a similar solution already exists in the codebase, the existing version should be updated to accommodate the new use case.

- Structure and organization: It should be apparent where code should go. This makes it easier to add new features and find where existing features are implemented.

- Best practices: 

- Simplicity: 

### Components

The codebase should contain broadly three types of components, screen components, section components and base/core components. Their uses, differences and intricacies between them are outlined below:

#### Screen components

Screen components are high-level components that lay out the contents of a screen. These should generally not contain any state. In the case that it seems imperative to add some state here, developers should consider making that state global (in Recoil) instead. This will prevent prop drilling and unnecessary renders.

There should be no custom styling on screen components, as the styling should be applied on the base/core components or section components (if deemed absolutely necessary). In general, section components should contain fixed heights/widths if these values can be computed beforehand, to make sure that there is little to no layout shift when the UI is being rendered.

In addition to this, sometimes these components might also contain both the mobile and desktop version of certain screens (when it is impossible to handle the layout differences between both in CSS only).

#### Section components

Section components compose base/core components to create a functional block of the UI. They are generally stateful, and can contain their own state, or depend on global state. They generally should not contain styling, except in edge cases that are too high-level for the base/core components (usually these occur when dealing with difficult responsiveness issues).

They are expected to be fairly larger than screen components on average, but developers can break them down into smaller sections at their discretion, to reduce complexity and increase modularity. One advantage of doing this is that it might reveal opportunities to reuse "sub-sections" in other parts of the app, or even create new base/core components.

#### Base/core components

Base/core components are atomic components that wrap base HTML elements and apply basic but custom logic and styling, in accordance with Kwenta's design primitives. They are the foundational layer of the entire UI. Good examples of these include: `Button`, `Icon`, `Text` etc.

The goal is for the entirety of the UI to be constructed using these as building blocks. To make this possible, a number of things must be in place:

- They should have props that cover all expected states, based on the design primitives. It is important to note that these props should also be easy to update without requiring extensive refactoring.
- They must contain exhaustive, yet extendable styles that respond to the state of the component.
- They should generally be stateless. This means that the component's appearance is entirely dependent on its props at any point in time, making sure that its behaviour is predictable and easily tested.
- They should be responsive, if possible.
- They should be developed with theming in mind.
- The components and all its states should be documented in Storybook, so they can be viewed and inspected in isolation, and updated without having to disrupt feature development.

We should eventually be able to migrate the existing section components to depend on only the base/core components. This will reduce the number of changes we need to make if colors, sizes, border radii or other design motifs change in the future. It should also make it easier to ensure consistency in the behaviour of components across the UI.

<!-- Make sure to discuss theming and responsiveness here. -->

### Logic (state, queries, contexts and hooks)

#### State


#### Queries & Refetching


#### Hooks

