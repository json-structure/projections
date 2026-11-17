---
title: "JSON Structure: Schema Projections"
abbrev: "JSON Structure Projections"
category: std

docname: draft-vasters-json-structure-projections-latest
submissiontype: IETF
number:
date: 2026-11-13
consensus: true
v: 3
area: Web and Internet Transport
workgroup: Building Blocks for HTTP APIs
keyword: Internet-Draft
venue:
  group: TBD
  type: Working Group
  mail: TBD
  arch: TBD
  github: "json-structure/projections"
  latest: "https://json-structure.github.io/projections/draft-vasters-json-structure-projections.html"

author:
  - fullname: Clemens Vasters
    organization: Microsoft Corporation
    email: clemensv@microsoft.com

normative:
  RFC3339:
  RFC3986:
  RFC4648:
  RFC6901:
  RFC8259:
  RFC8610:
  RFC8949:
  RFC9562:
  JSTRUCT-CORE:
    title: "JSON Structure: Core"
    author:
      - fullname: Clemens Vasters
    target: https://json-structure.github.io/core/draft-vasters-json-structure-core.html
  JSTRUCT-VALIDATION:
    title: "JSON Structure: Validation Extensions"
    author:
      - fullname: Clemens Vasters
    target: https://json-structure.github.io/validation/draft-vasters-json-structure-validation.html
  JSTRUCT-COMPOSITION:
    title: "JSON Structure: Conditional Composition"
    author:
      - fullname: Clemens Vasters
    target: https://json-structure.github.io/conditional-composition/draft-vasters-json-structure-cond-composition.html
  JSTRUCT-ALTNAMES:
    title: "JSON Structure: Alternate Names and Descriptions"
    author:
      - fullname: Clemens Vasters
    target: https://json-structure.github.io/alternate-names/draft-vasters-json-structure-alternate-names.html
  JSONSCHEMA-CORE:
    title: "JSON Schema: A Media Type for Describing JSON Documents"
    author:
      - fullname: Austin Wright
      - fullname: Henry Andrews
      - fullname: Ben Hutton
      - fullname: Greg Dennis
    date: 2022-06
    target: https://json-schema.org/draft/2020-12/json-schema-core
  JSONSCHEMA-VALIDATION:
    title: "JSON Schema Validation: A Vocabulary for Structural Validation of JSON"
    author:
      - fullname: Austin Wright
      - fullname: Henry Andrews
      - fullname: Ben Hutton
      - fullname: Greg Dennis
    date: 2022-06
    target: https://json-schema.org/draft/2020-12/json-schema-validation
  AVRO:
    title: "Apache Avro 1.12.0 Specification"
    author:
      - org: Apache Software Foundation
    target: https://avro.apache.org/docs/1.12.0/specification/
  PROTO2:
    title: "Protocol Buffers Language Guide (proto2)"
    author:
      - org: Google
    target: https://protobuf.dev/programming-guides/proto2/
  PROTO3:
    title: "Protocol Buffers Language Guide (proto3)"
    author:
      - org: Google
    target: https://protobuf.dev/programming-guides/proto3/
  PROTO-PRESENCE:
    title: "Protocol Buffers Application Note: Field Presence"
    author:
      - org: Google
    target: https://protobuf.dev/programming-guides/field_presence/
  PROTOJSON:
    title: "ProtoJSON Format"
    author:
      - org: Google
    target: https://protobuf.dev/programming-guides/json/
  XSD-STRUCTURES:
    title: "W3C XML Schema Definition Language (XSD) 1.1 Part 1: Structures"
    author:
      - fullname: Shudi Gao
      - fullname: C. M. Sperberg-McQueen
      - fullname: Henry S. Thompson
    date: 2012-04-05
    target: https://www.w3.org/TR/xmlschema11-1/
  XSD-DATATYPES:
    title: "W3C XML Schema Definition Language (XSD) 1.1 Part 2: Datatypes"
    author:
      - fullname: David Peterson
      - fullname: Shudi Gao
      - fullname: Ashok Malhotra
      - fullname: C. M. Sperberg-McQueen
      - fullname: Henry S. Thompson
    date: 2012-04-05
    target: https://www.w3.org/TR/xmlschema11-2/

informative:
  JSTRUCT-IMPORT:
    title: "JSON Structure: Import"
    author:
      - fullname: Clemens Vasters
    target: https://json-structure.github.io/import/draft-vasters-json-structure-import.html
  RFC9165:

--- abstract

This document projects the representation-neutral JSON Structure model into
JSON Schema, Apache Avro, Protocol Buffers, XML Schema, and Concise Data
Definition Language (CDDL). It defines target schemas, serializer value bindings,
reverse imports, and conditions for preserving values, constraints, and schema
information.

--- middle

# Introduction {#introduction}

JSON Structure {{JSTRUCT-CORE}} is intended as a representation-neutral
definition language. Its schemas are written in JSON {{RFC8259}} and
include provisions for JSON serialization, but its data definitions can
be used independently of that representation.

JSON Structure serves as the canonical, abstract base model for data
definitions. Data structures are defined in JSON Structure and projected
into programming languages, database schemas, and other schema languages.
This document covers projections into formal schema languages.

To serialize data in Avro, an application projects its JSON Structure
data definition into an Avro schema and uses an Avro serializer with that
schema. The same approach applies to Protocol Buffers and the other
formats covered here: their schemas are projections of the data model
contained in the JSON Structure definition. Reverse imports recover JSON
Structure definitions within the stated preservation limits.

The targets are JSON Schema {{JSONSCHEMA-CORE}} {{JSONSCHEMA-VALIDATION}},
Avro {{AVRO}}, Protocol Buffers {{PROTO2}} {{PROTO3}}, XML Schema (XSD)
{{XSD-STRUCTURES}} {{XSD-DATATYPES}}, and CDDL {{RFC8610}}.

The rules use Validation {{JSTRUCT-VALIDATION}},
Conditional Composition {{JSTRUCT-COMPOSITION}}, and Alternate Names
{{JSTRUCT-ALTNAMES}} where needed.

# Conventions {#conventions}

{::boilerplate bcp14-tagged}

The _model_ is the canonical data model defined by a JSON Structure
schema document. A _projection_ maps that model into another schema
language. _Source_ and _target_ follow the conversion direction. A
_binding_ maps model values to and from the serializer's data API. A
_carrier_ holds a value lacking an equivalent target type. Projection
rules specify schema mappings, bindings, and preservation limits.

The applicable Core version is identified by `$schema`. A processor MUST
report any substitution of another version's semantics.

# Projection and Preservation Model {#projection-model}

## Canonical Definitions and Serialization Bindings {#canonical-model}

A projection MUST specify target schemas and bindings from logical types,
not JSON tokens. Decimals are exact base-10; binary values are octets.

Native types SHOULD be used when value-preserving. Textual or wrapped
carriers require a target or serializer limitation, or an explicit
representation-preserving profile. The reason and consequences MUST be stated.
The model remains authoritative: a binding or identified validator MUST
enforce constraints absent from the target when preservation is claimed.
Validation outside the generated target schema is called _residual validation_.

Core JSON encodings apply only to selected JSON representations or
boundaries, including JSON Schema and the CDDL JSON profile. They MUST NOT
be imposed on native bindings.

## Instances and Value Equivalence {#value-equivalence}

Let S be the set of valid source values and T the set of target values.
Encoding E maps S into T; decoding D maps target values back to the
source model. Value preservation requires D(E(x)) = x for every x in S.

Equality follows the source model: object and set order is irrelevant;
array and tuple order matters. Absence, present null, and present empty
collections differ. Numeric equality is exact; binary equality compares
octets; string equality compares characters.

Logical values MUST be distinguished from lexical spellings. Trailing
decimal zeroes, equivalent binary-text spellings, token whitespace, XML
prefixes, unordered schema members, and comments need not survive unless
lexical or source-file preservation is requested.

Temporal bindings MUST identify calendar, offset-bearing, or instant
semantics. Discarding an observable offset loses information. An explicit
instant profile permits UTC normalization, without preserving original text.

## Independent Preservation Properties {#preservation-properties}

Value preservation:
: Every valid source value is reversible, even if its carrier changes.

Constraint preservation:
: The target schema accepts exactly the encodings of valid source values,
  E(S), with no additional values. Constraints checked only by an adapter
  are not native target-schema constraints.

