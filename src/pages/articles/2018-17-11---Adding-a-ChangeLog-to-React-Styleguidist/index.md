---
title: Customizing React Styleguidist
date: "2019-01-02T22:12:03.284Z"
layout: post
draft: false
path: "/posts/Customizing-React-Styleguidist/"
category: "React"
tags:
  - "React"
  - "Styleguidist"
description: "An easy way to add a Changelog to your Styleguidist living style guide. Demonstrates how to override styleguidist components."
---

[React-Styleguidist](https://react-styleguidist.js.org/) is a living styleguide for React components, an alternative to [Storybook](https://storybook.js.org/).
One of the reasons it appeals to me is its customizability. In this blog I will show how you can override Styleguidist components in order to customize your living documentation. This example is going to focus on adding a change log to the generated styleguide.

## Why add a Changelog

A Changelog is important to any library as it informs the users of additions, changes and deletions to the library.
Based on a Changelog a user can decided whether or not they should upgrade and what they may or may not need to change. More information can be found at [keepachangelog.com](https://keepachangelog.com/en/1.0.0/)

## Displaying the package version

Displaying the version of the library that these docs are for is extremely useful.
This can be easily done in the `styleguide.config.js` file.
```js
  version: require('./package.json').version
```

## Writing the Changelog Component

The changelog will be displayed in a modal which can be accessed by clicking a link in the side bar.
I used the library react-responsive-modal for the modal and react-markdown to allow us use markdown in the Changelog file.

```jsx

export class ChangelogViewer extends React.Component {

  state = {
    open: false,
    markdown: undefined
  };

  componentDidMount() {
    return fetch(require('./CHANGELOG.MD'))
      .then(response => response.text() )
      .then(markdown => {
        this.setState({markdown});
      })
  }

  render() {
    const {open, markdown} = this.state;
    return (
      <div>
        {children}
        <a onClick={() => this.setState({open: true})}>
          Change Log
        </a>
        <Modal
          open={open}
          onClose={() => this.setState({open:false})}
        >
          { markdown &&
            <ReactMarkdown source={markdown} />
          }
        </Modal>
      </div>
    )
  }
}

```

## Overriding the Component

Now that we have our ChangeLogViewer component we have to tell Styleguidist that we want to use this component instead of the supplied Version Renderer.

In the above example we can see we render children `{children}`. When we override the VersionRenderer, the version will be passed into our new component as a child.

<INSERT PICTURE>

In the `styleguide.config.js` file add the following.
`src/styleguide/VersionRenderer` is the path to our component.

```js
  styleguideComponents: {
    VersionRenderer: 'src/styleguide/VersionRenderer'
  }
```