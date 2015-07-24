# PHP Naming Standard

> There are only two hard things in Computer Science: cache invalidation and naming things.   
> -- Phil Karlt

**Date: July 14 2015**

----

The key words "MUST", "MUST NOT", "REQUIRED", "SHALL", "SHALL NOT", "SHOULD",
"SHOULD NOT", "RECOMMENDED", "MAY", and "OPTIONAL" in this document are to be
interpreted as described in [RFC 2119](http://tools.ietf.org/html/rfc2119).

----

## 0. Preface

PHP has long had standards for naming conventions, Zend and Pear immediately come to mind. 
These conventions were used for years without problem, even when autoloading came along and picked up
traction.

Since the FIG have come along and introduced the PSR0/4 standards, most package/framework maintainers
have blindly just updated their code to conform (for information on why we propose not to use
PSR0/4 see below). However, these standards that were once in place are still totally usable today.

## 1. Overview

This document describes a specification for naming classes in order to provide structure and facilitate 
autoloading. It is fully interoperable, and can be used in addition to any other naming specification, 
including PSR-0/4. 

Defining a naming standard is vitally important to code structure and organization. 
Having a clearly defined naming standard removes confusion and frees developers to focus on code as 
opposed to how to name things. 

A clear an logical naming standard also serves as a pre-cursor to autoloading, that requires a defined
standard to work.

## 2. Specification

One of the simplest naming convention standards is the [PascalCase]
(https://msdn.microsoft.com/en-us/library/x2dbyw72%28v=vs.71%29.aspx) naming standard.
This follows the cammel case standard AND uppercases the first letter. 

The term "class" refers to classes, interfaces, traits, and other similar structures.

A fully qualified class takes one of the 2 following forms:

	\<ClassName>
	\<NamespaceName>(\<SubNamespaceNames>)*\<ClassName>    

1. The fully qualified class name MAY have a top-level namespace name,
   also known as a "vendor namespace".

2. The fully qualified class name MAY have one or more sub-namespace names.

3. The fully qualified class SHOULD have at least 1 sub-namespace and SHOULD be limited to no more than 3.

4. The fully qualified class name MUST have a terminating class name.

5. Class names must use the [PascalCase](https://msdn.microsoft.com/en-us/library/x2dbyw72%28v=vs.71%29.aspx) standard.

6. All class names MUST be referenced in a case-sensitive fashion.

7. User land code (non-distributable/reusable app code) MUST not be namespaced.

8. Distributable/reusable non user land code MUST be namespaced.

**N.b.** User land code is code that constitutes your app, the code that contains business logic, 
templates, etc, that will never intended to be re-used outside of the app. 
Distributable/reusable code is code that doesn't contain business specific logic, and is potentially 
distributable/reusable, regardless of if it will be distributed/reused. 
Any internal frameworks for example fall in this category.

## 3. Examples

#### User land code

```php
class MyClassName
{

}
```

#### Distributable/reusable non-user land code

```php
namespace MyCompany\Library;

class MyClass
{

}

// or

namespace MyCompany\Component\Validation;

class FilterChain
{

}
```

## Addendum: PSR 0/4

The [PHP FIG](http://www.php-fig.org/) have developed 2 standards for naming conventions, PSR0 and PSR4,
with 0 being deprecated in favor of 4. These standards are flawed in their definition and force developer
to use namespacing incorrectly, making code less readable and harder to maintain. A full writeup is available
here: http://www.makegoodcode.com/2015/06/30/namespacing-in-PHP-and-how-we-broke-it/

TLDR; in summary, these are the problems with the PSR0/4 standards

* Forces over granular namespaces, as namespacing is tied directly to file path

* Breaks the intention of namespaces, forcing a vendor to create multiple useless namespaces within
their package

* Causes over use of `use` statements due to the granular namespaces

* Forces package maintainer to follow this standard in order for their packages to be autoloaded with
composer, or package their own autoloader with their package

* Forces all user land (you app) code to be placed under a namespace when it does not need to
(and should not) be in a namespace whatsoever

It may be tempting to use PSR4 just because a self appointed group says you should structure your
code this way, but unfortunately, with these flawed "specifications" one will end up with worse code.
What happens when the FIG introduce a 3rd iteration of the standard?