Schema round-trip preservation:
: Import recovers semantic declarations: types, references, names,
  constraints, defaults, and identities. Claims MUST state required metadata.

Unqualified _lossless_ requires all three for the stated subset and profile.
Partial claims MUST be qualified. For example, `int8` in unrestricted Avro
`int` preserves values, widens constraints, and needs metadata to recover width.


## Processing Profiles and Diagnostics {#profiles-and-diagnostics}

A processor MUST identify dialects, roots, extensions, and required adapters,
validators, or metadata. Unmet lossless requests MUST fail; explicitly enabled
lossy projections MAY proceed. Every applicable loss MUST be reported at its
affected source location. Every loss diagnostic MUST identify source
format and location, target construct or omission reason, affected preservation
property, effect, and any remedy or source restriction. Effects distinguish
widening (admitting additional values), narrowing (excluding source values),
value collapse (merging distinct values), representation change, and schema loss.

Ignored annotations MUST NOT count as enforcement. Unknown types and unresolved
references MUST NOT silently become permissive types or defaults.
Diagnostic interfaces and metadata formats are implementation-defined;
interoperable round-trip claims MUST use an agreed metadata channel and
interpretation.

## Roots, Namespaces, and References {#roots-and-references}

Processors MUST select root `type` or `$root`; definitions-only libraries
MUST NOT acquire arbitrary roots. A target requiring one root needs explicit
selection. References use `{"type":{"$ref":"..."}}`, or `{"$ref":"..."}`
inside union arrays. Bare schema `$ref` and referenced root `type` are
invalid; referenced roots use `$root`.

References MUST resolve before name rewriting, with JSON Pointer escaping
per {{RFC6901}} and identity preserved. Supported recursion MUST remain
references; unsupported recursion MUST be diagnosed, not depth-truncated.
External resources MAY be materialized or use Import {{JSTRUCT-IMPORT}};
resource identity and resolution MUST survive.

## Names and Annotations {#names-and-annotations}

Imported names MUST map to distinct legal JSON Structure identifiers
matching `[A-Za-z_][A-Za-z0-9_]*`. A declaration's `altnames` map MAY
retain names for other representations. Its `json` entry gives the JSON
property name; `avro`, `proto`, `xml`, and `cddl` give format-specific
names. A projection uses the entry for its target format.

For example, this schema gives the model property `firstName` different
JSON and Avro names:

~~~ json
{
  "$schema": "https://json-structure.org/meta/extended/v0/#",
  "$id": "https://example.com/schemas/Person",
  "$uses": ["JSONStructureAlternateNames"],
  "name": "Person",
  "type": "object",
  "properties": {
    "firstName": {
      "type": "string",
      "altnames": {
        "json": "first-name",
        "avro": "first_name"
      }
    }
  },
  "required": ["firstName"],
  "additionalProperties": false
}
~~~

The Avro record field is named `first_name`; the JSON property is
`first-name`. The model's `required` array still names `firstName`.
Alternate names specify spelling, not encoding, field numbers, XML node
kinds, or namespace identity. Target names MUST satisfy the format's
naming and uniqueness rules.

`required` and `tuple` name declaration keys; references MUST be rewritten
consistently. `altenums` mappings MUST be one-to-one. Descriptions SHOULD
be copied to target documentation. Examples, units, currencies, localized
descriptions, semantic annotations, and unknown extensions MUST be retained
as metadata or reported lost.

## Numeric and Textual Representations {#numeric-representations}

`integer` aliases `int32`. A b-bit signed integer spans -(2^(b-1)) through
2^(b-1)-1; an unsigned integer spans 0 through 2^b-1.

| Type | Minimum | Maximum |
|:-----|--------:|--------:|
| `int8` | -128 | 127 |
| `uint8` | 0 | 255 |
| `int16` | -32768 | 32767 |
| `uint16` | 0 | 65535 |
| `int32`, `integer` | -2147483648 | 2147483647 |
| `uint32` | 0 | 4294967295 |
| `int64` | -9223372036854775808 | 9223372036854775807 |
| `uint64` | 0 | 18446744073709551615 |
| `int128` | -170141183460469231731687303715884105728 | 170141183460469231731687303715884105727 |
| `uint128` | 0 | 340282366920938463463374607431768211455 |

Core JSON uses numbers through `uint32`, strings for wider integers and
`decimal`, and encoded text such as Base64 {{RFC4648}} for octets. Schema
numeric constraints follow those representations but MUST be applied
numerically. Signed integer strings match `-?(0|[1-9][0-9]*)`, unsigned
strings `(0|[1-9][0-9]*)`, over the entire string; bounds remain additional.

Core decimals require a fractional part. Projections MUST apply effective
precision and scale, defaulting to 34 significant and 7 fractional digits.
These MUST be reconciled with target coefficient-digit or fixed-scale rules.

Generic `number` MUST NOT be assumed binary64. Floating conversion preserves
only exactly representable values; overflow, underflow, and rounding lose
values. `float8` specifies no portable E4M3, E5M2, or binary16 encoding;
processors MUST NOT infer one. Wider carriers lose type identity. Reverse
projections MUST handle NaN, payloads, and infinities separately.

Character lengths, decoded octet lengths, encoded byte lengths, and numeric
precision MUST NOT be interchanged.

## Presence, Defaults, and Composition {#presence-and-composition}

For non-null T:

| Declaration | Permitted states |
|:------------|:-----------------|
| Required T | A present non-null value |
| Optional T | Absence or a present non-null value |
| Required union of `null` and T | A present null or non-null value |
| Optional union of `null` and T | Absence, a present null, or a present non-null value |

Presence states MUST remain distinct. Default application MUST be evaluated
separately; defaults do not imply nullability.

Core unions select the first matching member when several members accept
a value. Losing that branch identity affects round trips. An inclusive union
accepts any matching branch; an exclusive union requires exactly one match.
Inclusive unions MUST NOT become exclusive
unless disjoint. Alternative `required` sets mean exactly one complete set;
projections MUST preserve this condition, not flatten the sets.

Materialization expands inherited declarations into explicit properties.
Inheritance and add-ins MUST follow source semantics. Materialization MAY
be used after Core's first-base-wins property precedence. Derived properties
MUST NOT redefine inherited properties. Conjunctive constraints MUST remain
conjunctive without replacing that precedence. Unrepresentable abstractness,
inheritance, or add-in identity MUST be reported.

# JSON Schema {#json-schema}

This projection validates Core JSON representations using Draft 2020-12.
Earlier dialects MUST be normalized, including Draft-07 tuple `items`,
`additionalItems`, `definitions`, and older boolean `exclusiveMinimum`.

## JSON Structure to JSON Schema {#structure-to-json-schema}

### Primitive Types {#json-schema-primitives}

Targets MUST identify the 2020-12 `$schema`. Table mappings include the
qualifications below.

| JSON Structure type | JSON Schema representation |
|:--------------------|:---------------------------|
| `null` | `{"type":"null"}` |
| `boolean` | `{"type":"boolean"}` |
| `string` | `{"type":"string"}` |
| `number` | `{"type":"number"}` |
| `integer`, `int8`, `uint8`, `int16`, `uint16`, `int32`, `uint32` | `integer` with the inclusive bounds in {{numeric-representations}} |
| `int64`, `uint64`, `int128`, `uint128` | `string` with the integer grammar and the applicable bounds |
| `float8`, `float`, `double` | `number`, with source-domain restrictions when expressible |
| `decimal` | `string` with the decimal grammar, precision, and scale |
| `binary` | `string` with `contentEncoding` and optional `contentMediaType` |
| `date` | `string`, `format: "date"` |
| `datetime` | `string`, `format: "date-time"` |
| `time` | `string`, `format: "time"` |
| `duration` | `string`, `format: "duration"` |
| `uuid` | `string`, `format: "uuid"` |
| `uri` | `string`, `format: "uri-reference"` |
| `jsonpointer` | `string`, `format: "json-pointer"` |
| `any` | An unconstrained schema, such as `{}` or `true` |

Numeric keywords do not constrain strings; string-valued bounds MUST NOT be
copied into them. Integer-string ranges MAY use exact regular expressions;
otherwise their bounds MUST be retained for additional validation and native
widening reported. Decimal grammar, precision, scale, and bounds require
equivalent enforcement. A numeric carrier is a separate instance-transforming
profile.

By default, `format` is an annotation; its presence does not require a
validator to check that format. Extended types need an agreed assertion
profile or equivalent constraints; otherwise their domains widen.
`uri-reference` preserves Core's relative references; `uri` excludes them.

