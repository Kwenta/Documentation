---
description: Contributing to the Kwenta frontend
---

# Frontend

### Introduction

To ensure that contributing to the Kwenta UI remains simple and straightforward, this document aims to provide some standards and guidelines around how the codebase is structured, and how various components interact with one another.

Please note:
1. There are areas this document does not cover. Please feel free to create an issue, or make a pull request to address them.
2. Most parts of the codebase do not adhere to these guidelines yet. Please feel free to contribute to our refactoring efforts.
3. This document is new, and evolving.

With these guidelines are in place, we should aim for the following:

- Modularity and composablity: All code should be reusable and extendable. When adding new features, existing code should not be duplicated. In the situation that a similar solution already exists in the codebase, the existing version should be updated to accommodate the new use case.

- Structure and organization: It should be apparent where code should go. This makes it easier to add new features and find where existing features are implemented.

- Best practices: 

- Simplicity: 

- Security: Since this code is trusted by a number of people to handle important financial transactions, it is imperative that changes and dependency updates are audited, to guard potential attack vectors.

### Components

The codebase should contain three types of components, screen components, section components and base components. Their uses, differences and intricacies between them are outlined below:

#### Screen components

Screen components are high-level components that lay out the contents of a screen. These should generally not contain any state. In the case that it seems imperative to add some state here, developers should consider making that state global (in Recoil) instead. This will prevent prop drilling and unnecessary renders.

There should be no custom styling on screen components, as the styling should be applied on the base components or section components (if deemed absolutely necessary). In general, section components should contain fixed heights/widths if these values can be computed beforehand, to make sure that there is little to no layout shift when the UI is being rendered.

In addition to this, sometimes these components might also contain both the mobile and desktop version of certain screens (when it is impossible to handle the layout differences between both in CSS only).

#### Section components

Section components compose base components to create a functional block of the UI. They are generally stateful, and can contain their own state, or depend on global state. They generally should not contain styling, except in edge cases that are too high-level for the base components (usually these occur when dealing with difficult responsiveness issues).

They are expected to be fairly larger than screen components on average, but developers can break them down into smaller sections at their discretion, to reduce complexity and increase modularity. One advantage of doing this is that it might reveal opportunities to reuse "sub-sections" in other parts of the app, or even create new base components.

#### Base (core) components

Base/core components are atomic components that wrap base HTML elements and apply basic but custom logic and styling, in accordance with Kwenta's design primitives. They are the foundational layer of the entire UI. Good examples of these include: `Button`, `Icon`, `Text` etc.

The goal is for the entirety of the UI to be composed using these components. To make this possible, a number of things must be in place:

- They should have props that cover all expected states, based on the design primitives. It is important to note that these props should also be easy to update without requiring extensive refactoring.
- They must contain exhaustive, yet extendable styles that respond to the state of the component.
- They should generally be stateless. This means that the component's appearance is entirely dependent on its props at any point in time, making sure that its behaviour is predictable and easily tested.
- They should be responsive, if possible.
- They should be developed with theming in mind.
- The components and all its states should be documented in Storybook, so they can be viewed and inspected in isolation, and updated without having to disrupt feature development.

We should eventually be able to migrate the existing section components to depend on only the base components. This will reduce the number of changes we need to make if colors, sizes, border radii or other design motifs change in the future. It should also make it easier to ensure consistency in the behaviour of components across the UI.

### Theming

Recently, we rolled out light theme support on Kwenta. This means that going forward, style changes and additions have to account for both dark and light theme variants, as well as any other themes that might be added in the future. To make this easier, the current theme is available within the body of styled-components, as well as Recoil state (should only be used if absolutely necessary). The theme definitions can also be edited or augmented, to account for new design elements, but should generally use some existing base colors.

To make sure that code changes don't break theming, this will become one of the criteria for PR reviews.

### Responsiveness

The existing Kwenta UI was developed with desktop users in mind. While this accounts for most users already, it is important that the experience be optimized for mobile users as well. To this end, mobile versions of the existing screens are being built. Responsiveness will also be one of the criteria for the acceptance of future pull requests.


It should be noted that this is one of the places where section components are quite handy. Section component can be easily adapted for use in mobile contexts, since they generally contain smaller portions of the interface that are reused in the mobile designs.


### Logic (state, queries, hooks and contexts)

#### State

State management is a very important part of any application. Since this application contains a number of features, it follows that there is a lot of state to keep track of. Currently, there is a hybrid approach to managing state. Some state is stored in components (which leads to prop drilling when other components have to depend on that state), and we also store some "global" state in Recoil. There is nothing particularly wrong with this approach, but it sometimes leads to confusion. How do you know what state should be stored in Recoil vs. in section components?

A good rule of thumb is to consider the "influence" of each piece of state. This should generally influence where it should be stored. For example, the open/closed state of a modal should probably be stored inside a component, while the position data for the currently selected market should probably be stored in global state (Recoil), as it is accessed by multiple components.

A hidden benefit of global state is it removes the need to add/remove props from multiple components, or contorting component hierarchy, just to get access to state from another component.

In addition to this, selectors should be used instead of atoms when creating derived state. This removes the complexity of having to "listen" to updates from atoms, to update other atoms.


#### Queries & Refetching

Since this application depends on more than one data source (we display information from contracts, subgraphs and external APIs), handling data fetching, mutations and refetching is a complicated task.

For example, one of the problems currently being dealt with is triggering data refetches to the subgraph after data is written to a contract. While this seems trivial, there are a lot of variables to account for, including, but not limited to:

- Failed transactions
- Transaction confirmation taking a long time
- Latency between transaction confirmation and subgraph handling.

We are currently working on a solution to mitigate issues around data fetching, while also accounting for every conceivable edge case.

<!-- Discuss the RefetchContext -->

#### Hooks

Hooks are very helpful to encapsulate reusable logic. The fact that hooks can also be composed to create other hooks also means that we can further extract application logic from components, so that they can be reused, optimized and tested in isolation. Ideally, the number of hooks directly accessed within components should be reduced to a bare minimum, so that there is some form of separation of concerns. Components should handle rendering and layout, while hooks handle data and state manipulation.

#### Contexts

Sometimes, we may need to hoist data that cannot be stored in state (usually functions). Sometimes, it may be possible to put the function logic in a hook and reuse it, but in other cases, it is important to share the same instance of the function (e.g. calling refetch on multiple queries). Contexts are the preferred method of handling these cases. However, it should be noted that there are usually very few situations where this is necessary, and hooks or global state are usually sufficient. A good example of a context in the codebase is the [RefetchContext](https://github.com/Kwenta/kwenta/blob/dev/contexts/RefetchContext.tsx).
