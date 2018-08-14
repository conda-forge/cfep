
<table>
<tr><td> Title </td><td> Packages which are "Too Big to Fail"</td>
<tr><td> Status </td><td> Proposed </td></tr>
<tr><td> Author(s) </td><td> Christopher J. "CJ" Wright &lt;cjwright4242@gmail.com&gt;</td></tr>
<tr><td> Created </td><td> Aug 13, 2018</td></tr>
<tr><td> Updated </td><td> Aug 13, 2018</td></tr>
<tr><td> Discussion </td><td> link to the PR where the CFEP is being discussed, NA is circulated initially </td></tr>
<tr><td> Implementation </td><td> link to the PR for the implementation, NA if not availble </td></tr>
</table>

## Abstract

There are core packages which are "too big to fail" (TBTF). 
These are packages, if they are built incorrectly will cause systemic problems across the ecosystem. 
For example the recent move of some recipes to `noarch` could have caused problems with `conda` itself. 

We need to have tighter control of these recipes.

We need to (at least):
1. Have all (or at least some) of core as maintainers of these packages
1. Require review approval to merge


## Motivation
The move to noarch for many python projects almost crippled our conda package.
Previous versions of `conda` would not work well with `noarch` runtime 
dependencies. 
This could have been particularly damaging, as a corrupted conda
would not be able to install the needed updates to get the user out of the 
corrupted state.

In this case this was a near miss, but one could imagine changes to a 
different part of the ecosystem doing something similar.

## Implementation
### Controls for "Too Big to Fail" feedstocks
Feedstocks which are TBTF would have the following additional controls on their
updates implemented:
1.  Have all (or at least some) of the core team as maintainers of these 
packages
1. Require review approval of at least one person to merge

### Criteria for "Too Big to Fail"
Any package meeting at least one of the following criteria would be considered 
TBTF:
1. Runtime dependency of `conda`
    ```python
    import networkx as nx
    g = nx.read_gpickle('graph.pkl')
    s = set()
    conda_runtime = [x.split(' ')[0] for x in g.node['conda']['meta_yaml']['requirements']['run']]
    for n in conda_runtime:
        # Note that this over counts since it also includes build/host deps
        s.update(nx.ancestors(g, n))
    ```
2. Has more than 5% of the ecosystem as indirect or direct decedents 
(as of writing this is 165 recipes)
    ```python
    import networkx as nx
    g = nx.read_gpickle('graph.pkl')
    s = set()
    percent = .05
    for n in g.nodes:
        if len(nx.descendants(g, n)) > int(len(g) * percent):
            s.add(n)
    ``` 
3. Has been agreed upon by core that the errors in the feedstock would 
pose a systemic risk.