Content annotations do not require decoding or validation. Core's default
binary encoding MUST be explicit. `contentCompression` requires metadata
or a loss report.

### Objects, Maps, and Collections {#json-schema-compounds}

Objects recursively map `properties` and `required` using JSON alternate
names. Effective `additionalProperties` MUST be explicit, including
version-defined defaults and recursively projected schema values.
Alternative required sets MUST add `oneOf` branches `{"required": Ri}`
to the common object schema.
Ri denotes one alternative set of required property names.

Maps use `object` without declared properties and projected `values` as
`additionalProperties`. `keyNames` becomes `propertyNames`; `patternKeys`
becomes `patternProperties` only with equivalent additional-property behavior.

Arrays project `items`; sets add `uniqueItems: true`, without suppressing
observable JSON array order. Tuples use `prefixItems` in `tuple` order and
MUST set `minItems: n`, `maxItems: n`, and `items: false`. Names need metadata.

### Unions and Choices {#json-schema-unions}

Unions use `anyOf`. Equivalent unrestricted alternatives MAY use a `type`
array. Branch order SHOULD survive; JSON Schema validation does not assign
Core's first-match identity.

Tagged choices use `oneOf` branches with `properties: {k: V}`,
`required: [k]`, and `additionalProperties: false`.
Inline choices use materialized alternatives; each MUST require its selector
and constrain it with `const`. Base and branch properties MUST be combined
before closure.

### Constraints, Definitions, and Defaults {#json-schema-constraints}

These keywords map directly only with matching applicability, representation,
and evaluation:
`minimum`, `maximum`, `exclusiveMinimum`, `exclusiveMaximum`,
`multipleOf`, `minLength`, `maxLength`, `pattern`, `minItems`,
`maxItems`, `uniqueItems`, `contains`, `minContains`,
`maxContains`, `minProperties`, `maxProperties`,
`dependentRequired`, and `propertyNames`.

Map entry bounds become property bounds; `const` and `enum` retain JSON
values. Constraints MUST intersect intrinsic type domains. Conditional
Composition MAY use corresponding `allOf`, `anyOf`, `oneOf`, `not`, `if`,
`then`, and `else` with recursively equivalent evaluation.

Definitions become uniquely keyed `$defs`; processors MUST rewrite references
and MUST NOT emit namespace-only objects as unconstrained definitions.
`$root` becomes `$ref`; `$id` survives when resource identity remains valid.
`name` MAY supply `title`, not type identity; descriptions map directly.
`$uses`, `$offers`, abstractness, and namespaces need metadata.

JSON Schema `default` annotates; Validation defaults fill missing properties.
This behavioral difference MUST be reported or implemented by a declared adapter.

## JSON Schema to JSON Structure {#json-schema-to-structure}

### Primitive and Numeric Inference {#json-schema-inference}

`null`, `boolean`, `string`, and `number` map directly. Integer import MUST
compute the effective domain, select a containing Core type, and retain
narrower constraints. Wide types change JSON numbers to strings. Domains
outside all Core integer types MUST be rejected in constraint-preserving
Core-only requests; other profiles can use an explicit numeric-text adapter.

Integral `1`, `1.0`, and `1e0` satisfy JSON Schema integer validation.
Processors MUST distinguish parsed-value preservation from Core's lexical
integer restrictions. Numeric formats MUST NOT independently justify
narrowing or representation changes.
String formats MAY select extended types only when asserted and
domain-compatible; otherwise they remain annotations. Absolute `uri`
restrictions MUST survive. Digit strings MUST remain strings without
declared numeric semantics.

### Structural Mapping {#json-schema-reverse-structure}

Declared properties become an object; dynamic objects become maps.
Unconstrained objects use map-of-`any`. An object that permits no properties,
whose only value is `{}`, uses Validation `maxEntries: 0`, not null.
Undeclared `required` names need `any` declarations
plus applicable pattern and additional-property constraints.

JSON Schema excludes pattern-matched keys from `additionalProperties`;
Core patterns do not declare properties. Processors MUST construct equivalent
key/value rules or diagnose unsupported combinations. Multiple pattern
matches intersect.

Homogeneous arrays remain arrays, retaining uniqueness constraints.
Conversion to sets MAY occur only under an explicit order-discarding,
value-collapsing profile. `prefixItems` becomes a tuple only with exact
length and no tail; names MUST be generated or recovered. Other prefixes
MUST retain equivalent rules or be rejected, not fixed-length by assumption.

### Composition and Unsupported Evaluation Features {#json-schema-evaluation}

`true` becomes `any`; `false` requires rejection or Conditional Composition
`{"type":"any","not":{"type":"any"}}`. Missing `type` MUST preserve
category-specific keyword applicability and unconstrained categories.

`anyOf` MAY become a type union when all branches declare types and selection
adds no observable distinction. `oneOf` needs disjointness for inclusive
conversion; required-set forms follow {{json-schema-compounds}}.
`allOf` MUST retain intersections, including contradictions; structural
merging requires equivalence, never property overwrites.

`$dynamicRef`, `$dynamicAnchor`, `unevaluatedProperties`, and `unevaluatedItems`
MUST be transformed equivalently for the selected graph or rejected in
lossless mode. References, anchors, nested `$id`, and vocabularies MUST
resolve before conversion. Unknown required vocabularies prevent lossless claims.

## Example and Preservation Boundary {#json-schema-example}

This closed object:

~~~ json
{
  "$schema": "https://json-structure.org/meta/core/v0/#",
  "$id": "https://example.com/schemas/Reading",
  "name": "Reading",
  "type": "object",
  "properties": {
    "label": { "type": "string" },
    "level": { "type": "uint8" },
    "note": { "type": ["null", "string"] }
  },
  "required": ["label", "level"],
  "additionalProperties": false
}
~~~

projects to:

~~~ json
{
  "$schema": "https://json-schema.org/draft/2020-12/schema",
  "$id": "https://example.com/schemas/Reading",
  "title": "Reading",
  "type": "object",
  "properties": {
    "label": { "type": "string" },
    "level": { "type": "integer", "minimum": 0, "maximum": 255 },
    "note": { "anyOf": [{ "type": "null" }, { "type": "string" }] }
  },
  "required": ["label", "level"],
  "additionalProperties": false
}
~~~

Presence and bounds survive; recovering original width requires metadata.

# Apache Avro {#avro}

This section uses Avro 1.12.0 {{AVRO}}, excluding remote procedure calls,
container compression, and registry identifiers. Non-record roots are legal;
wrappers for surrounding-system restrictions MUST be separately declared.

## JSON Structure to Avro {#structure-to-avro}

### Primitive Types {#avro-primitives}

| JSON Structure type | Avro carrier | Qualification |
|:--------------------|:-------------|:--------------|
| `null` | `null` | Same null value |
| `boolean` | `boolean` | Same value set |
| `string` | `string` | Same Unicode text values |
| `integer`, `int32` | `int` | Same signed 32-bit domain |
| `int8`, `int16`, `uint8`, `uint16` | `int` | Source range is not enforced natively |
| `uint32` | `long` | Full value domain fits; unsigned bound is not native |
| `int64` | `long` | Native signed 64-bit integer |
| `uint64` | `bytes` with `logicalType: "decimal"` | Precision 20, scale 0; retain unsigned 64-bit bounds |
| `int128` | `bytes` with `logicalType: "decimal"` | Precision 39, scale 0; retain signed 128-bit bounds |
| `uint128` | `bytes` with `logicalType: "decimal"` | Precision 39, scale 0; retain unsigned 128-bit bounds |
| `float8`, `float` | `float` | Exact-value and source-domain qualifications apply |
| `double`, `number` | `double` | Generic numbers can lose precision |
| `binary` | `bytes` | Native octet sequence |
| `decimal` | `bytes` with `logicalType: "decimal"` | Derive compatible precision and scale |
| `date` | `int` with `logicalType: "date"` | Shared calendar domain |
| `datetime` | `long` with a timestamp logical type | Declared instant, range, and precision profile |
| `time` | A compatible native time binding or an explicit carrier | Offset and precision must remain accounted for |
| `duration` | `fixed(12)` with `logicalType: "duration"` for the compatible component domain | Other duration domains need a carrier |
| `uuid` | `string` with `logicalType: "uuid"` | Domain depends on supported UUID semantics |
| `uri`, `jsonpointer` | `string` | Semantic restrictions need additional validation |
| `any` | A generic reader union with an extensible named record | A concrete writer schema supplies extensions; see {{avro-any}} |

