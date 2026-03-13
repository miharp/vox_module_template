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
copier copy gh:your-org/vox_module_template /path/to/my-modules/puppetlabs-apache
```

Or from a local clone:

```bash
copier copy /path/to/vox_module_template /path/to/my-modules/puppetlabs-apache
```

### Update an existing module

```bash
cd /path/to/my-modules/puppetlabs-apache
copier update
```

Copier stores your answers in `.copier-answers.yml` so updates are seamless.

## Generated module structure

```text
<forge_username>-<module_name>/
├── manifests/
│   ├── init.pp          # Main class (public API)
│   ├── install.pp       # Package management
│   ├── config.pp        # Configuration files
│   └── service.pp       # Service management
├── data/
│   └── common.yaml      # Module-layer Hiera defaults
├── spec/
│   ├── spec_helper.rb
│   └── classes/
│       └── init_spec.rb
├── files/               # Static files served by puppet:// URIs
├── templates/           # EPP/ERB templates
├── hiera.yaml           # Module-layer Hiera config
├── metadata.json        # Forge metadata
├── Gemfile
├── Rakefile
├── .fixtures.yml
├── .gitignore
├── README.md
└── CHANGELOG.md
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
