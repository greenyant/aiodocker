[![Build Status](https://travis-ci.org/zbyte64/aiodocker.svg?branch=master)](https://travis-ci.org/zbyte64/aiodocker)

# AsyncIO bindings for docker.io

A simple Docker HTTP API wrapper written with asyncio and aiohttp.


## Installation

```sh
pip install aiodocker
```

For development version:

```sh
pip install 'git+https://github.com/paultag/aiodocker#egg=aiodocker'
```


## Examples

```python
import asyncio
import aiodocker

async def list_things():
    docker = aiodocker.Docker()
    print('== Images ==')
    for image in (await docker.images.list()):
        print(f" {image['Id']} {image['RepoTags'][0] if image['RepoTags'] else ''}")
    print('== Containers ==')
    for container in (await docker.containers.list()):
        print(f" {container._id}")
    await docker.close()

async def run_container():
    docker = aiodocker.Docker()
    print('== Running a hello-world container ==')
    container = await docker.containers.create_or_replace(
        config={
            'Cmd': ['/bin/ash', '-c', 'echo "hello world"'],
            'Image': 'alpine:latest',
        },
        name='testing',
    )
    await container.start()
    logs = await container.log(stdout=True)
    print(''.join(logs))
    await container.delete(force=True)
    await docker.close()

if __name__ == '__main__':
    loop = asyncio.get_event_loop()
    loop.run_until_complete(list_things())
    loop.run_until_complete(run_container())
    loop.close()
```
