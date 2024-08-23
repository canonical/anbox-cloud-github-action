# anbox-cloud-github-action

Github action to setup Anbox Cloud on a Github runner. It installs the
Anbox Cloud Appiance snap package and provides a ready to use environment
to spin up containerized or virtualized Android instance directly on the
Github runner.

See https://anbox-cloud.io/docs for more details

## Action inputs

* `channel`: Channel in the snap store of the `anbox-cloud-appliance` snap to use.
* `ubuntu-pro-token`: Token to attach the runner to Ubuntu Pro which is required to have
  access to the official images provided by Canonical.

## Example usage

```yaml
name: Run integration tests
on: push
jobs:
  run-tests:
    runs-on: ubuntu-latest
    steps:
    - name: Setup Anbox Cloud
      uses: canonical/anbox-cloud-github-action@main
      with:
        channel: 1.23/stable
        ubuntu-pro-token: ${{ secrets.UBUNTU_PRO_TOKEN }}
    - name: Create Android instance
      id: create-instance
      run: |
        set -x
        id="$(amc launch -r -s adb jammy:android13:amd64)"
        amc wait -c status=running "$id"
        echo "id=$id" >> "$GITHUB_OUTPUT"
    - name: Access Android over ADB
      run: |
        sudo apt install -y adb
        id=${{ steps.create-instance.outputs.id }}
        addr="$(amc show "$id" --format=json | jq -r .network.address)"
        adb connect "$addr":5559
    - name: Run tests
      run: |
        test "$(adb shell getprop ro.product.model)" = Anbox
    ...
```

# Licensing

The action is licensed under the terms of [the Apache-2.0 license](LICENSE.md).

## Contributing guidelines

The following resources can help you get started:

* [Report a bug](https://bugs.launchpad.net/anbox-cloud)

## More information

Explore more with the following resources:

* [Anbox Cloud documentation](https://anbox-cloud.io/docs)
* [Contact us](https://anbox-cloud.io/contact-us)
