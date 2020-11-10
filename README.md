Pystache
========

<!-- Since PyPI rejects reST long descriptions that contain HTML, -->
<!-- HTML comments must be removed when converting this file to reST. -->
<!-- For more information on PyPI's behavior in this regard, see: -->
<!-- http://docs.python.org/distutils/uploading.html#pypi-package-display -->
<!-- The Pystache setup script strips 1-line HTML comments prior -->
<!-- to converting to reST, so all HTML comments should be one line. -->
<!-- -->
<!-- We leave the leading brackets empty here.  Otherwise, unwanted -->
<!-- caption text shows up in the reST version converted by pandoc. -->
![GitHub CI Workflow Status](https://img.shields.io/github/workflow/status/sarnold/pystache/ci)

This fork of Pystache is currently tested on Python 2.7 and Python 3.6+ on Linux, Darwin, and Windows.

![](http://defunkt.github.com/pystache/images/logo_phillips.png "mustachioed, monocled snake by David Phillips")

[Pystache](http://defunkt.github.com/pystache) is a Python
implementation of [Mustache](http://mustache.github.com/). Mustache is a
framework-agnostic, logic-free templating system inspired by
[ctemplate](https://code.google.com/p/google-ctemplate/) and
et by Ivan Formichev. Like ctemplate, Mustache "emphasizes separating
logic from presentation: it is impossible to embed application logic in
this template language."

The [mustache(5)](https://mustache.github.com/mustache.5.html) man page
provides a good introduction to Mustache's syntax. For a more complete
(and more current) description of Mustache's behavior, see the official
[Mustache spec](https://github.com/mustache/spec).

Fork
----

This is a fork of the abandoned [pystache](https://github.com/defunkt/pystache)
project.

The reason for forking is that setuptools as of v58.0.2 no longer allows
the 'use_2to3' option that was being used to build this project for both
Python2 and Python3. Instead I have run the automatic conversion program
`2to3` over the whole codebase and committed. Python2 is not supported
by this fork.

The conversion was done with:

    2to3 --write --nobackups --no-diffs --doctests_only pystache
    2to3 --write --nobackups --no-diffs pystache


This fork is in active use by the Penny Dreadful MTG project but you may
get better results from the [chevron](https://github.com/noahmorrison/chevron)
project.

<!--
![](https://secure.travis-ci.org/defunkt/pystache.png "Travis CI current build status")

Pystache is [semantically versioned](https://semver.org) and can be found
on [PyPI](https://pypi.python.org/pypi/pystache). This version of
Pystache passes all tests in [version
1.1.2](https://github.com/mustache/spec/tree/v1.1.2) of the spec.
-->

Requirements
------------

Pystache is tested with--

-   Python 3.1
-   Python 3.2
-   Python 3.3

Install It
----------

    pip install pystache

And test it--

    pystache-test

To install and test from source (e.g. from GitHub), see the Develop
section.

Use It
------

    >>> import pystache
    >>> print(pystache.render('Hi {{person}}!', {'person': 'Mom'}))
    Hi Mom!

You can also create dedicated view classes to hold your view logic.

Here's your view class (in .../examples/readme.py):

    class SayHello(object):
        def to(self):
            return "Pizza"

Instantiating like so:

    >>> from pystache.tests.examples.readme import SayHello
    >>> hello = SayHello()

Then your template, say\_hello.mustache (by default in the same
directory as your class definition):

    Hello, {{to}}!

Pull it together:

    >>> renderer = pystache.Renderer()
    >>> print renderer.render(hello)
    Hello, Pizza!

For greater control over rendering (e.g. to specify a custom template
directory), use the `Renderer` class like above. One can pass attributes
to the Renderer class constructor or set them on a Renderer instance. To
customize template loading on a per-view basis, subclass `TemplateSpec`.
See the docstrings of the
[Renderer](https://github.com/PennyDreadfulMTG/pystache/blob/master/pystache/renderer.py)
class and
[TemplateSpec](https://github.com/PennyDreadfulMTG/pystache/blob/master/pystache/template_spec.py)
class for more information.

You can also pre-parse a template:

    >>> parsed = pystache.parse(u"Hey {{#who}}{{.}}!{{/who}}")
    >>> print parsed
    [u'Hey ', _SectionNode(key=u'who', index_begin=12, index_end=18, parsed=[_EscapeNode(key=u'.'), u'!'])]

And then:

    >>> print(renderer.render(parsed, {'who': 'Pops'}))
    Hey Pops!
    >>> print(renderer.render(parsed, {'who': 'you'}))
    Hey you!

Unicode
-------

This section describes how Pystache handles unicode, strings, and
encodings.

Internally, Pystache uses [only unicode strings](https://docs.python.org/howto/unicode.html#tips-for-writing-unicode-aware-programs).
For input, Pystache accepts both unicode strings and byte strings.
For output, Pystache's template rendering methods return only unicode.

Pystache's `Renderer` class supports a number of attributes to control
how Pystache converts byte strings to unicode on input. These include
the `file_encoding`, `string_encoding`, and `decode_errors` attributes.

The `file_encoding` attribute is the encoding the renderer uses to
convert to unicode any files read from the file system. Similarly,
`string_encoding` is the encoding the renderer uses to convert any other
byte strings encountered during the rendering process into unicode (e.g.
context values that are encoded byte strings).

The `decode_errors` attribute is what the renderer passes as the
`errors` argument to `str()`. The valid values for
this argument are `strict`, `ignore`, and `replace`.

Each of these attributes can be set via the `Renderer` class's
constructor using a keyword argument of the same name. See the Renderer
class's docstrings for further details. In addition, the `file_encoding`
attribute can be controlled on a per-view basis by subclassing the
`TemplateSpec` class. When not specified explicitly, these attributes
default to values set in Pystache's `defaults` module.

Develop
-------

To test from a source distribution (without installing)--

    python test_pystache.py

To test Pystache with multiple versions of Python (with a single
command!), you can use [tox](https://pypi.python.org/pypi/tox):

    pip install virtualenv
    pip install tox
    tox

If you do not have all Python versions listed in `tox.ini`--

    tox -e py31,py32  # for example

The source distribution tests also include doctests and tests from the
Mustache spec. To include tests from the Mustache spec in your test
runs:

    git submodule init
    git submodule update

The test harness parses the spec's (more human-readable) yaml files if
[PyYAML](https://pypi.python.org/pypi/PyYAML) is present. Otherwise, it
parses the json files. To install PyYAML--

    pip install pyyaml

To run a subset of the tests, you can use
[nose](https://somethingaboutorange.com/mrl/projects/nose/0.11.1/testing.html):

    pip install nose
    nosetests --tests pystache/tests/test_context.py:GetValueTests.test_dictionary__key_present


Mailing List
------------

There is a [mailing list](https://librelist.com/browser/pystache/). Note
that there is a bit of a delay between posting a message and seeing it
appear in the mailing list archive.

Credits
-------

    Original Author: Chris Wanstrath
    Previous Maintainer: Chris Jerdonek
    Forker: Thomas David Baker

Pystache logo by [David Phillips](https://davidphillips.us/) is licensed
under a [Creative Commons Attribution-ShareAlike 3.0 Unported
License](https://creativecommons.org/licenses/by-sa/3.0/deed.en_US).
![](https://i.creativecommons.org/l/by-sa/3.0/88x31.png "Creative
Commons Attribution-ShareAlike 3.0 Unported License")
