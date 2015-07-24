# Autoloading Standard

----

The key words "MUST", "MUST NOT", "REQUIRED", "SHALL", "SHALL NOT", "SHOULD",
"SHOULD NOT", "RECOMMENDED", "MAY", and "OPTIONAL" in this document are to be
interpreted as described in [RFC 2119](http://tools.ietf.org/html/rfc2119).

----

## 1. Overview

Autoloading is a process whereby the file that contains a class definition is automatically
loaded by the PHP runtime, as opposed to manually calling `require`.

In order for autoloading to work, a naming standard must exist that allows for a `ClassName`
to easily map to a `filename.php`

The [Naming](Naming.md) specification is REQUIRED by this standard and
used to inform the autoloading patterns used.

The naming standard outlines the following conventions:

1. Class names MUST use `[PascalCase](https://msdn.microsoft.com/en-us/library/x2dbyw72%28v=vs.71%29.aspx)` 
   naming convention

2. Classes MAY have a namespace

3. Namespaces MUST have a `Vendor` prefix

4. Namespaces MAY have more than one `SubNamespace`

Using the above conventions, this autoloading standards outlines the following rules:

## 2. Specification

1. The term "class" refers to classes, interfaces, traits, and other similar structures.

2. A fully qualified class name has the following form:

        (\<NamespaceName>\)?(\<SubNamespaceNames>)*\<ClassName>

    1. The fully qualified class name MAY have a top-level namespace name,
       also known as a "vendor namespace".

    2. The fully qualified class name MAY have one or more sub-namespace
       names.

    3. The fully qualified class name MUST have a terminating class name.

    4. Underscores have no special meaning in any portion of the fully
       qualified class name.

    5. All class names MUST be referenced in a case-sensitive fashion.

3. When loading a file that corresponds to a fully qualified class name ...

    1. Class names MUST be split by uppercase word, with each part referencing a
       directory, and the last part referencing a file

    2. Class names containing `UPPERCASE` words MUST keep the words in tact

    3. The global namespace `\` (no namespace) MAY map to multiple base directories

    4. It is RECOMMENDED to have only 1 global namespace base directory (user-land app code)

    5. Non-namespaced (user-land) code MUST exist within a global namespace base directory

    6. A contiguous series of one or more leading namespace and sub-namespace
       names, not including the leading namespace separator, in the fully
       qualified class name (a "namespace prefix") corresponds to at least one
       "base directory".

    7. The contiguous sub-namespace names after the "namespace prefix"
       correspond to a subdirectory within a "base directory", in which the
       namespace separators represent directory separators.

    8. File paths MUST be lower-cased to avoid case sensitivity conflicts between file systems

    9. A file MAY exist in a folder by the same name as the file, this allows  for an
       abstract/interface/trait and the concrete implementation to be located in the same folder

4. A file MUST contain one class and one class only

5. Autoloader implementations MUST NOT throw exceptions, MUST NOT raise errors of any level,
   and SHOULD NOT return a value.


## 3. Examples

The table below shows the corresponding file path for a given fully qualified
class name, namespace prefix, and base directory.

N.b. for the purposes of below, assume that `./` refers to the project root, but could technically
be any path.

#### Class name -> file name (3.1)

```
MyClassName                 => my/class/name.php
```

#### Class name + uppercase -> file name (3.2)

```
MyHTTPClass                 => my/http/class.php
```

#### Global namespace class name -> file name (3.4, 3.5)

```
\ControllerFoo              => ./app/controller/foo.php
\ControllerFooActions       => ./app/controller/foo/actions.php
```

#### Namespace -> base path (3.6)

```
Acme\Foo\Bar                => ./vendor/acme/foo-bar
Acme\Screen                 => ./screens/
```

#### Namespace -> multiple base paths (3.6)

```
Acme                        => ./overrides/acme/
Acme                        => ./vendor/acme/
```

#### Sub-namespace -> base path + folder path (3.7)

```
Acme                        => ./vendor/acme/

thus:

Acme\Foo                    => ./acme/foo/
Acme\Bar                    => ./acme/bar/
Acme\Screen                 => ./acme/screen/
```

#### Alternative locations (3.9)

```
ModelDatabase               => ./app/model/database/database.php
ModelDatabaseInterface      => ./app/model/database/interface.php

Acme\Http\Request           => ./vendor/acme/http/request/request.php
Acme\Http\RequestIntefrace  => ./vendor/acme/http/request/interface.php
```

### Further examples

| Fully Qualified Class Name            | Namespace Prefix   | Base Directory           | Resulting File Path
| --------------------------------------|--------------------|--------------------------|-------------------------------------------
| \Acme\Screen\ControllerCartoon.php    | \Acme\Screen       | /path/to/acme/screen/    | /path/to/acme/screen/controller/cartoon.php
| \Acme\Log\Writer\File_Writer          | \Acme\Log          | ./acme-log-writer/lib/   | ./acme-log-writer/lib/writer/file_writer.php
| \Acme\Http\Request                    | \Acme\Http         | ./vendor/acme/http/      | ./vendor/acme/http/request.php
| \Acme\Http\Request                    | \Acme\Http         | ./vendor/acme/http/      | ./vendor/acme/http/request/request.php
| \ControllerDashboard                  | \                  | ./app/                   | ./app/controller/dashboard.php
| \UtilPurger                           | \                  | ./scripts/               | ./scripts/util/purger.php