`uint64` MUST NOT use `long` unless an explicit restricted profile fits its
signed range. Wide integers use {{avro-wide-integers}}.

### Wide Integers {#avro-wide-integers}

The standard projection MUST use `decimal` on `bytes`, scale 0, precision
20 for `uint64` and 39 for either 128-bit type. The exact integer is the
unscaled coefficient: signed big-endian two's complement, including a leading
zero octet when needed for a positive value. Avro supplies the byte-count prefix.

~~~ json
{
  "type": "record",
  "name": "WideIntegers",
  "namespace": "example",
  "fields": [
    {
      "name": "unsigned64",
      "type": {
        "type": "bytes", "logicalType": "decimal",
        "precision": 20, "scale": 0
      }
    },
    {
      "name": "signed128",
      "type": {
        "type": "bytes", "logicalType": "decimal",
        "precision": 39, "scale": 0
      }
    },
    {
      "name": "unsigned128",
      "type": {
        "type": "bytes", "logicalType": "decimal",
        "precision": 39, "scale": 0
      }
    }
  ]
}
~~~

A declared `fixed` profile MAY substitute at least 9 octets for precision 20
or 17 for precision 39. A 16-octet precision-39 declaration is invalid.
Bindings or validators MUST enforce original bounds, including unsigned
non-negativity. Unsupported precision MUST fail, not round, truncate, or
pass through binary64. Reconstruction MUST retain integer identity and bounds;
decimal parameters cannot distinguish the two 128-bit types.

### Logical Types {#avro-logical-types}

Logical types MUST use their specified carriers: `decimal` on `bytes`/`fixed`,
`date` and `time-millis` on `int`, timestamps on `long`. Unknown annotations
are ignored by ordinary readers.

An Avro decimal has precision p and scale s; its value is coefficient times
10^(-s). Projections MUST derive compatible
p > 0 and 0 <= s <= p from effective source constraints, without rounding;
otherwise they MUST declare an alternative carrier or reject lossless native
conversion. Decimal text needs numeric validation.

For n-octet `fixed`, precision cannot exceed
floor(log10(2^(8*n-1)-1)). Size, padding, and sign extension MUST conform.
Core decimal output MUST restore a fractional part, including `.0` at scale
zero; lexical zeroes need not survive.

* `date`: signed days since 1970-01-01, within both calendar domains.
* `time-millis`/`time-micros`: offset-free time since midnight; Core
  `full-time` has an offset. Dropped offsets or fractions are losses.
* `timestamp-millis`/`timestamp-micros`/`timestamp-nanos`: signed epoch
  counts. Bindings MUST check range, precision, and leap seconds under
  a declared instant policy; original offsets and text do not survive.
* `local-timestamp-millis`/`local-timestamp-micros`/`local-timestamp-nanos`:
  no zone; UTC MUST NOT be assumed.
* `duration`: unsigned little-endian `uint32` months, days, and milliseconds
  in 12 octets, not a general signed duration.

Unmatched temporal bindings MUST identify preserved components and validate
temporal text.

### Records, Optionality, and Collections {#avro-records}

Objects become records, properties become fields, and namespaces remain
distinct. Recursive records MUST follow legal Avro definition order.
Open objects need an extra-properties map with externally enforced collision
exclusion; Avro records have no `additionalProperties`.

Every field has a value. For non-null T:

| Source property | Avro field representation |
|:----------------|:--------------------------|
| Required T | T |
| Required nullable T | Union of `null` and T |
| Optional T | Union of `null` and T; null represents absence |
| Optional nullable T | Union of `null` and a named present-value record whose `value` field has union type `null` and T |

In the last row, outer null means absence. A present record whose `value`
is null means present null.

Wrapper names MUST be unique. Optional fields MAY default to null with
null first. Optional-nullable flattening to `["null", T]` MUST report
value collapse; other null-as-absence bindings require source correspondence.

Arrays and string-keyed maps project recursively. Sets use arrays with
external uniqueness; collection bounds need validation. Tuples use fields
in `tuple` order; names and order MUST survive, without inferring object identity.

### Unions, Choices, and Enumerations {#avro-unions}

Direct nested unions and duplicate unnamed branch kinds require distinct
named record wrappers. Processors MUST add these where necessary and apply
Core first-match selection before choosing the Avro branch.

Tagged choices SHOULD use distinct named one-field records, unwrapped on
import. A discriminator plus independent union needs correlation validation.
Inline choices use materialized branch records; selectors MUST be restored.
Defaults do not enforce selector constants.

String enums require legal unique symbols or reversible mappings. Numeric
enums MUST NOT become symbols without explicit transformation. `const`,
patterns, lengths, narrower bounds, required-set alternatives, and composition
generally need validation beyond Avro.

### Extensible Values and Schema Evolution {#avro-any}

The projection of `any` uses different reader and writer schemas. The generic
reader includes an empty named record in its union. A concrete writer adds
typed fields to that record without changing the reader.
Primitive, array, or map branches MAY predeclare understood categories;
they do not close the source domain.

Union order and promotions matter: `string` can resolve as `bytes`, or
vice versa. Value-preserving bindings MUST use named wrappers or retain
writer-branch identity and its exact inverse, not infer categories from
resolved values.

Each extensible position MUST have a stable unique full name; writers MUST
retain it and enclosing reader compatibility. Unmatched new union branches
fail resolution. Written fields MUST have concrete types; decoding MUST use
the actual writer schema as well as the reader.

This reader uses the extension record for binary and collection values:

~~~ json
{
  "type": "record",
  "name": "Message",
  "namespace": "example",
  "fields": [
    { "name": "id", "type": "long" },
    {
      "name": "payload",
      "type": [
        "null", "boolean", "int", "long",
        "float", "double", "string",
        {
          "type": "record",
          "name": "Message_payload_AnyValue",
          "fields": []
        }
      ]
    }
  ]
}
~~~

A later writer adds typed payload fields:

~~~ json
{
  "type": "record",
  "name": "Message",
  "namespace": "example",
  "fields": [
    { "name": "id", "type": "long" },
    {
      "name": "payload",
      "type": [
        "null", "boolean", "int", "long",
        "float", "double", "string",
        {
          "type": "record",
          "name": "Message_payload_AnyValue",
          "fields": [
            { "name": "label", "type": "string" },
            {
              "name": "counter",
              "type": {
                "type": "bytes", "logicalType": "decimal",
                "precision": 20, "scale": 0
              }
            }
          ]
        }
      ]
    }
  ]
}
~~~

The reader retains `id` but skips `label` and `counter`. New-writer/old-reader
compatibility needs no defaults; new-reader/old-writer requires defaults for
missing required reader fields.

Bindings MUST specify extension-field and synthetic-wrapper meanings,
select the intended branch, and encode all data under the actual writer
schema, without rounding to fit floating branches. Every value MUST conform
to that schema; schema changes require identification or framing.

Skipped fields are lost even when the decoder knows the writer schema.
The empty result MUST NOT claim canonical reconstruction without independent
evidence. Value-preserving imports or relays MUST decode writer fields with
a writer-aware binding or retain the encoded datum and writer schema.

## Avro to JSON Structure {#avro-to-structure}

### Types and Names {#avro-reverse-types}

Primitives map directly, except `int` -> `int32`, `long` -> `int64`,
and `bytes` -> `binary`. Non-finite values need tagged representations or
rejection of the source domain; finite-only restrictions MUST be reported.

Records become closed objects with every field required, including nullable
or defaulted fields. Empty records use map-of-`any` with Validation
`maxEntries: 0`; identifying original `any` needs retained correspondence.

Arrays, maps, and named references map directly after full-name resolution.
Enums become string enums; round trips retain symbol order.
`fixed` becomes binary with exact octet count; `maxLength` MUST NOT claim
to reconstruct that binary size.

Unions MAY become Core unions without required identity loss; otherwise
tagged choices MUST retain branches, including `int`/`long` distinctions
for equal values. Array/map union members require reusable type references.

### Logical Types and Resolution Metadata {#avro-reverse-logical}

