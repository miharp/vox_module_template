# vox_module_template

A [Copier](https://copier.readthedocs.io/) template for creating Puppet modules
following the [Puppet Best Practices Guide](https://puppet.com/docs/puppet/latest/bgtm.html).

## Requirements

- Python 3.10+
- Copier ≥ 9.0.0 (`pipx install copier`)
- Git 2.27+

## Usage

### Create a new module

```bash
copier copy gh:miharp/vox_module_template ./vox-nginx
```

Copier will prompt for the following answers:

```
Module name (snake_case, e.g. 'apache', 'ntp'): nginx
Puppet Forge username or organization (e.g. 'puppetlabs'): vox
Author full name [vox]: Vox Pupuli
One-line summary of what this module manages: Manages the nginx web server
Full description of the module [Manages the nginx web server]: Installs, configures, and manages the nginx HTTP server and its service.
Initial module version (semantic versioning) [0.1.0]:
Module license [Apache-2.0]:
Source repository URL [https://github.com/vox/puppet-nginx]:
Minimum supported Puppet version [7.0.0]:
Maximum Puppet version (exclusive upper bound) [9.0.0]:
```

Or pass answers non-interactively with `--data`:

```bash
copier copy gh:miharp/vox_module_template ./vox-nginx \
  --data module_name=nginx \
  --data puppet_forge_username=vox \
  --data module_author="Vox Pupuli" \
  --data module_summary="Manages the nginx web server" \
  --data module_description="Installs, configures, and manages the nginx HTTP server and its service." \
  --defaults
```

### Run lint and unit tests

Using the [voxbox container](https://github.com/voxpupuli/container-voxbox).
Pull the image once:

```bash
docker pull ghcr.io/voxpupuli/voxbox:8
```

**Lint** — runs `puppet-lint` against all manifests:

```bash
docker run --rm -v $PWD:/repo:Z ghcr.io/voxpupuli/voxbox:8 -f /Rakefile lint
```

A clean module produces no output and exits 0.

**Unit tests** — downloads fixture modules, then runs rspec-puppet across
every OS in `metadata.json`:

```bash
docker run --rm -v $PWD:/repo:Z ghcr.io/voxpupuli/voxbox:8 -f /Rakefile spec
```

Example output for the generated `vox-nginx` module:

```
I, [...]  INFO -- : Creating symlink /repo/spec/fixtures/modules/nginx => /repo
Notice: Preparing to install into /repo/spec/fixtures/modules ...
Notice: Installing -- do not interrupt ...
/repo/spec/fixtures/modules
└── puppetlabs-stdlib (v9.7.0)

...............................................................................................................

Coverage Report:

Total resources:   5
Touched resources: 3
Resource coverage: 60.00%

Untouched resources:
  Package[nginx]
  Service[nginx]


Finished in 0.98 seconds (files took 0.95 seconds to load)
111 examples, 0 failures
```

The 111 examples cover every OS family in `metadata.json` (RedHat, CentOS,
AlmaLinux, Rocky, Debian, Ubuntu) × the full set of `init_spec.rb` contexts:
compile, class inclusion, ordering, `manage_package => false`, and
`manage_service => false`.

**Lint + spec in one shot:**

```bash
docker run --rm -v $PWD:/repo:Z ghcr.io/voxpupuli/voxbox:8 -f /Rakefile lint spec
```

### Update an existing module

```bash
cd ./vox-nginx
copier update
```

Copier stores your answers in `.copier-answers.yml` so updates are seamless.

## Generated module structure

```text
vox-nginx/
├── manifests/
│   ├── init.pp          # Main class (public API) with contain + ordering
│   ├── install.pp       # Package management
│   ├── config.pp        # Configuration files
│   └── service.pp       # Service management
├── data/
│   └── common.yaml      # Module-layer Hiera defaults
├── spec/
│   ├── spec_helper.rb   # Uses voxpupuli/test/spec_helper
│   └── classes/
│       └── init_spec.rb # rspec-puppet tests across all supported OS
├── files/               # Static files served by puppet:// URIs
├── templates/           # EPP/ERB templates
├── hiera.yaml           # Module-layer Hiera config (OS family overrides)
├── metadata.json        # Forge metadata with OS support matrix
├── Gemfile              # voxpupuli-test + voxpupuli-acceptance
├── Rakefile             # validate, lint, spec tasks
├── .fixtures.yml        # stdlib fixture; self-symlink auto-created
├── .gitignore
├── README.md
└── CHANGELOG.md
```

### Example: generated `manifests/init.pp`

```puppet
# @summary Manages the nginx web server
#
# @param manage_package   Whether this module should manage the package resource.
# @param package_ensure   The ensure value for the managed package.
# @param package_name     The name of the package to install.
# @param manage_config    Whether this module should manage configuration files.
# @param manage_service   Whether this module should manage the service resource.
# @param service_ensure   The ensure value for the managed service.
# @param service_enable   Whether the service should be enabled on boot.
# @param service_name     The name of the service to manage.
class nginx (
  Boolean $manage_package = true,
  String  $package_ensure = 'present',
  String  $package_name   = 'nginx',
  Boolean $manage_config  = true,
  Boolean $manage_service = true,
  String  $service_ensure = 'running',
  Boolean $service_enable = true,
  String  $service_name   = 'nginx',
) {
  contain nginx::install
  contain nginx::config
  contain nginx::service

  Class['nginx::install']
  -> Class['nginx::config']
  ~> Class['nginx::service']
}
```

## Template structure

```text
vox_module_template/
├── copier.yml           # Copier configuration and questions
├── README.md            # This file (not copied to generated modules)
└── template/            # Source files (_subdirectory: template)
    └── ...
```

## License

Apache-2.0
