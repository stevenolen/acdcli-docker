# acdcli docker.

This is a [docker](https://www.docker.io) image to simplify the usage of [acd_cli](https://github.com/yadayada/acd_cli) which is a phenomenal CLI to use with the Amazon Cloud Drive service. It's particularly basic: mostly just resolves the dependencies and lets you easily separate the data from the cli.

## Usage

  * link volume to `/acd_cache` which contains the acd_cli cache data (including [auth setup](https://github.com/yadayada/acd_cli/blob/master/docs/authorization.rst#simple-appspot)). It's a bit annoying to do this process in a docker container, so I'll leave you to that original setup for now!
  * link volume to `/acd_data` which contains the source content you'd like to upload (note in example below I've mounted this read-only)
  * pass environment variables for `ARGS`, `SRC` and `DEST` that fit your needs.

The full example below is intended to be dropped in a crontab or similar and run as frequently as you'd like to sync (supplying a `--name` and `--rm=true` allows us to ensure very easily that a new container wont start if the previous container has not finished, and the container will not persist at all once the process is complete). A few options are also set (simultaneous threads, a regex to exclude). Finally, note that the `DEST` directory must already exist on cloud drive (any directories below that will be created by the acd_cli tool though).

```bash
docker run --rm=true --name acdcli \
  -v /path/to/data:/acd_data:ro \
  -v /path/to/cache:/acd_cache \
  -e ARGS="-x 4 -r 5 -o --exclude-regex \"^\.\"" \
  -e SRC="/acd_data/*" \
  -e DEST="/test/" \
  stevenolen/acdcli
```