Retained wide-integer bindings MUST recover the declared integer type after
checking precision, scale, integrality, and bounds. Otherwise decimal import
MUST preserve bounds +/-(10^p-1)*10^(-s) and quantum 10^(-s).
The quantum is the difference between adjacent representable decimal values.
One exact construction uses Core precision p for s > 0, p+1 for s = 0,
scale max(1,s), those bounds, and Validation `multipleOf` equal to the quantum.
For p=2, s=2, bounds are -0.99..0.99 and quantum 0.01. Decimal constraints
retain fractional syntax, including `.0`; documents MUST declare
`JSONStructureValidation` in `$uses`. Unsupported parameters MUST be diagnosed,
not narrowed.

Round trips MUST retain carrier size and logical identity. `big-decimal`
MUST use declared variable-scale semantics or report restriction.
UUIDs require shared domains; fixed 16-octet versus textual carriers need metadata.
Dates MAY use the shared calendar domain; timestamps MAY use explicit UTC
formatting with unsupported ranges/fractions rejected or diagnosed.
Timezone-free types MUST NOT invent offsets. Durations MAY use `uint32`
month/day/millisecond objects or tuples; duration text needs semantic and range
equivalence.

Unknown logical types MUST retain underlying types and annotations; malformed
annotations MUST NOT justify recognized Core types.
Aliases, field/sort order, enum/field defaults, and resolution rules need
metadata. Resolution defaults do not permit omitted writer fields; conversion
to Validation defaults MUST qualify behavioral changes. Byte/fixed defaults
MUST be decoded by Avro's rules before Core encoding.

## Example and Preservation Boundary {#avro-example}

This model has an `int64` identifier and required nullable note:

~~~ json
{
  "$schema": "https://json-structure.org/meta/core/v0/#",
  "$id": "https://example.com/schemas/Contact",
  "name": "Contact",
  "$root": "#/definitions/example/Contact",
  "definitions": {
    "example": {
      "Contact": {
        "name": "Contact",
        "type": "object",
        "properties": {
          "id": { "type": "int64" },
          "note": { "type": ["null", "string"] }
        },
        "required": ["id", "note"],
        "additionalProperties": false
      }
    }
  }
}
~~~

Its Avro projection is:

~~~ json
{
  "type": "record",
  "name": "Contact",
  "namespace": "example",
  "fields": [
    { "name": "id", "type": "long" },
    { "name": "note", "type": ["null", "string"] }
  ]
}
~~~

`id` is supplied as an integer; `note` selects null or string.
An Avro default would not make `note` optional.

# Protocol Buffers {#protobuf}

Generation uses proto3 {{PROTO3}} with explicit presence; import also covers
proto2 {{PROTO2}}. Editions features MUST resolve explicitly, not inherit
proto3 presence or enum assumptions. Mappings MUST distinguish decoded-message
values, binary layout, and ProtoJSON {{PROTOJSON}}. Services and methods are
outside scope.

## JSON Structure to Protocol Buffers {#structure-to-protobuf}

### Primitive Types {#protobuf-primitives}

| JSON Structure type | Protocol Buffers carrier | Qualification |
|:--------------------|:-------------------------|:--------------|
| `boolean` | `bool` | Same boolean values |
| `string` | `string` | UTF-8 text |
| `binary` | `bytes` | Native octet sequence |
| `integer`, `int32` | `int32` | Same signed 32-bit domain |
| `int8`, `int16` | `int32` | Narrower bounds need validation |
| `uint8`, `uint16` | `uint32` | Narrower bounds need validation |
| `uint32` | `uint32` | Same unsigned 32-bit domain |
| `int64` | `int64` | Native signed 64-bit integer |
| `uint64` | `uint64` | Full unsigned 64-bit domain |
| `int128`, `uint128` | `string` | Grammar and bounds need validation |
| `float8`, `float` | `float` | Binary32 carrier; source-domain checks apply |
| `double`, `number` | `double` | Generic numbers can be rounded |
| `decimal` | `string` | No native decimal scalar |
| `datetime` | `google.protobuf.Timestamp` under a compatible instant profile | Check range, precision, and offset semantics |
| `date`, `time`, `duration` | A compatible component message or an explicit carrier | No general matching scalar |
| `uuid`, `uri`, `jsonpointer` | `string` | No native scalar constraints |
| `null` | `google.protobuf.NullValue` | Must preserve explicit presence |
| `any` | `string` carrying one JSON value | Exact-number JSON adapter required |

Declared encoding policies MAY select `sint32`/`sint64`; ZigZag and fixed-width
choices change wire identity. `google.protobuf.Empty` is not null.
String carriers do not enforce numeric or semantic restrictions.

`Timestamp` bindings MUST declare instant semantics and check year range,
nanoseconds, offset normalization, and leap seconds without truncation or
invented offsets. Observable offsets need component carriers.
Explicit temporal-text bindings MAY address native type gaps.

### Messages and Presence {#protobuf-presence}

Objects become messages. Scalars use explicit presence (`optional T`);
unset means absence, set includes zero, false, and empty string
{{PROTO-PRESENCE}}. Required scalars MUST additionally be checked for presence.
Message fields already track presence and MUST NOT become implicitly present
empty objects.

Nullable properties SHOULD use a wrapper with null and value `oneof` arms.
Wrapper absence, selected null, and selected value represent the three states.
Bindings MUST reject present wrappers with unset arms, require wrappers for
required properties, and report these non-native checks.

Primitive wrappers lack present null. ProtoJSON parsing MUST NOT claim
presence distinctions absent from decoded messages.
Open objects need separate extra-property maps or entry lists with collision
checks; unknown numbered wire fields are not string-keyed additional properties.

### Collections, Tuples, and Choices {#protobuf-compounds}

Arrays use `repeated`; nested collections MUST use message wrappers.
Nullable items also need wrappers. Optional arrays/maps need outer presence
wrappers; required collections materialize empty values. Maps use
`map<string,V>` with legal or wrapped values; order is irrelevant.
Set uniqueness and collection bounds need validation.

Tuples use named message fields, retained positional order, and member-presence
validation; field numbers do not declare tuple order.

Tagged choices use `oneof` fields, with repeated/map alternatives wrapped.
Bindings MUST restore tags and reject unset groups when choice values are
present: `oneof` enforces at most one, not exactly one. Inline choices also
restore/check selectors after inheritance. Core unions need declared first-match
selection and reversible wrappers, not independent optional fields.

### Field Numbers and Enumerations {#protobuf-identities}

Fields MUST receive unique numbers 1..536870911 except 19000..19999.
Correspondences MUST survive; removed numbers MUST be reserved, not reused.
Initial allocation MAY use lowest available numbers in lexicographic identifier
order; this is not an evolution policy.

Round trips retain packages, nested scopes, JSON names, reservations, and
integer encodings. String enums require explicit symbol/number mappings.
Proto3 requires an initial zero symbol and admits unknown numbers; extra
values need validation. Assignments and symbols MUST survive round trips,
and widening MUST be diagnosed. Numeric aliases lose spelling identity.

Constants, narrower bounds, patterns, lengths, requiredness, uniqueness, and
composition need extra validation. Custom options MUST identify vocabulary
and enforcing processors; they are not standalone proto3 constraints.

## Protocol Buffers to JSON Structure {#protobuf-to-structure}

### Scalar and Enum Mapping {#protobuf-reverse-scalars}

| Protocol Buffers type | JSON Structure type |
|:----------------------|:--------------------|
| `bool` | `boolean` |
| `string` | `string` |
| `bytes` | `binary` |
| `int32`, `sint32`, `sfixed32` | `int32` |
| `uint32`, `fixed32` | `uint32` |
| `int64`, `sint64`, `sfixed64` | `int64` |
| `uint64`, `fixed64` | `uint64` |
| `float` | `float`, for finite representable values |
| `double` | `double`, for finite representable values |

Signedness MUST survive. Encoding choices need metadata; non-finite floats
need explicit carriers or reported restrictions.
Closed enums MAY use numeric enums with retained symbols/aliases.
Open enums MUST admit unknown `int32` values; symbol profiles need an
unknown-number representation.

### Presence and Compound Mapping {#protobuf-reverse-presence}

Proto2 required fields become required properties; optional fields remain
optional non-null, including defaulted scalars. Reading a getter's default
value does not make an unset field present. Validation defaults instead
create missing properties.

Proto3 explicit presence maps unset/set to absent/present. Implicit scalars
materialize effective values as required properties; omission-of-defaults
profiles MUST be declared without claiming original wire presence.
Repeated/map fields become required arrays/maps, materializing empties.
Typed keys need reversible canonical strings and MUST retain signedness,
bounds, and boolean `true`/`false` spellings.