#### Packages which are "Too Big to Fail"
##### 1 (conda runtime dependencies)
```python
{'asn1crypto',
 'atomicwrites',
 'attrs',
 'autoconf',
 'automake',
 'bison',
 'bzip2',
 'c_compiler_stub',
 'ca-certificates',
 'cdt_stub',
 'certifi',
 'cffi',
 'chardet',
 'cmake',
 'cmake-binary',
 'colorama',
 'compatible_pin_stub',
 'cryptography',
 'cryptography-vectors',
 'curl',
 'cxx_compiler_stub',
 'cython',
 'expat',
 'flex',
 'funcsigs',
 'idna',
 'ipaddress',
 'krb5',
 'libedit',
 'libffi',
 'libiconv',
 'libssh2',
 'libtool',
 'libuv',
 'm4',
 'make',
 'more-itertools',
 'msinttypes',
 'ncurses',
 'openssl',
 'perl',
 'pip',
 'pkg-config',
 'pluggy',
 'py',
 'pycparser',
 'pyopenssl',
 'pysocks',
 'pytest',
 'pytest-runner',
 'python',
 'readline',
 'rhash',
 'setuptools',
 'setuptools_scm',
 'six',
 'sqlite',
 'texinfo',
 'tk',
 'toolchain',
 'urllib3',
 'wheel',
 'win_inet_pton',
 'wincertstore',
 'xz',
 'yaml',
 'zlib'}
 ```
 ##### 2 (5% of the ecosystem)
 ```python
{'asn1crypto',
 'atomicwrites',
 'attrs',
 'autoconf',
 'automake',
 'backports',
 'backports.functools_lru_cache',
 'backports_abc',
 'bison',
 'blas',
 'bzip2',
 'c_compiler_stub',
 'ca-certificates',
 'cairo',
 'cdt_stub',
 'certifi',
 'cffi',
 'chardet',
 'click',
 'cmake',
 'cmake-binary',
 'colorama',
 'compatible_pin_stub',
 'cryptography',
 'cryptography-vectors',
 'curl',
 'cxx_compiler_stub',
 'cycler',
 'cython',
 'dbus',
 'decorator',
 'enum34',
 'expat',
 'flex',
 'fontconfig',
 'fortran_compiler_stub',
 'freetype',
 'funcsigs',
 'functools32',
 'gcc',
 'gettext',
 'glib',
 'gmp',
 'gobject-introspection',
 'gperf',
 'graphite2',
 'gsl',
 'gst-plugins-base',
 'gstreamer',
 'harfbuzz',
 'hdf5',
 'icu',
 'idna',
 'ipaddress',
 'jinja2',
 'jom',
 'jpeg',
 'kiwisolver',
 'krb5',
 'libedit',
 'libffi',
 'libflac',
 'libgcc',
 'libgfortran',
 'libgfortran-ng',
 'libiconv',
 'libjpeg-turbo',
 'libogg',
 'libpng',
 'libsndfile',
 'libssh2',
 'libtiff',
 'libtool',
 'libuuid',
 'libuv',
 'libvorbis',
 'libxcb',
 'libxml2',
 'm4',
 'make',
 'markupsafe',
 'matplotlib',
 'more-itertools',
 'mpfr',
 'mpir',
 'msinttypes',
 'ncurses',
 'nose',
 'numpy',
 'openblas',
 'openssl',
 'ordereddict',
 'pandas',
 'pango',
 'pcre',
 'perl',
 'pip',
 'pixman',
 'pkg-config',
 'pkgconfig',
 'pluggy',
 'pthread-stubs',
 'py',
 'pycparser',
 'pygments',
 'pyopenssl',
 'pyparsing',
 'pyqt',
 'pysocks',
 'pytest',
 'pytest-runner',
 'python',
 'python-dateutil',
 'pytz',
 'pyyaml',
 'qt',
 'r-base',
 'r-lattice',
 'r-magrittr',
 'r-mass',
 'r-matrix',
 'r-r6',
 'r-rcpp',
 'readline',
 'requests',
 'rhash',
 'scipy',
 'sed',
 'setuptools',
 'setuptools_scm',
 'singledispatch',
 'sip',
 'six',
 'sqlite',
 'ssl_match_hostname',
 'subprocess32',
 'texinfo',
 'texlive-core',
 'tk',
 'toolchain',
 'tornado',
 'urllib3',
 'wheel',
 'win_inet_pton',
 'wincertstore',
 'xcb-proto',
 'xorg-inputproto',
 'xorg-kbproto',
 'xorg-libice',
 'xorg-libsm',
 'xorg-libx11',
 'xorg-libxau',
 'xorg-libxdmcp',
 'xorg-libxext',
 'xorg-libxrender',
 'xorg-libxt',
 'xorg-renderproto',
 'xorg-util-macros',
 'xorg-xextproto',
 'xorg-xproto',
 'xorg-xtrans',
 'xz',
 'yaml',
 'yasm',
 'zlib'}
```
##### 3 (Systemic Risk)
TBD

### Automation
Some of this workflow could be automated via either webservices or another bot.
The main two parts would be:
1. adding core to the maintainers
1. setting github to require reviews for PRs

## Copyright

All CFEPs are explicitly [CC0 1.0 Universal](https://creativecommons.org/publicdomain/zero/1.0/).
