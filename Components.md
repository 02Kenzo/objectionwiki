`objection` is made up of three primary components.

* The first component is the [Frida Gadget](https://www.frida.re/) that runs in [embedded mode](https://www.frida.re/docs/modes/) and starts up with a patched mobile application. Frida is responsible for most of the _magic_ under the hood, running the hooks provided by `objection` ultimately making this tool possible in the first place.

* The second being the `objection` tool itself. This is a `python` software component that provides the `objection` commands and `explore` repl. It is responsible for interacting with a loaded Frida Gadget, running hooks and parsing the output generated by those.

* Lastly, the `objection` [hooks](https://github.com/sensepost/objection/tree/master/agent) themselves. These are compiled from a TypeScript project to produce a single `agent.js` file which are run using Frida within their respective run-times to make all of the `objection` features you can use possible.