Real `oneof` groups become optional tagged choices named by fields; unset
means absent. Synthetic proto3-optional groups MUST NOT become choices.
These descriptor-only oneofs track scalar presence, not application choices.
Declared-field messages become closed objects; empty messages use
map-of-`any`, Validation `maxEntries: 0`, not null. Preserved unknown wire
fields require an additional numbered-field representation.

### Well-Known Types and ProtoJSON {#protobuf-well-known}

Full `google.protobuf` identities govern well-known types:

* `NullValue`: null with enclosing presence preserved.
* `Timestamp`: UTC datetime; range, nanoseconds, and normalization MUST be checked.
* `Duration`: signed seconds/nanoseconds with bounds and consistent signs;
  calendar text needs a declared profile.
* `Struct`, `ListValue`, `Value`: JSON-like maps, arrays, and values;
  numeric values use binary64.
* `Any`: retain type URL and serialized message, or resolve and project
  the typed payload; it MUST NOT become unconstrained JSON.

ProtoJSON uses strings for 64-bit integers and enum symbols, Base64 for bytes,
and `json_name` or derived names. Its accepted input grammar differs from Core.
Mappings MUST distinguish input syntax, canonical output, and decoded values;
JSON null may leave fields unset.

## Examples and Preservation Boundary {#protobuf-example}

Required `id` and optional `labels` use:

~~~ protobuf
syntax = "proto3";
package example;

message Labels {
  repeated string values = 1;
}

message Packet {
  optional uint64 id = 1;
  Labels labels = 2;
}
~~~

Unset `labels` means absence; present empty `Labels` means an empty array.
Validation requires `id` presence.

Conversely:

~~~ protobuf
syntax = "proto3";
package example;

message Reply {
  oneof result {
    string text = 4;
    uint32 count = 9;
  }
}
~~~

The declared-field import is:

~~~ json
{
  "$schema": "https://json-structure.org/meta/core/v0/#",
  "$id": "https://example.com/schemas/Reply",
  "name": "Reply",
  "type": "object",
  "properties": {
    "result": {
      "name": "ReplyResult",
      "type": "choice",
      "choices": {
        "text": { "type": "string" },
        "count": { "type": "uint32" }
      }
    }
  },
  "required": [],
  "additionalProperties": false
}
~~~

Empty maps to `{}`; selected zero maps to `{"result":{"count":0}}`.
Numbers 4 and 9 need metadata. Preservation claims MUST qualify wrapper states,
unset oneofs, enum openness, and external validation, and distinguish decoded
values from binary or ProtoJSON compatibility.

# XML Schema (XSD) {#xsd}

This section uses XSD 1.1 {{XSD-STRUCTURES}} {{XSD-DATATYPES}}.
XSD 1.0 profiles MUST diagnose unavailable assertions, timezone constraints,
and other features. An expanded name combines a namespace URI and a local
name, independent of the namespace prefix.
Instances comprise expanded names, typed content, and
relevant order; namespace prefixes MUST resolve before reference mapping.

## JSON Structure to XSD {#structure-to-xsd}

### Primitive Types {#xsd-primitives}

| JSON Structure type | XSD representation |
|:--------------------|:-------------------|
| `boolean` | `xs:boolean` |
| `string` | `xs:string`, subject to XML character restrictions |
| `integer`, `int32` | `xs:int` |
| `int8`, `uint8` | `xs:byte`, `xs:unsignedByte` |
| `int16`, `uint16` | `xs:short`, `xs:unsignedShort` |
| `uint32` | `xs:unsignedInt` |
| `int64`, `uint64` | `xs:long`, `xs:unsignedLong` |
| `int128`, `uint128` | `xs:integer` restricted to the Core bounds |
| `number` | `xs:decimal` for exact finite decimal-number values |
| `float` | `xs:float`, with the JSON-compatible domain |
| `double` | `xs:double`, with the JSON-compatible domain |
| `float8` | A wider floating carrier with retained source restrictions |
| `decimal` | A restricted `xs:decimal`, when facet semantics agree |
| `binary` | `xs:base64Binary` or `xs:hexBinary` with an encoding adapter |
| `date`, `time`, `datetime` | Compatible restrictions of `xs:date`, `xs:time`, `xs:dateTime` |
| `duration` | `xs:duration`, only over the shared value and lexical domain |
| `uuid`, `jsonpointer` | Restricted `xs:string` |
| `uri` | Restricted `xs:anyURI` or `xs:string` |
| `null` | A nilled element, not a simple datatype |
| `any` | A declared JSON-value representation, not unrestricted `xs:anyType` |

Integer facets retain exact {{numeric-representations}} bounds.
Exponent notation MUST expand exactly to decimal text without requiring every
value to pass through binary64; `double` alternatives retain the common
precision qualifications.
Constraint-preserving floats MUST exclude non-Core values and retain or
diagnose narrower domains.

`totalDigits`/`fractionDigits` equivalence MUST be checked; fractional digits
are upper bounds, not display widths. Reversal MUST restore Core's fractional
syntax. Temporal restrictions MUST enforce Core calendars, lexical forms,
{{RFC3339}} offsets for time/datetime, and timezone-free dates.

`xs:anyURI` does not assert {{RFC3986}} validity; compatible restrictions
or validation are needed. UUID {{RFC9562}} and JSON Pointer lack built-in types.

### XML Text and Binary Values {#xsd-text}

XML 1.0 cannot carry U+0000. Out-of-repertoire characters MUST be rejected
or reversibly encoded, for example as Base64 UTF-8. Escaping MUST account
for line-ending and attribute normalization, not just `<` and `&`.

Binary bindings supply octets; binary length facets count octets, not encoded
text. Compression/media-type metadata MUST NOT trigger decompression without
a selected transformation.

### Objects, Properties, and Nullability {#xsd-objects}

Objects use complex types with `xs:all` singleton property elements:
`minOccurs` is 1 when required, 0 otherwise; `maxOccurs` is 1.
Collections stay inside property containers ({{xsd-collections}}).
Nullable elements use `nillable="true"`; absent, nilled, and empty non-nilled
states MUST remain distinct. Null-only types MUST enforce nil-only behavior
or diagnose widening, not become unrestricted strings.

Closed objects MUST NOT gain unrestricted wildcards. Open objects need key/value
collections; bindings MUST preserve arbitrary string keys and prevent
declared-property collisions. `xs:any` requires separate treatment of expanded
names, namespaces, and `processContents`, not an assumed JSON-map equivalence.

Explicit layouts MAY use attributes, which cannot be nilled or carry arbitrary
complex values. Nullable attributes need carriers or restrictions.
Round trips MUST retain node kinds and expanded names.

### Arrays, Maps, Tuples, and Choices {#xsd-collections}

Arrays contain `item` sequences with occurrence bounds, `unbounded` when
no maximum exists. Item and container nullability are independent; optional
containers distinguish absence from emptiness. Sets need equivalent JSON-equality
validation; scalar `xs:unique` MUST NOT claim arbitrary-value uniqueness.

Maps contain repeated `entry` elements with text `key` and typed `value`
children, including empty/punctuated keys within the character repertoire.
Supported `xs:unique` MUST enforce key uniqueness with namespace-correct paths.
Tuples use `xs:sequence` in positional order, each occurrence exactly one.
Tagged choices use `xs:choice` occurring exactly once, with singleton tagged
elements. Inline choices need materialized branches and exact selector correlation.

### Facets, Inheritance, and Names {#xsd-facets}

Bounds, lengths, and enumeration facets require matching value spaces,
units, and whitespace behavior. Node-defaulting `fixed` MUST NOT substitute
for general `const`. ECMAScript patterns MUST be translated to equivalent
XSD matching or reported unsupported.

Compatible abstract types and inheritance MAY use XSD derivation; extension
appends XML content, not unordered properties. Materialization must retain
or report inheritance identity. Namespace-URI correspondence MUST be explicit;
expanded names are URI/local-name pairs, requiring reversible legal identifiers.
Descriptions use `xs:documentation`; identified `xs:appinfo` or companion
metadata MAY retain other annotations without enforcing them.

## XSD to JSON Structure {#xsd-to-structure}

### Simple Types {#xsd-reverse-primitives}

