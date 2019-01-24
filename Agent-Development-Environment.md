This article aims to explain how to configure a development environment for the Frida agent used in `objection`.

Although `objection` is built using many languages, TypeScript is used for the Frida agent. Before the agent can be used in objection, it has to go through a compilation phase to translate the TypeScript to an ES5 compatible JavaScript agent.

## toc

* [os dependencies](#os-dependencies)
* [agent dependencies](#agent-dependencies)

## os dependencies
To start off with configuring a development environment for the Frida agent, ensure that you have both the `node` and `npm` commands available. Both of there should be installable using your Operating Systems package manager. Installing the latest versions of both these commands is also recommended. For more detailed instructions on how to install these, please refer to documentation relevant to your Operating System.

## agent dependencies
With the `node` and `npm` commands available, the following steps should get you up and running, building the final `agent.js`.

- `cd` to the `objection/agent` directory.
- Run the `npm install` command to grab all of the gazillion dependencies needed. This command should drop the dependencies in a `node_modules` folder in the current directory.
- Depending on your version of `npm`, the `npm run build` command may automatically run. If not, run `npm run build` to generate the `agent.js` from the current TypeScript source code.

Running the `npm run build` command will fire off a one shot compilation of the agent. If you are planning on developing for a while then you may want to consider the `npm run watch` command. This command will watch the filesystem for changes (such as when you save a file) and automatically fire off a new compilation run for you.