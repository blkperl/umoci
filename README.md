[![umoci](https://umo.ci/umoci-black.png)][umoci-site]

<!--
  This hack is necessary to work around the fact we cannot conditionally hide
  these images in Hugo (see <https://github.com/gohugoio/hugo/issues/7398>).
-->
<div style="display: none">

[![Release](https://img.shields.io/github/release/openSUSE/umoci.svg)](https://github.com/openSUSE/umoci/releases/latest)
[![Build Status](https://img.shields.io/travis/openSUSE/umoci/master.svg)](https://travis-ci.org/openSUSE/umoci)
![License: Apache 2.0](https://img.shields.io/github/license/openSUSE/umoci.svg)

[![Go Report Card](https://goreportcard.com/badge/github.com/openSUSE/umoci)](https://goreportcard.com/report/github.com/openSUSE/umoci)
[![CII Best Practices](https://bestpractices.coreinfrastructure.org/projects/1084/badge)](https://bestpractices.coreinfrastructure.org/projects/1084)
[![DOI](https://zenodo.org/badge/72283469.svg)](https://zenodo.org/badge/latestdoi/72283469)

</div>

**u**moci **m**odifies **O**pen **C**ontainer **i**mages.

umoci (pronounced [/u&#720;mo&#712;&#680;i/][umoci-ipa] or approximately
"oo-mo-tchee") is a reference implementation of the [OCI image
specification][oci-image-spec] and provides users with the ability to create,
manipulate, and otherwise interact with container images. It is designed to be
as small and unopinonated as possible, so as to act as a foundation for larger
systems to be built on top of. The primary method of using umoci is as a
command-line tool:

```ShellSession
  Extract image "leap" from image directory "opensuse" and place it
  inside an OCI runtime-spec bundle at the path "bundle".
% umoci unpack --image opensuse:leap bundle

  Make some changes to the root filesystem ("bundle/rootfs").
% runc run -b bundle ctr
ctr-sh$ zypper install -y foobarbaz
ctr-sh$ exit
% echo foo > bundle/rootfs/README

  Create a new image (called "new-leap") in the image directory "opensuse",
  based on "leap" which contains the changes made to "bundle/rootfs".
% umoci repack --image opensuse:new-leap bundle

  Modify the configuration of the "new-leap" image to specify a new author.
% umoci config --image opensuse:new-leap \
>              --author="Aleksa Sarai <cyphar@cyphar.com>" \
>              --config.workingdir="/var/www"

  Garbage-collect any unreferenced blobs in the image directory "opensuse".
% umoci gc --layout opensuse
```

See [the project website][umoci-site] for more accessible documentation about
how to use umoci. Notable users of umoci include:

 * [KIWI][kiwi], which uses umoci to support building both [base and derived
   container images][kiwi-container] which are then converted to Docker images.
 * The [Open Build Service][obs], which uses umoci (through [KIWI][kiwi]) to
   support building and publishing container images from its built-in container
   registry. The openSUSE project has been using this method of building
   container images in production since 2016.
 * [Stacker][stacker], which uses umoci as its core building primitive, and is
   used by Cisco to build container images for some of their appliances since
   2018.
 * [LXC][lxc] provides support for OCI container images through an [OCI
   template][lxc-oci], which is implemented as a shell script that wraps umoci.
   The fact that a container runtime with a vastly different model to OCI
   container runtimes can make use of umoci is further evidence of its
   unopinionated design.

If you wish to provide feedback or contribute, read the [`CONTRIBUTING.md`][contributing]
for this project to refresh your knowledge about how to submit good bug reports
and patches. Information about how to privately submit security disclosures is
also provided.

[umoci-site]: https://umo.ci/
[umoci-ipa]: http://ipa-reader.xyz/?text=u%CB%90mo%CB%88%CA%A8i&voice=Amy
[oci-image-spec]: https://github.com/opencontainers/image-spec
[kiwi]: https://osinside.github.io/kiwi/
[kiwi-container]: https://osinside.github.io/kiwi/building/build_docker_container.html
[obs]: https://openbuildservice.org/
[stacker]: https://github.com/anuvu/stacker
[lxc]: https://linuxcontainers.org/
[lxc-oci]: https://github.com/lxc/lxc/blob/lxc-4.0.2/templates/lxc-oci.in
[contributing]: /CONTRIBUTING.md

### Install ###

Pre-built binaries can be downloaded from [umoci's releases page][releases]. As
umoci's builds are reproducible, a cryptographic checksum file is included in
the release assets. All of the assets are also signed with a [release
key][umoci-keyring], whose fingerprint is:

```text
pub   rsa4096 2016-06-21 [SC] [expires: 2031-06-18]
      5F36C6C61B5460124A75F5A69E18AA267DDB8DB4
uid           [ultimate] Aleksa Sarai <asarai@suse.com>
uid           [ultimate] Aleksa Sarai <asarai@suse.de>
sub   rsa4096 2016-06-21 [E] [expires: 2031-06-18]
```

umoci is also available from several distributions' repositories:

* [openSUSE](https://software.opensuse.org/package/umoci)
* [Gentoo](https://packages.gentoo.org/packages/app-emulation/umoci)
* [Arch Linux (AUR)](https://aur.archlinux.org/packages/umoci/)

To build umoci from the [source code][source], a simple `make` should work on
most machines, as should `make install`.

[releases]: https://github.com/openSUSE/umoci/releases
[umoci-keyring]: /umoci.keyring
[source]: https://github.com/openSUSE/umoci
[go]: https://golang.org/

### Usage ###

umoci has a subcommand-based command-line. For more detailed information, see
the generated man pages (which you can build with `make docs`). You can also
read through our [quick start guide][quickstart].

<!-- TODO: Put the man pages on the website... -->

```text
% umoci --help
NAME:
   umoci - umoci modifies Open Container images

USAGE:
   umoci [global options] command [command options] [arguments...]

VERSION:
   0.4.5

AUTHOR:
   Aleksa Sarai <asarai@suse.com>

COMMANDS:
   raw      advanced internal image tooling
   help, h  Shows a list of commands or help for one command

   image:
     config      modifies the image configuration of an OCI image
     unpack      unpacks a reference into an OCI runtime bundle
     repack      repacks an OCI runtime bundle into a reference
     new         creates a blank tagged OCI image
     tag         creates a new tag in an OCI image
     remove, rm  removes a tag from an OCI image
     stat        displays status information of an image manifest
     insert      insert content into an OCI image

   layout:
     gc        garbage-collects an OCI image's blobs
     init      create a new OCI layout
     list, ls  lists the set of tags in an OCI layout

GLOBAL OPTIONS:
   --verbose      alias for --log=info
   --log value    set the log level (debug, info, [warn], error, fatal) (default: "warn")
   --help, -h     show help
   --version, -v  print the version
```

[quickstart]: https://umo.ci/quick-start/

### Releases and Stability ###

We regularly publish [new releases][releases], with each release being given a
unique identifying version number (as governed by [Semantic Versioning
(SemVer)][semver]). Information about previous releases including the list of
new features, bug fixes and resolved security issues is available in the
[change log][changelog].

Note that while umoci is currently usable as a Go library (and we do have
several users of the Go APIs), the API is explicitly considered **unstable**
until umoci `1.0` is released. However, the umoci CLI API is considered to be
stable despite umoci not being a `1.0` project.

[releases]: https://github.com/openSUSE/umoci/releases
[semver]: http://semver.org/
[changelog]: /CHANGELOG.md

### Governance ###

umoci is an Open Container Initative project, and is thus bound by the [OCI
Code of Conduct][oci-coc] and the [OCI Charter][oci-charter]. In addition, the
umoci project has its own specific [governance rules][governance] which
determine how changes are accepted into the project, how maintainers are added
or removed, how releases are proposed and released, and how the governance
rules are changed. In the case of any conflict which cannot be resolved by this
project's governance rules, the [OCI Technical Oversight Board][oci-tob] may
step in to help resolve the issue.

[oci-coc]: https://github.com/opencontainers/.github/blob/master/CODE_OF_CONDUCT.md
[oci-charter]: https://github.com/opencontainers/tob/blob/master/CHARTER.md
<!-- TODO: Add proper governance documents. -->
[governance]: /GOVERNANCE.md
[oci-tob]: https://opencontainers.org/about/tob/

### History ###

umoci was originally developed in 2016 by Aleksa Sarai as part of the [openSUSE
project][opensuse], and was donated to the Open Container Initiative as a
reference implementation of the [OCI image specification][oci-image-spec] in
mid-2020.

[opensuse]: https://www.opensuse.org/
[oci-image-spec]: https://github.com/opencontainers/image-spec

### License ###

umoci is licensed under the terms of the Apache 2.0 license.

```text
umoci: Umoci Modifies Open Containers' Images
Copyright (C) 2016-2020 SUSE LLC
Copyright (C) 2018 Cisco Systems

Licensed under the Apache License, Version 2.0 (the "License");
you may not use this file except in compliance with the License.
You may obtain a copy of the License at

   http://www.apache.org/licenses/LICENSE-2.0

Unless required by applicable law or agreed to in writing, software
distributed under the License is distributed on an "AS IS" BASIS,
WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
See the License for the specific language governing permissions and
limitations under the License.
```

### Citation ###

If you have used umoci in your research, please cite it like you would any
other useful software. Here is a handy BibTex citation.

```
@misc{umoci,
	title = {umoci - Standalone Tool For Manipulating Container Images},
	author = {Aleksa Sarai et al.},
	year = {2016},
	url = {https://umo.ci/},
	doi = {http://dx.doi.org/10.5281/zenodo.1188474},
}
```

Thank you.
