
<table>
<tr><td> Pinning Proposal System for Automatic Rebuilds</td><td> Pinning Proposal </td>
<tr><td> Status </td><td> Proposed </td></tr>
<tr><td> Author(s) </td><td> Christopher J. "CJ" Wright &lt;cjwright4242@gmail.com&gt;; Matthew R. Becker &lt;becker.mr@gmail.com&gt;; Marius van Niekerk &lt;marius.v.niekerk@gmail.com&gt;</td></tr>
<tr><td> Created </td><td> Feb 20, 2019</td></tr>
<tr><td> Updated </td><td> Aug 30, 2019</td></tr>
<tr><td> Discussion </td><td> NA </td></tr>
<tr><td> Implementation </td><td> NA </td></tr>
</table>

## Abstract

This CFEP proposes a change to how our internal pinning system works.
To propose a migration in one or more pins a PR will be issued into 
the [pinning feedstock](https://github.com/conda-forge/conda-forge-pinning-feedstock)
 with a yaml expressing the changes to the global 
pinning file into the `migrations` folder.
Once the PR is squashed and merged the bot will take the yaml
to calculate which packages need to be rebuilt and start issuing
PRs with the new pinning as a local pinning file to the feedstocks
in topo order.

This CFEP is taken from @beckermr's work on [conda-forge.github.io](https://github.com/conda-forge/conda-forge.github.io/issues/712) and @mariusvniekerk's work in [conda-smithy](https://github.com/conda-forge/conda-forge.github.io/issues/712).

## Motivation
Currently when we move pinnings we move the pin in the ``conda-forge-pinning-feedstock``.
Then we start a migration using the ``regro-cf-autotick-bot``.
This order of operations causes some issues, when new feedstocks
are created, or existing feedstocks rerendered they receive the new pinnings 
even if their dependencies are not rebuilt with the new pinnings themselves.

## Specification

### The migrations proceed as follows:
1. We make a PR into the ``migrations`` folder in the ``conda-forge-pinning-feedstock`` with a new yaml file representing the migration.
2. Once the change is merged, the bot picks it up, builds a migrator object and begins the migration process
3. A migration PR is issued for a node only if 
    1. The node depends on the changed pinnings.
    2. It has no dependencies that depend on the new pinnings and have not been migrated.
4. Process 3 continues until we determine that the migration is sufficiently complete and the change is applied to the global pinning file via a PR.

### Implementation Details:
Given a feedstock with the following pinnings

```yaml
# Existing pins
a:
  - 1.5
b:
  - 1.2
someflag:
  - disabled
```

There are four classes of pinning we can do in general.

#### 1. Simple Additive Version Migrations

<table>
<tr>
<td>

```yaml
# Existing pins
a:
  - 1.5
b:
  - 1.2
someflag:
  - disabled
```
</td>
<td> with </td>
<td>

```yaml
# migrator
__migrator: kind: version
a:
  - 1.6
b:
  - 1.1
```
</td>
<td> -> </td>
<td>

```yaml
# resulting pins
a:
  - 1.6
b:
  - 1.2
someflag:
  - disabled
```
</td>
</tr>
</table>

These are the most common version migrations and encompass things like moving the pinnings for openssl etc.

Note that the version of `b` was NOT decreased.  
This is intentional as we don't wish to downgrade accidentally.  
In practice many migrators could be in flight and may affect the same version.  
In this case the higher version should always win.  
If we want to downgrade there is a mechanism but it is purposefully clunky and bad.

#### 2. Matrix Version Addition changes

<table>
<tr>
<td>

```yaml
# Existing pins
a:
  - 1.5
python:
  - 3.6
someflag:
  - disabled
```
</td>
<td> with </td>
<td>

```yaml
# migrator
__migrator: kind: version
python:
  - 3.6
  - 3.8a1
```
</td>
<td> -> </td>
<td>

```yaml
# resulting pins
a:
  - 1.5
python:
  - 3.6
  - 3.8a1
someflag:
  - disabled
```
</td>
</tr>
</table>

These are things like the compiler migration and can be thought of as additions to a version.  
These need substantial care when dealing with things like zip_keys.  
But in general it should be feasible to perform for many common cases.

This can also be used to do something like add a new python version to the matrix or remove an existing one.

For each sublist we perform the version comparison of all the elements within that list and come to a final result.

#### 3. Name migrations

Names don't sort well and as such are a bit problematic.  
However we can impose a new name by 
stating what the ordering of names are.  
This allows us to effectively treat a name migration exactly the the same as
a version migration because we have an ordering that we can impose on the system.

<table>
<tr>
<td>

```yaml
# Existing pins
c_compiler:
  - toolchain_c
```
</td>
<td> with </td>
<td>

```yaml
# migrator
__migrator:
  kind: version
  ordering:
    c_compiler:
      - toolchain_c
      - gcc
c_compiler:
  - gcc
```
</td>
<td> -> </td>
<td>

```yaml
# resulting pins
c_compiler:
  - gcc
```
</td>
</tr>
</table>


#### 4. Key removal

Some times we want to perform a migrator where we delete a given value and don't wish to substitute it with a new one.
A deletion migration is designed purely to remove values

<table>
<tr>
<td>

```yaml
# Existing pins
ruamel_yaml:
  - 1.40
numpy:
  - 1.14
```
</td>
<td> with </td>
<td>

```yaml
# migrator
__migrator:
  kind: deletion
ruamel_yaml:
  - 1.40
```
</td>
<td> -> </td>
<td>

```yaml
# resulting pins
numpy:
  1.14
```
</td>
</tr>
</table>

#### 5. Pin downgrade

To downgrade a pin one can use the order parameter

<table>
<tr>
<td>

```yaml
# Existing pins
jpeg:
  - 3.0
```
</td>
<td> with </td>
<td>

```yaml
# migrator
__migrator:
  ordering:
      jpeg:
          - 3.0
          - 2.0
jpeg:
    - 2.0
```
</td>
<td> -> </td>
<td>

```yaml
# resulting pins
jpeg:
  2.0
```
</td>
</tr>
</table>


Key removals followed by additions are a viable way to perform a migration to a lower version.  
We have never done this really.

## Rationale

### Multiple migrations of the same package:
A feedstock can get multiple migration PRs at once, it is up to the feedstock maintainers to choose which PRs to merge in what order.


## Backwards Compatibility
This is fully backwards compatible with the current ecosystem.

## Copyright

All CFEPs are explicitly [CC0 1.0 Universal](https://creativecommons.org/publicdomain/zero/1.0/).
