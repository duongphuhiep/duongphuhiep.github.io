---
published: false
title: Backbone Model and ReactJs
layout: post
---
I want to write some opinions after reading some articles about using [Backbone Model](http://backbonejs.org/#Model) in [ReactJs](https://facebook.github.io/react/) applications.  

# Why people still use the old "Backbone Model"?

 * In most scenario, the application was backbones-based (from start) and people want to migrate only the "View" layer to React. 
 * Some peoples choose Backbone Model because they are more familiar with. 

# Is this "dou" good or bad?

I didn't see any article telling the advantages of Backbone Model on a ReactJs applications in term of Perf / Testability / Maintainability..

From what I read, peoples usually: 

 1. store Backbone object in components State.
 2. change the backbone objects... (It means mutate the react state.)
 3. listen for changes and call `reactComponent.forceUpdate()` [sometimes with optimization](http://timecounts.github.io/backbone-react/#36)

It works, ReactJs as a View (greatly) improves the old Backbones-based application. Make it better, cleaner than before.

**BUT**.. Backbone Model won't pull the best out of React rendering performance.

*"Normally you should try to avoid all uses of forceUpdate()..."* (Quote from [React docs](https://facebook.github.io/react/docs/component-api.html#forceupdate))

In order to get the best out of React. I will resume [this official guidelines](https://facebook.github.io/react/docs/advanced-performance.html) with  my understading and experiences: 

 * We should implement `shouldComponentUpdate()` on every components (at best!) or on performance-critical components (lists / big components)
 * `shouldComponentUpdate(nextProps, nextState)` compares the current state/props with the next state/props and tell React whether to render the components or not  (**Note:** `forceUpdate()` will skip checking `shouldComponentUpdate()`)
 * `shouldComponentUpdate()` is called frequently, it must to be really fast -> Deep-object comparison is not a choice here (neither Backbone Model) -> it leads to the uses of [`PureRenderMixin`](https://facebook.github.io/react/docs/pure-render-mixin.html) with [immutable-js](https://facebook.github.io/immutable-js/) and [cloning objects with ES6 `Object.assign()`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object/assign#Cloning_an_object) 

**HOWEVER**.. I don't think Backbone Model is bad either. 
 
* Evens the rendering is not optimized to the best. The `render()` funtions might be called more frequently than it should on the VirtualDOM.. but It is not the end of the world, because the `render()` functions is often cheap enough. The expensive updates on the real DOM happen only if there are real changes in the VirtualDOM. You might call `render()` a thousand times without triggering any update in the real DOM.
* `forceUpdate()` is not recommended to call, but it is not worst than `$digest()` and `$apply()` of AngularJs (and look how AngularJs is fantastic)


