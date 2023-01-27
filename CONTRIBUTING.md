# Contributing

If you would like to contribute please read OpenTelemetry Collector [contributing
guidelines](https://github.com/open-telemetry/opentelemetry-collector/blob/main/CONTRIBUTING.md) before you begin your
work.

## Pull-request title

The title for your pull-request should contain the component type and name in brackets, plus a short statement for your
change. For instance:

    [processor/tailsampling] fix AND policy

## Changelog

Pull requests that contain user-facing changes will require a changelog entry. Keep in mind the following types of users:
1. Those who are consuming the telemetry exported from the collector
2. Those who are deploying or otherwise managing the collector or its configuration
3. Those who are depending on APIs exported from collector packages
4. Those who are contributing to the repository

The changelog is primarily directed at the first three groups but it is sometimes appropriate to include important updates relevant only to the forth group.

If a changelog entry is not required, a maintainer or approver will add the `Skip Changelog` label to the pull request.

**Examples**

Changelog entry required:
- Changes to the configuration of the collector or any component
- Changes to the telemetry emitted from and/or processed by the collector
- Changes to the prerequisites or assumptions for running a collector
- Changes to an API exported by a collector package
- Meaningful changes to the performance of the collector

Judgement call:
- Major changes to documentation
- Major changes to tests or test frameworks
- Changes to developer tooling in the repo

No changelog entry:
- Typical documentation updates
- Refactorings with no meaningful change in functionality
- Most changes to tests
- Chores, such as enabling linters, or minor changes to the CI process

### Adding a Changelog Entry

The [CHANGELOG.md](./CHANGELOG.md) file in this repo is autogenerated from `.yaml` files in the `./.chloggen` directory.

Your pull-request should add a new `.yaml` file to this directory. The name of your file must be unique since the last release.

During the collector release process, all `./.chloggen/*.yaml` files are transcribed into `CHANGELOG.md` and then deleted.

**Recommended Steps**
1. Create an entry file using `make chlog-new`. This generates a file based on your current branch (e.g. `./.chloggen/my-branch.yaml`)
2. Fill in all fields in the new file
3. Run `make chlog-validate` to ensure the new file is valid
4. Commit and push the file

Alternately, copy `./.chloggen/TEMPLATE.yaml`, or just create your file from scratch.

## Adding New Components

**Before** any code is written, [open an
issue](https://github.com/open-telemetry/opentelemetry-collector-contrib/issues/new?assignees=&labels=new+component&template=new_component.md&title=New%20component)
providing the following information:

* Who's the sponsor for your component. A sponsor is an approver who will be in charge of being the official reviewer of
  the code and become a code owner for the component. For vendor-specific components, it's good to have a volunteer
  sponsor. If you can't find one, we'll assign one in a round-robin fashion. A vendor-specific component directly interfaces
  with a vendor-specific API and is expected to be maintained by a representative of the same vendor. For non-vendor specific
  components, having a sponsor means that your use case has been validated.
* Some information about your component, such as the reasoning behind it, use-cases, telemetry data types supported, and
  anything else you think is relevant for us to make a decision about accepting the component.
* The configuration options your component will accept. This will help us understand what it does and have an idea of
  how the implementation might look like.

Components comprise of exporters, extensions, receivers, and processors. The key criteria to implementing a component is to:

* Implement the [component.Component](https://pkg.go.dev/go.opentelemetry.io/collector/component#Component) interface
* Provide a configuration structure which defines the configuration of the component
* Provide the implementation which performs the component operation

Familiarize yourself with the interface of the component that you want to write, and use existing implementations as reference.
[Building a Trace Receiver](https://opentelemetry.io/docs/collector/trace-receiver/) tutorial provides a detailed example of building a component.

*NOTICE:* The Collector is in Beta stage and as such the interfaces may undergo breaking changes. Component creators
must be available to update or review their components when such changes happen, otherwise the component will be
excluded from the default builds.

Generally, maintenance of components is the responsibility of contributors who authored them. If the original author or
some other contributor does not maintain the component it may be excluded from the default build. The component **will**
be excluded if it causes build problems, has failing tests or otherwise causes problems to the rest of the repository
and the rest of contributors.

- Create your component under the proper folder and use Go standard package naming recommendations.
- Use a boiler-plate Makefile that just references the one at top level, ie.: `include ../../Makefile.Common` - this
  allows you to build your component with required build configurations for the contrib repo while avoiding building the
  full repo during development.
- Each component has its own go.mod file. This allows custom builds of the collector to take a limited sets of
  dependencies - so run `go mod` commands as appropriate for your component.
- Implement the needed interface on your component by importing the appropriate component from the core repo. Follow the
  pattern of existing components regarding config and factory source files and tests.
- Implement your component as appropriate. Provide end-to-end tests (or mock backend/client as appropriate). Target is
  to get 80% or more of code coverage.
- Add a README.md on the root of your component describing its configuration and usage, likely referencing some of the
  yaml files used in the component tests. We also suggest that the yaml files used in tests have comments for all
  available configuration settings so users can copy and modify them as needed.
- Add a `replace` directive at the root `go.mod` file so your component is included in the build of the contrib
  executable.
- Add your component to `versions.yaml`.
- All components must be included in [`internal/components/`](./internal/components) and in the respective testing
  harnesses. To align with the test goal of the project, components must be testable within the framework defined within
  the folder. If a component can not be properly tested within the existing framework, it must increase the non testable
  components number with a comment within the PR explaining as to why it can not be tested.
- Add the sponsor for your component and yourself to a new line for your component in the
  [`.github/CODEOWNERS`](./.github/CODEOWNERS) file.
- Run `make generate-gh-issue-templates` to add your component to the dropdown list in the issue templates.

### Releasing New Components
After a component has been approved and merged, and has been enabled in `internal/components/`, it must be added to the
[OpenTelemetry Collector Contrib's release manifest.yaml](https://github.com/open-telemetry/opentelemetry-collector-releases/blob/main/distributions/otelcol-contrib/manifest.yaml)
to be included in the distributed otelcol-contrib binaries and docker images.

### Rotating sponsors

The following GitHub users are the currently available sponsors, either by being an approver or a maintainer of the contrib repository. The list is ordered based on a random sort of the list of sponsors done live at the Collector SIG meeting on 27-Apr-2022 and serves as the seed for the round-robin selection of sponsors, as described in the section above.

* [@dashpole](https://github.com/dashpole)
* [@TylerHelmuth](https://github.com/TylerHelmuth)
* [@djaglowski](https://github.com/djaglowski)
* [@codeboten](https://github.com/codeboten)
* [@Aneurysm9](https://github.com/Aneurysm9)
* [@mx-psi](https://github.com/mx-psi)
* [@dmitryax](https://github.com/dmitryax)
* [@evan-bradley](https://github.com/evan-bradley)
* [@MovieStoreGuy](https://github.com/MovieStoreGuy)
* [@bogdandrutu](https://github.com/bogdandrutu)
* [@jpkrohling](https://github.com/jpkrohling)

Whenever a sponsor is picked from the top of this list, please move them to the bottom.

## Adding metrics to existing receivers
Following these steps for contributing additional metrics to existing receivers.
 - Read instructions [here](https://github.com/open-telemetry/opentelemetry-collector/blob/main/CONTRIBUTING.md#fork) on how to
   fork, build and create PRs. The only difference is to change repository name from `opentelemetry-collector` to `opentelemetry-collector-contrib`
 - Edit `metadata.yaml` of your metrics receiver to add new metrics, e.g.: `redisreceiver/metadata.yaml`
 - To generate new metrics on top of this updated YAML file.
   - Run `cd receiver/redisreceiver`
   - Run `go generate ./...`
- Review the changed files and merge the changes into your forked repo.
- Create PR from Github web console following the instructions above.

## General Recommendations
Below are some recommendations that apply to typical components. These are not rigid rules and there are exceptions but
in general try to follow them.

- Avoid introducing batching, retries or worker pools directly on receivers and exporters. Typically, these are general
  cases that can be better handled via processors (that also can be reused by other receivers and exporters).
- When implementing exporters try to leverage the exporter helpers from the core repo, see [exporterhelper
  package](https://github.com/open-telemetry/opentelemetry-collector/tree/main/exporter/exporterhelper). This will
  ensure that the exporter provides [zPages](https://opencensus.io/zpages/) and a standard set of metrics.
- `replace` statements in `go.mod` files can be automatically inserted by running `make crosslink`. For more information
  on the `crosslink` tool see the README [here](https://github.com/open-telemetry/opentelemetry-go-build-tools/tree/main/crosslink).

## Issue Triaging

To help provide a consistent process for seeing issues through to completion, this section details some guidelines and
definitions to keep in mind when triaging issues.

### Roles

Determining the root cause of issues is a shared responsibility between those with triager permissions, code owners,
OpenTelemetry community members, issue authors, and anyone else who would like to contribute.

#### Triagers

Contributors with [triager](https://github.com/open-telemetry/opentelemetry-collector-contrib/#contributing) permissions can help move
issues along by adding missing component labels, which help organize issues and trigger automations to notify code owners. They can
also use their familiarity with the Collector and its components to investigate issues themselves. Alternatively, they may point issue
authors to another resource or someone else who may know more.

#### Code Owners

In many cases, the code owners for an issue are the best resource to help determine the root cause of a bug or whether an enhancement
is fit to be added to a component. Code owners will be notified by repository automations when:

- a component label is added to an issue
- an issue is opened
- the issue becomes stale

Code owners may not have triager permissions on the repository,
so they can help triage through investigation and by participating in discussions. They can also help organize issues by
[adding labels via comments](#adding-labels-via-comments).

#### Community Members

Community members or interested parties are welcome to help triage issues by investigating the root cause of bugs, adding input for
features they would like to see, or participating in design discussions.

### Triage process

Triaging an issue requires getting the issue into a state where there is enough information available on the issue or understanding
between the involved parties to allow work to begin or for the issue to be closed. Facilitating this may involve, but is not limited to:

- Determining whether the issue is related to the code or documentation, or whether the issue can be resolved without any changes.
- Ensuring that a bug can be reproduced, and if possible, the behavior can be traced back to the offending code or documentation.
- Determining whether a feature request belongs in a component, should be accomplished through other means, or isn't appropriate for a component at this time.
- Guiding any interested parties to another person or resource that may be more knowledgeable about an issue.
- Suggesting an issue for discussion at a SIG meeting if a synchronous discussion would be more productive.

#### Issue assignment

Issues are assigned for someone to work on by a triager when someone volunteers to work on an issue. Assignment is intended to prevent duplicate work by making it visible who is
working on a particular task. A person who is assigned to the issue may be assigned to help triage the issue and implement it, or can be assigned after the issue has already been
triaged and is ready for work. If someone who is assigned to an issue is no longer able to work on it, they may request to be unassigned from the issue.

### Label Definitions

| Label                | When to apply                                                                                                                                                                                           |
| -------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `bug`                | Something that is advertised or intended to work isn't working as expected.                                                                                                                             |
| `enhancement`        | Something that isn't an advertised feature that would be useful to users or maintainers.                                                                                                                |
| `flaky test`         | A test unexpectedly failed during CI, showing that there is a problem with the tests or test setup that is causing the tests to intermittently fail.                                                    |
| `good first issue`   | Implementing this issue would not require specialized or in-depth knowledge about the component and is ideal for a new or first-time contributor to take.                                               |
| `help wanted`        | The code owners for this component do not expect to have time to work on it soon, and would welcome help from contributors.                                                                             |
| `needs discussion`   | This issue needs more input from the maintainers or community before work can be started.                                                                                                               |
| `needs triage`       | This label is added automatically, and can be removed when a triager or code owner deems that an issue is either ready for work or should not need any work.                                            |
| `waiting for author` | Can be applied when input is required from the author before the issue can move any further.                                                                                                            |
| `priority:p0`        | A critical security vulnerability or Collector panic using a default or common configuration unrelated to a specific component.                                                                         |
| `priority:p1`        | An urgent issue that should be worked on quickly, before most other issues.                                                                                                                             |
| `priority:p2`        | A standard bug or enhancement.                                                                                                                                                                          |
| `priority:p3`        | A technical improvement, lower priority bug, or other minor issue. Generally something that is considered a "nice to have."                                                                               |
| `release:blocker`    | This issue must be resolved before the next Collector version can be released.                                                                                                                          |
| `Sponsor Needed`     | A new component has been proposed, but implementation is not ready to begin. This can be because a sponsor has not yet been decided, or because some details on the component still need to be decided. |
| `Accepted Component` | A sponsor has elected to take on a component and implementation is ready to begin.                                                                                                                      |
| `Vendor Specific Component` | This should be applied to any component proposal where the functionality for the component is particular to a vendor. |

### Adding Labels via Comments

In order to facilitate proper label usage and to empower Code Owners, you are able to add labels to issues via comments. To add a label through a comment, post a new comment on an issue starting with `/label`, followed by a space-separated list of your desired labels. Supported labels come from the table below, or correspond to a component defined in the [CODEOWNERS file](.github/CODEOWNERS).

The following general labels are supported:

| Label                | Label in Comment     |
|----------------------|----------------------|
| `good first issue`   | `good-first-issue`   |
| `help wanted`        | `help-wanted`        |
| `needs discussion`   | `needs-discussion`   |
| `needs triage`       | `needs-triage`       |
| `waiting for author` | `waiting-for-author` |

To delete a label, prepend the label with `-`. Note that you must make a new comment to modify labels; you cannot edit an existing comment.

Example label comment:

```
/label receiver/prometheus help-wanted -exporter/prometheus
```

## Becoming a Code Owner

A Code Owner is responsible for a component within Collector Contrib, as indicated by the [CODEOWNERS file](https://github.com/open-telemetry/opentelemetry-collector-contrib/blob/main/.github/CODEOWNERS). That responsibility includes maintaining the component, responding to issues, and reviewing pull requests.

Sometimes a component may be in need of a new or additional Code Owner. A few reasons this situation may arise would be:
- The component was never assigned a Code Owner.
- A previous Code Owner stepped down.
- An existing Code Owner has become unresponsive. See [unmaintained stability status](https://github.com/open-telemetry/opentelemetry-collector#unmaintained).
- The existing Code Owners are actively looking for new Code Owners to help.

If you would like to help and become a Code Owner you must meet the following requirements:

1. [Be a member of the OpenTelemetry organization.](https://github.com/open-telemetry/community/blob/main/community-membership.md#member)
2. (Code Owner Discretion) It is best to have resolved an issue related to the component, contributed directly to the component, and/or review component PRs. How much interaction with the component is required before becoming a Code Owner is up to any existing Code Owners.

Code Ownership is ultimately up to the judgement of the existing Code Owners and Collector Contrib Maintainers. Meeting the above requirements is not a guarantee to be granted Code Ownership.

To become a Code Owner, open a PR with the CODEOWNERS file modified, adding your GitHub username to the component's row. Be sure to tag the existing Code Owners, if any, within the PR to ensure they receive a notification.

### Makefile Guidelines

When adding or modifying the `Makefile`'s in this repository, consider the following design guidelines.

Make targets are organized according to whether they apply to the entire repository, or only to an individual module.
The [Makefile](./Makefile) SHOULD contain "repo-level" targets. (i.e. targets that apply to the entire repo.)
Likewise, `Makefile.Common` SHOULD contain "module-level" targets. (i.e. targets that apply to one module at a time.)
Each module should have a `Makefile` at its root that includes `Makefile.Common`.

#### Module-level targets

Module-level targets SHOULD NOT act on nested modules. For example, running `make lint` at the root of the repo will
*only* evaluate code that is part of the `go.opentelemetry.io/collector` module. This excludes nested modules such as
`go.opentelemetry.io/collector/component`.

Each module-level target SHOULD have a corresponding repo-level target. For example, `make golint` will run `make lint`
in each module. In this way, the entire repository is covered. The root `Makefile` contains some "for each module" targets
that can wrap a module-level target into a repo-level target.

#### Repo-level targets

Whenever reasonable, targets SHOULD be implemented as module-level targets (and wrapped with a repo-level target).
However, there are many valid justifications for implementing a standalone repo-level target.

1. The target naturally applies to the repo as a whole. (e.g. Building the collector.)
2. Interaction between modules would be problematic.
3. A necessary tool does not provide a mechanism for scoping its application. (e.g. `porto` cannot be limited to a specific module.)
4. The "for each module" pattern would result in incomplete coverage of the codebase. (e.g. A target that scans all file, not just `.go` files.)

#### Default targets

The default module-level target (i.e. running `make` in the context of an individual module), should run a substantial set of module-level
targets for an individual module. Ideally, this would include *all* module-level targets, but exceptions should be made if a particular
target would result in unacceptable latency in the local development loop.

The default repo-level target (i.e. running `make` at the root of the repo) should meaningfully validate the entire repo. This should include
running the default common target for each module as well as additional repo-level targets.