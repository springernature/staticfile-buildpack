# Cloud Foundry Staticfile Buildpack

[![CF Slack](https://www.google.com/s2/favicons?domain=www.slack.com) Join us on Slack](https://cloudfoundry.slack.com/messages/buildpacks/)

A Cloud Foundry [buildpack](https://docs.cloudfoundry.org/buildpacks/) for static content such as websites (HTML/JS/CSS).

## staticfile-lua

**WARNING** This is deprecated - please use the standard [nginx-buildpack](https://github.com/cloudfoundry/nginx-buildpack) in preference, as this will no longer be maintained and may vanish at any point.

This fork uses [OpenResty](http://openresty.org/en/) 1.15.8.2, which provides Lua support.

Ideally this should be moved to work off the [nginx](https://github.com/cloudfoundry/nginx-buildpack) buildpack; however, that uses a different (not `erb`) templating system, and further appears to be [in the process of being replaced(https://github.com/cloudfoundry/nginx-buildpack/issues/18#issuecomment-541827184).

OpenResty for `cflinuxfs3` was built on an Ubuntu 18.04.2 VM:

```
sudo add-apt-repository universe # for "symlinks"
sudo apt zlib1g-dev install libreadline-dev libncurses5-dev libpcre3-dev libssl-dev perl make build-essential libgeoip-dev git symlinks
curl -sSL https://openresty.org/download/openresty-1.15.8.2.tar.gz | tar xzf -
cd openresty-1.15.8.2
./configure --with-luajit --with-pcre-jit --with-http_realip_module --with-ipv6 --with-http_stub_status_module --prefix=/home/vcap/app
sudo rm -rf /home/vcap/app
sudo make install
sudo symlinks -crv /home/vcap/app
tar czvf nginx-1.15.8.2-cflinuxfs2.tgz -C /home/vcap/app .
```

### Buildpack User Documentation

Official buildpack documentation can be found at [staticfile buildpack docs](https://docs.cloudfoundry.org/buildpacks/staticfile/index.html).

### Building the Buildpack

To build this buildpack, run the following commands from the buildpack's directory:

1. Source the .envrc file in the buildpack directory.

   ```bash
   source .envrc
   ```
   To simplify the process in the future, install [direnv](https://direnv.net/) which will automatically source .envrc when you change directories.

1. Install buildpack-packager

    ```bash
    go install github.com/cloudfoundry/libbuildpack/packager/buildpack-packager
    ```

1. Build the buildpack

    ```bash
    buildpack-packager build [ --cached=(true|false) ]
    ```

1. Use in Cloud Foundry

   Upload the buildpack to your Cloud Foundry and optionally specify it by name

    ```bash
    cf create-buildpack [BUILDPACK_NAME] [BUILDPACK_ZIP_FILE_PATH] 1
    cf push my_app [-b BUILDPACK_NAME]
    ```

### Testing

To test this buildpack, run the following commands from the buildpack's directory:

1. Source the .envrc file in the buildpack directory.

   ```bash
   source .envrc
   ```
   To simplify the process in the future, install [direnv](https://direnv.net/) which will automatically source .envrc when you change directories.

1. Run unit tests

    ```bash
    ./scripts/unit.sh
    ```

1. Run integration tests

   Buildpacks use the [Cutlass](https://github.com/cloudfoundry/libbuildpack/tree/master/cutlass) framework for running integration tests against Cloud Foundry. Before running the integration tests, you need to login to your Cloud Foundry using the [cf cli](https://github.com/cloudfoundry/cli):

    ```bash
    cf login -a https://api.your-cf.com -u name@example.com -p pa55woRD
    ```

   Note that your user requires permissions to run `cf create-buildpack` and `cf update-buildpack`. To run the integration tests, run the following command from the buildpack's directory:

    ```bash
    ./scripts/integration.sh
    ```

More information can be found on [here](https://github.com/cloudfoundry/libbuildpack/tree/master/cutlass).

### Contributing

Find our guidelines [here](./CONTRIBUTING.md).

### Help and Support

Join the #buildpacks channel in our [Slack community](https://slack.cloudfoundry.org/) if you need any further assistance.

### Reporting Issues

Open a GitHub issue on this project [here](https://github.com/cloudfoundry/staticfile/issues/new).

### Active Development

The project backlog is on [Pivotal Tracker](https://www.pivotaltracker.com/projects/1042066).

### Acknowledgements

This buildpack is based heavily upon Jordon Bedwell's Heroku buildpack and the modifications by David Laing for Cloud Foundry [nginx-buildpack (deprecated)](https://github.com/cloudfoundry-community/nginx-buildpack). It has been tuned for usability (configurable with `Staticfile`) and to be included as a default buildpack (detects `Staticfile` rather than the presence of an `index.html`). Thanks for the buildpack Jordon!
If you require additional custom NGINX configuration, use the new [nginx-buildpack](https://github.com/cloudfoundry/nginx-buildpack).

