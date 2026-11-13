<!-- regenerate: off -->

# JSON Structure: Schema Projections

This is the working area for the individual Internet-Draft,
"JSON Structure: Schema Projections".

JSON Structure is the canonical, representation-neutral definition model.
The specification derives JSON Schema, Avro, Protocol Buffers, XML Schema
(XSD), and CDDL schemas from that model, together with the value bindings
needed by their serializers. It also defines imports into the model and
the conditions under which values, constraints, and type information survive.

* [Editor's Copy](https://json-structure.github.io/projections/draft-vasters-json-structure-projections.html)
* [Draft Source](draft-vasters-json-structure-projections.md)
* [Issues](https://github.com/json-structure/projections/issues)

## Contributing

See the [contribution guidelines](CONTRIBUTING.md). Contributions can be made
by creating issues or pull requests.

## Command Line Usage

Formatted text and HTML versions of the draft can be built using `make`.

```sh
make
```

Check formatting and references with:

```sh
make lint
```

The build uses the
[Internet-Draft template tools](https://github.com/martinthomson/i-d-template).
See the [setup instructions](https://github.com/martinthomson/i-d-template/blob/main/doc/SETUP.md)
for dependencies. On Windows, run `make` in WSL or another supported POSIX
environment.

Pushes build and publish the editor's copy through GitHub Pages. Creating a
draft release is a separate publication step; an ordinary push does not submit
the draft to the IETF Datatracker.