| XSD type | JSON Structure representation |
|:---------|:------------------------------|
| `xs:boolean` | `boolean`, after XML lexical parsing |
| `xs:string` and string-derived types | `string`, retaining restrictions and whitespace rules |
| `xs:byte`, `xs:short`, `xs:int`, `xs:long` | `int8`, `int16`, `int32`, `int64` |
| `xs:unsignedByte`, `xs:unsignedShort`, `xs:unsignedInt`, `xs:unsignedLong` | `uint8`, `uint16`, `uint32`, `uint64` |
| Bounded `xs:integer` derivatives | A containing Core integer type plus retained bounds |
| Unbounded `xs:integer` derivatives | An exact decimal-string carrier with retained numeric interpretation |
| `xs:decimal` | `decimal` only with compatible finite precision and scale; otherwise an exact carrier |
| `xs:float`, `xs:double` | `float`, `double` over the supported finite subset |
| `xs:base64Binary`, `xs:hexBinary` | `binary`, after decoding to octets |
| Compatible temporal restrictions | The corresponding Core temporal type |
| `xs:anyURI` | `string` unless Core URI-reference validity is established |
| `xs:QName`, `xs:NOTATION` | A representation preserving expanded-name identity |

Unbounded integer derivatives MUST NOT acquire finite widths, nor unbounded
decimals Core's default precision/scale. Arbitrary-precision processing MAY
use `number`; explicit numeric text MAY serve as an alternative, with type loss
qualified.

Processors MUST identify lexical versus post-validation values; normalized
whitespace cannot claim lexical recovery. Lists become arrays only with defined
tokenization/item mappings. Union member identity MUST survive where assigned;
overlap cannot be assumed harmless. QName values need namespace bindings
or URI/local-name carriers.

### Elements, Attributes, and Content Models {#xsd-reverse-structure}

Selected global elements become roots; global collections MUST NOT select an
arbitrary first root. Element/type identities MUST remain separate.
Elements and attributes become properties under the presence rules above;
containerless repeated particles become bounded arrays, including empty arrays.
Required attributes are non-null. Node roles/namespaces MUST survive, with
distinct identifiers for same-local-name attributes and elements.

Simple content with attributes needs a dedicated value property whose name
MUST avoid collisions and identify its synthetic role.
Compatible `xs:all` singletons become unordered objects; fixed sequences can
be tuples, otherwise ordering needs a carrier or loss report.
Distinguishable choices become tagged choices, optional or repeated as needed;
multi-element branches MUST NOT become unrelated leaves.

Mixed content needs an explicit XML-content profile: ordered tagged text/element
events retaining names and values, not unordered properties or concatenated text.

### XML-Specific Semantics {#xsd-reverse-semantics}

Validation can add default values and type information; this is called
augmentation.
Processors MUST state default/augmentation state, not claim original absence
from defaulted nodes. Substitution groups, `xsi:type`, type alternatives,
wildcard namespaces, `processContents`, identity constraints including
`xs:keyref`, and assertions MUST receive equivalent representations or loss
diagnostics. `xs:anyType` needs a defined XML-to-JSON representation for
attributes and mixed content.

## Example and Preservation Boundary {#xsd-example}

Required `name` and optional nullable `age` use:

~~~ xml
<xs:schema xmlns:xs="http://www.w3.org/2001/XMLSchema">
  <xs:element name="Person">
    <xs:complexType>
      <xs:all>
        <xs:element name="name" type="xs:string"/>
        <xs:element name="age" type="xs:int"
                    minOccurs="0" nillable="true"/>
      </xs:all>
    </xs:complexType>
  </xs:element>
</xs:schema>
~~~

Either child order is valid. Omission means absence;
`<age xsi:nil="true"/>` means present null with `xsi` bound to
`http://www.w3.org/2001/XMLSchema-instance`; empty non-nilled `age` is invalid.

# CDDL {#cddl}

CDDL {{RFC8610}} processors MUST identify JSON versus Concise Binary Object
Representation (CBOR) {{RFC8949}} interpretation. CBOR adds octets, tags,
undefined, non-finite floats, and non-text keys. Additional controls, including
{{RFC9165}}, need identified, enforced vocabularies.

The primary profile binds native CBOR logical values.
Core's existing CBOR mapping instead follows {{cddl-json-profile}}, except
binary uses `bstr`; wide integers and decimals are not wire-compatible with
this native profile. Processors MUST identify the binding, not infer it from
the media type.

## JSON Structure to CDDL {#structure-to-cddl}

### Primitive Types in the CBOR Profile {#cddl-primitives}

| JSON Structure type | CDDL representation |
|:--------------------|:--------------------|
| `null` | `nil` |
| `boolean` | `bool` |
| `string` | `tstr` |
| `integer`, `int8`, `uint8`, `int16`, `uint16`, `int32`, `uint32` | The exact integer range in {{numeric-representations}} |
| `int64`, `uint64` | The exact integer range in {{numeric-representations}} |
| `int128`, `uint128` | Bounded native integers and tagged bignums as specified below |
| `float` | `float32`, with a JSON-compatible finite domain |
| `double` | `float64`, with a JSON-compatible finite domain |
| `float8` | A wider float carrier with retained source restrictions |
| `number` | `number`, only over its exactly representable subset |
| `decimal` | Tag 4 decimal fraction with compatible numeric restrictions, or an explicit alternative carrier |
| `binary` | `bstr` |
| `date`, `time`, `datetime`, `duration` | `tstr` with the source temporal restrictions |
| `uuid`, `uri`, `jsonpointer` | `tstr` with the source semantic restrictions |
| `any` | A JSON-compatible recursive value rule, not unrestricted CBOR `any` |

CBOR `int` spans -18446744073709551616..18446744073709551615; narrower Core
bounds MUST survive. `uint .size n` bounds values, not encoded width.
The complete 128-bit domains use:

~~~ cddl
js-uint128 = uint / #6.2(bstr .size (0..16))

js-b127 = (bstr .size (0..15)) /
          ((bstr .size 16) .bits (0..6 / 8..127))

js-int128 = int / #6.2(js-b127) / #6.3(js-b127)
~~~

Tag 2 carries unsigned big-endian n; tag 3 means -1-n. For signed 16-octet
magnitudes, `.bits` excludes first-octet bit 7, limiting n to 2^127-1.
Larger byte strings are excluded even with redundant zeroes.
Explicit `tstr` alternatives MAY be used but MUST enforce grammar and bounds.

CDDL `number` admits integers/floats, not arbitrary JSON decimals; silent
rounding MUST NOT occur. Tag-4 alternatives MAY use representable exponent
and mantissa components. Decimal bindings MUST enforce effective precision,
scale, and bounds on mantissa*10^exponent, with compatible digit semantics;
unconditional `float64` is forbidden. Tool-limited text alternatives need exact
parsing/formatting, not a choice based on Core's JSON spelling.

Binary binds to octets. Semantic tags MAY use declared profiles whose identity
and content MUST be checked; tag-0 text does not validate {{RFC3339}} syntax.
Core `any` MUST use recursive JSON-compatible arrays, text-keyed maps, and
supported scalars, excluding undefined, non-finites, and arbitrary tags unless
explicitly represented reversibly.

### Maps and Presence {#cddl-maps}

Objects use literal text keys: occurrence one for required, `?` for optional,
and `/ nil` for nullable:

~~~ cddl
presence-example = {
  "required": tstr,
  ? "optional": tstr,
  "required-nullable": tstr / nil,
  ? "optional-nullable": tstr / nil
}
~~~

Closed objects omit wildcards; open objects add projected extra-value rules.
Known keys MUST use cuts, supplied by literal-key colon notation, to prevent
wildcard bypass. Here the cut commits `level` to `0..255`; a string-valued
`level` cannot fall back to the wildcard:

~~~ cddl
open-reading = {
  ? "level": 0..255,
  * tstr => any
}
~~~

Here `any` requires a full-CBOR extra-value profile; Core
`additionalProperties: true` MUST instead use {{cddl-primitives}}' recursive
JSON domain.

Maps use `{ * tstr => V }`. Bindings MUST reject duplicate logical keys
and MUST NOT add numeric/tagged keys. Alternative required sets MUST enforce
exactly-one-complete-set semantics or report residual validation.

### Arrays, Tuples, Unions, and Choices {#cddl-compounds}

