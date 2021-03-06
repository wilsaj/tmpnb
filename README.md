## tmpnb

[![Gitter](https://badges.gitter.im/Join Chat.svg)](https://gitter.im/jupyter/tmpnb?utm_source=badge&utm_medium=badge&utm_campaign=pr-badge&utm_content=badge)

Launches "temporary" Jupyter notebook servers.

#### Quick start

Get Docker, then:

```
docker pull jupyter/minimal
export TOKEN=$( head -c 30 /dev/urandom | xxd -p )
docker run --net=host -d -e CONFIGPROXY_AUTH_TOKEN=$TOKEN --name=proxy jupyter/configurable-http-proxy --default-target http://127.0.0.1:9999
docker run --net=host -d -e CONFIGPROXY_AUTH_TOKEN=$TOKEN --name=tmpnb -v /var/run/docker.sock:/docker.sock jupyter/tmpnb
```

BAM! Visit your host on port 8000 and you have a working tmpnb setup.

If it didn't come up, try running `docker ps -a` and `docker logs tmpnb` to help diagnose issues.

#### Advanced configuration

If you need to set the `docker-version` or other options, they can be passed to `jupyter/tmpnb` directly:

```
docker run --net=host -d -e CONFIGPROXY_AUTH_TOKEN=$TOKEN -v /var/run/docker.sock:/docker.sock jupyter/tmpnb python orchestrate.py --cull-timeout=60 --docker-version="1.13" --command="ipython3 notebook --NotebookApp.base_url={base_path}" --ip=0.0.0.0 --port {port}"
```

#### Launching with *your own* Docker images

tmpnb can run any Docker container provided by the `--image` option, so long as the `--command` option tells where the `{base_path}` and `{port}`. Those are literal strings, complete with curly braces that tmpnb will replace with an assigned `base_path` and `port`.

```
docker run --net=host -d -e CONFIGPROXY_AUTH_TOKEN=$TOKEN \
           -v /var/run/docker.sock:/docker.sock \
           jupyter/tmpnb python orchestrate.py --image='jupyter/demo' --command="ipython3 notebook --NotebookApp.base_url={base_path}" --ip=0.0.0.0 --port {port}"
```

#### Options

```
Usage: orchestrate.py [OPTIONS]

Options:

  --command                        command to run when booting the image. A
                                   placeholder for base_path should be
                                   provided. Example: "ipython3 notebook
                                   --NotebookApp.base_url=/{base_path}"
  --container_ip                   IP address for containers to bind to
                                   (default 127.0.0.1)
  --container_port                 Port for containers to bind to (default
                                   8888)
  --cpu_shares                     Limit CPU shares, per container
  --cull_period                    Interval (s) for culling idle containers.
                                   (default 600)
  --cull_timeout                   Timeout (s) for culling idle containers.
                                   (default 3600)
  --docker_version                 Version of the Docker API to use (default
                                   1.13)
  --help                           show this help information
  --image                          Docker container to spawn for new users.
                                   Must be on the system already (default
                                   jupyter/minimal)
  --max_dock_workers               Maximum number of docker workers (default 2)
  --mem_limit                      Limit on Memory, per container (default
                                   512m)
  --pool_name                      Container name fragment used to identity
                                   containers that belong to this instance.
  --pool_size                      Capacity for containers on this system. Will
                                   be prelaunched at startup. (default 128)
  --port                           port for the main server to listen on
                                   (default 9999)
  --redirect_uri                   URI to redirect users to upon initial
                                   notebook launch (default /tree)
  --static_files                   Static files to extract from the initial
                                   container launch
```

#### Development

```
git clone https://github.com/jupyter/tmpnb.git
cd tmpnb

# Kick off the proxy and run the server.
# Runs on all interfaces on port 8000 by default.
make dev
```
