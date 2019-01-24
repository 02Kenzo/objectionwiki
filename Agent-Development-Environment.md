This article aims to explain how to configure a development environment for the Frida agent used in `objection`.

Although `objection` is built using many languages, TypeScript is used for the Frida agent. Before the agent can be used in objection, it has to go through a compilation phase to translate the TypeScript to an ES5 compatible JavaScript agent.

## toc

* [os dependencies](#command-format)
* [typescript environment](#getting-started-ios-edition)

## os dependencies
To start off with configuring a development environment for the Frida agent, ensure that you have both the `node` and `npm` commands available. Both of there should be installable using your Operating Systems package manager.

