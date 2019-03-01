# @mariusvniekerk's proposal

This is a general sketch of what I'd like migrators to be able to achieve.  
Effectively this means we can decompose all of our migrations into a small set of primitives 
that we can apply to generate a resulting desired target.

# @mariusvniekerk's propsal

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

## 1. Simple Additive Version Migrations

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
  - 1.56
b:
  - 1.2
someflag:
  - disabled
```
</td>
</tr>
</table>

These are the most common version migrations and encompass things like moving the pinnings for openssl etc

## 2. Matrix Version Addition changes

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
b:
  - 1.2
  - 2.0
```
</td>
<td> -> </td>
<td>

```yaml
# resulting pins
a:
  - 1.56
b:
  - 1.2
  - 2.0
someflag:
  - disabled
```
</td>
</tr>
</table>

These are things like the compiler migration and can be thought of as additions to a version.  
These need substantial care when dealing with things like zip_keys.  But in general it should be
feasible to perform for many common cases.

This can also be used to do something like add a new python version to the matrix or remove an existing one.

For each sublist we perform the version comparison of all the elements within that list and come to a final result.

## 3. Name migrations

Names don't sort well and as such are a bit problematic.  However we can impose a new name by 
stating what the ordering of names are.  This allows us to effectively treat a name migration exactly the the same as
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


## 4. Key removal

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



