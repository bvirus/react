---
id: props-in-getInitialState-as-anti-pattern
title: Props in getInitialState Is an Anti-Pattern
layout: tips
permalink: props-in-getInitialState-as-anti-pattern.html
prev: componentWillReceiveProps-not-triggered-after-mounting.html
next: dom-event-listeners.html
---

> Note:
>
> This isn't really a React-specific tip, as such anti-patterns often occur in code in general; in this case, React simply points them out more clearly.

Using props, passed down from parent, to generate state in `getInitialState` often leads to duplication of "source of truth", i.e. where the real data is. Whenever possible, compute values on-the-fly to ensure that they don't get out of sync later on and cause maintenance trouble.

**Bad example:**

```js
/** @jsx React.DOM */

var MessageBox = React.createClass({
  getInitialState: function() {
    return {nameWithQualifier: 'Mr. ' + this.props.name};
  },

  render: function() {
    return <div>{this.state.nameWithQualifier}</div>;
  }
});

React.renderComponent(<MessageBox name="Rogers"/>, mountNode);
```

Better:

```js
/** @jsx React.DOM */

var MessageBox = React.createClass({
  render: function() {
    return <div>{'Mr. ' + this.props.name}</div>;
  }
});

React.renderComponent(<MessageBox name="Rogers"/>, mountNode);
```

(For more complex logic, simply isolate the computation in a method.)

However, it's **not** an anti-pattern if you make it clear that synchronization's not the goal here:

```js
/** @jsx React.DOM */

var Counter = React.createClass({
  getInitialState: function() {
    // naming it initialX clearly indicates that the only purpose
    // of the passed down prop is to initialize something internally
    return {count: this.props.initialCount};
  },

  handleClick: function() {
    this.setState({count: this.state.count + 1});
  },

  render: function() {
    return <div onClick={this.handleClick}>{this.state.count}</div>;
  }
});

React.renderComponent(<Counter initialCount={7}/>, mountNode);
```