Arrays use `[* T]`, bounded `[m*n T]`, or open occurrence ranges.
Tuples fix members in `tuple` order, retaining names as metadata.
Sets need residual JSON-equality uniqueness retained or reported; distinct
map keys MUST NOT substitute for arbitrary-value set equality.

Unions use `/`, retaining order/selection policy for overlapping Core branches.
Tagged choices use closed singleton maps; inline choices require literal
selectors in materialized maps, not optional unconstrained strings.
Named types become rules. Finite-valued recursion MAY survive; processors
MUST NOT replace cycles with `any` or reject all recursion because of cycles.

### Controls and Annotations {#cddl-controls}

Compatible bounds use ranges/comparisons. Exclusive integer limits MAY shift
by one using exact arithmetic; floating/decimal limits MUST NOT.
Constants/enums use literals/type choices and MUST follow the instance binding.

Text `.size` counts UTF-8 octets: U+00E9 is one character, two octets.
Character limits MUST NOT be copied without unit equivalence.
`.regexp` needs ECMAScript-to-XSD translation; all chained constraints MUST
survive through controlled types or intersections. `.cbor`/`.cborseq` constrain
decoded content, unlike media-type annotations.

Round-trip metadata includes groups, generics, sockets, and comments within scope;
`altnames.cddl` retains rule spelling only.

## CDDL to JSON Structure {#cddl-to-structure}

### Primitive and Tagged Values {#cddl-reverse-primitives}

| CDDL construct in the CBOR profile | JSON Structure representation |
|:----------------------------------|:------------------------------|
| `bool`, `true`, `false` | `boolean`, with `const` for a literal |
| `nil`, `null` | `null` |
| `tstr`, `text` | `string` |
| `bstr`, `bytes` | `binary` with an encoding adapter |
| `uint` | `uint64`, not `int64` |
| `nint` | `int128` with the exact native-negative range |
| `int` | `int128` with the exact native-integer range |
| A bounded integer range | A containing Core type with retained bounds |
| Unbounded `integer` or bignum rules | An exact numeric carrier; no arbitrary finite-width assumption |
| `float16` | `float` with the narrower source domain retained |
| `float32`, `float64` | `float`, `double` over the supported finite domains |
| `undefined` | No Core null-equivalent value |
| General `any` | No unrestricted JSON-equivalent value domain |

`nint` bounds -18446744073709551616..-1 MUST survive; `int` uses
{{cddl-primitives}} bounds, encoded as Core `int128` constraint strings.
Carrier width MUST NOT replace source bounds. Widened `float16` needs
representability constraints, not just magnitude limits.

Undefined MUST NOT become null. Unknown tags MUST NOT disappear. Tagged-value
profiles MAY use tag-number/payload objects or tuples but MUST retain
distinctions. Tag-4 decimals, tag-0 date-time, tag-1 epoch time, and binary
UUIDs MUST retain their individual semantics, ranges, and representations.
Prelude `time` is not Core {{RFC3339}} time-of-day.

### Structures and Evaluation {#cddl-reverse-structure}

Known text keys become objects; homogeneous text-keyed maps become maps.
Other keys need typed entries or rejection: stringifying 1 and `"1"` collapses
identity. Occurrences determine presence/bounds; nil means null, not absence.
Repeated arrays become arrays; fixed groups become named tuples. Variable,
optional, or repeated-group layouts MUST retain adapters/validation or be rejected.

`/` is an inclusive type choice. A group choice `//` selects a complete
group of entries. `//` MUST preserve groups, not unrelated leaves.
`&(a: 1, b: 2)` enumerates 1 and 2, with metadata labels.
`~` MUST be evaluated before projection, not interpreted as inheritance.
Generics MAY be materialized; generic declarations within the claimed
round-trip scope MUST survive.
`/=`/`//=` extensions and sockets MUST be assembled first; unknown required
controls, unresolved sockets, or unavailable feature conditions prevent losslessness.

Size, regexp, intersection, and decoded-content semantics MUST survive.
`.cbor` MUST NOT become only a media-type annotation; decoding/re-encoding MUST NOT
claim original octet preservation.

## JSON Representation Profile {#cddl-json-profile}

JSON profiles MUST use Core encodings: binary as encoded `tstr`, wide integers
and decimals as grammar/range-constrained `tstr`, small integers as bounded
JSON numbers. Floating constraints concern JSON values, not CBOR widths.
Tags, undefined, non-finites, and non-text keys need separate JSON encodings
or exclusion. RFC 8610 limits JSON numeric precision; accepted subsets or
alternative carriers MUST be identified.
Switching native/JSON profiles transforms instances, not necessarily logical values.

## Example and Preservation Boundary {#cddl-example}

A sequence and binary payload:

~~~ json
{
  "$schema": "https://json-structure.org/meta/core/v0/#",
  "$id": "https://example.com/schemas/Frame",
  "name": "Frame",
  "type": "object",
  "properties": {
    "sequence": { "type": "uint64" },
    "payload": { "type": "binary" }
  },
  "required": ["sequence", "payload"],
  "additionalProperties": false
}
~~~

project to native CBOR:

~~~ cddl
frame = {
  "sequence": 0..18446744073709551615,
  "payload": bstr
}
~~~

Native bindings round-trip 18446744073709551615 and payload octets.
JSON instead uses constrained digit text and Base64; width/encoding identity
needs metadata.

# Conformance and Verification {#conformance}

Processors MUST implement {{projection-model}} and identify format and profile.
Support MAY be partial if unsupported constructs are rejected or reported
under explicit lossy policies. Verification MUST cover the declared domain,
not observed samples.

## Required Verification Cases {#verification-cases}

The following cases are a minimum; verification also covers every applicable
source constraint.

| Source condition | Required observation |
|:-----------------|:---------------------|
| `uint8` at 0 and 255; then -1 and 256 | Endpoints survive; out-of-range values are rejected by a claimed equivalent constraint |
| `uint64` at 18446744073709551615 | No signed conversion, overflow, or binary64 rounding |
| `int128` at -170141183460469231731687303715884105728 | Exact value and sign survive |
| Optional nullable property | Absence, present null, and present value remain distinct |
| Optional array or map | Absence and a present empty collection remain distinct |
| Overlapping type-union members | Core's first-match identity is retained or its loss is reported |
| Alternative required sets | Instances satisfying neither or more than one complete set are rejected |
| Fixed tuple of length n | Both n-1 and n+1 items are rejected |
| Decimal fraction beyond binary64 precision | No implicit binary rounding |
| An offset date-time with a fractional second | Offset text and fractional precision are preserved, or their changes are reported |
| One-character U+00E9 string with a length limit | Character count is not mistaken for UTF-8 octet count |
| Binary value with a known octet count | Decoded size is not mistaken for encoded text length |
| An unknown type, reference, or required vocabulary | Explicit error or loss diagnostic, not a permissive default |
| An Avro nullable field with a default | It remains present in the required-field record model |
| A proto3 optional scalar set to zero | Explicit presence survives |
| An unset Protocol Buffers oneof | It is not mistaken for a selected null or a valid required choice |
| XSD object properties in either order under the canonical layout | Both orders are valid |
| A nilled optional XML element | It differs from an absent or empty non-nilled element |
| A known CDDL map key with the wrong value type | An open wildcard does not bypass the known-key constraint |
| Recursive named source types | References remain valid without depth-based type substitution |

Value tests MUST compare D(E(x)) with x under {{value-equivalence}}.
Constraint tests MUST include values outside E(S). Schema tests MUST compare
semantic declarations within scope, including field numbers, namespaces, and defaults.

## Loss Reporting {#loss-reporting}

Reports MUST distinguish exact types from wider carriers, and rejection
from successful projection of a restricted domain. Hidden restrictions
cannot justify lossless claims.

# Security Considerations {#security-considerations}

Applications MUST NOT rely on discarded constraints without replacement
validators. Resolvers SHOULD use explicit retrieval policies, trusted contexts,
and limits; loading XSD MUST NOT enable XML external entities.

Implementations SHOULD bound regex matching and reject unverified translations.
Recursion, bounds, generic expansion, bignums, and exponent expansion SHOULD
have resource limits reported as failures, not truncation or type substitution.

Implementations MUST distinguish content annotations from processing requests
and SHOULD limit decoded size and nesting depth. Metadata, alternate names,
and field-number correspondences MUST be treated as untrusted and checked
for collisions, illegal identifiers, reserved numbers, and inconsistent
declarations.

# IANA Considerations {#iana-considerations}

This document has no IANA actions.

--- back
