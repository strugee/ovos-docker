# Howto install skills?

Two different methods are supported to install a skill with Open Voice OS, each of them having pros :material-thumb-up-outline:{ style="color: green" } and cons :material-thumb-down-outline:{ style="color: red" }.

## In `ovos_core` container

The first method is to use a `skills.list` file within `~/ovos/config/` directory, this file acts as a Python `requirements.txt` file.

When `ovos_core` container starts, it will look for a `skills.list` file and install the skills defined in there.

!!! warning "Skill requirements"

    The skill has to be compatible with the `pip install` method which requires a `setup.py` file.

```ini title="skills.list"
ovos-skill-stop # Latest skill version on PyPi
ovos-skill-volume==0.0.1 # Specific skill version on PyPi
git+https://github.com/OpenVoiceOS/skill-ovos-wikipedia.git@fix/whatever # Specific skill's branch on GitHub
```

If the `ovos_core` container is wiped for any reason like an update, the skill(s) will be automatically reinstalled.

!!! tip "Not only for skills"

    `skills.list` file could be used as well to install extra Python librairies, *e.g.*, `SoCo`, `RPi.GPIO`.

The main advantage of this method is the simplicity, **but** the downside will be more Python dependencies *(libraries)* within the `ovos_core` container, potential conflicts across them and a slower start of the container.

## As standalone container *(recommended)*

The second method is to leverage the [ovos-workshop](../../../about/glossary/components.md#ovos-workshop) component by running a skill as standalone, it means the skill will not be part of `ovos_core` container but it will be running inside its own container.

The main advantage is that each skill are isolated which provide more flexibility about Python dependencies *(libraries)*, packages, easy to update and more security **but** the downside will be that more system resources will be consumed and a container image has to be built.

!!! note "Podman users :muscle:"

    If you are running Podman instead of Docker, replace `docker compose` with `podmand-compose`.

=== "Raspberry Pi"

    ```shell
    docker compose --project-name ovos --file docker-compose.yml --file docker-compose.raspberrypi.yml --file docker-compose.skills.yml up --detach
    ```

=== "Linux"

    ```shell
    docker compose --project-name ovos --file docker-compose.yml --file docker-compose.skills.yml up --detach
    ```

=== "Mac OS"

    ```shell
    docker compose --project-name ovos --file docker-compose.macos.yml --file docker-compose.skills.yml --env-file .env up --detach
    ```

=== "Windows WSL2"

    ```shell
    docker compose --project-name ovos --file docker-compose.windows.yml --file docker-compose.skills.yml up --detach
    ```

Depending your Internet speed, your Wi-Fi or Ethernet connection speed and your hardware *([I/O](https://en.wikipedia.org/wiki/Input/output))*, the whole process could take several minutes.

| Hardware                           | Time           |
| ---------------------------------- | -------------- |
| Raspberry Pi 3B+ with USB drive    | *~12 minutes*  |
| Raspberry Pi 4B with USB drive     | *~48 seconds*  |
| MacBook Air i7 Early 2015 with SSD | *~50 seconds*  |
| AMD Ryzen 7 5800 with NVMe drive   | *~15 seconds*  |

!!! danger "Resources overhead"

    To reduce the potential ressources overhead due to the image downloads and extracts, the `--parallel x` option could be added in order to process the images by batch of `x` *(where `x` is an integer)*.

If the `ovos_core` container is restarted or even deleted, the skill containers will automatically register again to it on up and running.

## Containers status

At this point of the installation, here are the containers that should be running.

=== "Docker"

    ```shell
    docker container list --all --filter 'name=ovos'
    CONTAINER ID   IMAGE                                        COMMAND                  CREATED        STATUS                 PORTS     NAMES
    1446b87d7a32   smartgic/ovos-skill-volume:alpha             "ovos-skill-launcher…"   18 hours ago   Up 8 hours                       ovos_skill_volume
    7ad46a871661   smartgic/ovos-skill-wikipedia:alpha          "ovos-skill-launcher…"   18 hours ago   Up 8 hours                       ovos_skill_wikipedia
    b43b8cf31a43   smartgic/ovos-skill-fallback-unknown:alpha   "ovos-skill-launcher…"   18 hours ago   Up 8 hours                       ovos_skill_fallback_unknown
    f27d3fceecec   smartgic/ovos-skill-alerts:alpha             "ovos-skill-launcher…"   18 hours ago   Up 8 hours                       ovos_skill_alerts
    30b70c9e72ef   smartgic/ovos-skill-hello-world:alpha        "ovos-skill-launcher…"   18 hours ago   Up 8 hours                       ovos_skill_hello_world
    f42175c6d7b8   smartgic/ovos-skill-weather:alpha            "ovos-skill-launcher…"   18 hours ago   Up 8 hours                       ovos_skill_weather
    0ae42a59fb0b   smartgic/ovos-skill-stop:alpha               "ovos-skill-launcher…"   18 hours ago   Up 8 hours                       ovos_skill_stop
    5760fb22deb9   smartgic/ovos-skill-date-time:alpha          "ovos-skill-launcher…"   18 hours ago   Up 8 hours                       ovos_skill_date_time
    73f4d4b0a091   smartgic/ovos-skill-personal:alpha           "ovos-skill-launcher…"   18 hours ago   Up 8 hours                       ovos_skill_personal
    219eb6254d32   smartgic/ovos-listener-dinkum:alpha          "/bin/bash /usr/loca…"   18 hours ago   Up 8 hours                       ovos_listener
    31f5d5e7a1ec   smartgic/ovos-audio:alpha                    "/bin/bash /usr/loca…"   18 hours ago   Up 8 hours                       ovos_audio
    05e94905b867   smartgic/ovos-core:alpha                     "/bin/bash /usr/loca…"   18 hours ago   Up 8 hours                       ovos_core
    d256c2e7b6f3   smartgic/ovos-phal:alpha                     "/bin/bash /usr/loca…"   18 hours ago   Up 8 hours                       ovos_phal
    a4db13a597a4   smartgic/ovos-phal-admin:alpha               "/bin/bash /usr/loca…"   25 hours ago   Up 8 hours                       ovos_phal_admin
    d157740c9965   smartgic/ovos-messagebus:alpha               "/bin/bash -c ovos-m…"   25 hours ago   Up 8 hours (healthy)             ovos_messagebus
    6e3536dcfae5   smartgic/ovos-cli:alpha                      "sleep infinity"         25 hours ago   Up 8 hours                       ovos_cli
    ```

=== "Podman"

    ```shell
    podman container list --all --filter 'name=ovos'
    CONTAINER ID   IMAGE                                        COMMAND                  CREATED      STATUS                PORTS     NAMESCONTAINER ID   IMAGE                                        COMMAND                  CREATED        STATUS                 PORTS     NAMES
    1446b87d7a32   smartgic/ovos-skill-volume:alpha             "ovos-skill-launcher…"   18 hours ago   Up 8 hours                       ovos_skill_volume
    7ad46a871661   smartgic/ovos-skill-wikipedia:alpha          "ovos-skill-launcher…"   18 hours ago   Up 8 hours                       ovos_skill_wikipedia
    b43b8cf31a43   smartgic/ovos-skill-fallback-unknown:alpha   "ovos-skill-launcher…"   18 hours ago   Up 8 hours                       ovos_skill_fallback_unknown
    f27d3fceecec   smartgic/ovos-skill-alerts:alpha             "ovos-skill-launcher…"   18 hours ago   Up 8 hours                       ovos_skill_alerts
    30b70c9e72ef   smartgic/ovos-skill-hello-world:alpha        "ovos-skill-launcher…"   18 hours ago   Up 8 hours                       ovos_skill_hello_world
    f42175c6d7b8   smartgic/ovos-skill-weather:alpha            "ovos-skill-launcher…"   18 hours ago   Up 8 hours                       ovos_skill_weather
    0ae42a59fb0b   smartgic/ovos-skill-stop:alpha               "ovos-skill-launcher…"   18 hours ago   Up 8 hours                       ovos_skill_stop
    5760fb22deb9   smartgic/ovos-skill-date-time:alpha          "ovos-skill-launcher…"   18 hours ago   Up 8 hours                       ovos_skill_date_time
    73f4d4b0a091   smartgic/ovos-skill-personal:alpha           "ovos-skill-launcher…"   18 hours ago   Up 8 hours                       ovos_skill_personal
    219eb6254d32   smartgic/ovos-listener-dinkum:alpha          "/bin/bash /usr/loca…"   18 hours ago   Up 8 hours                       ovos_listener
    31f5d5e7a1ec   smartgic/ovos-audio:alpha                    "/bin/bash /usr/loca…"   18 hours ago   Up 8 hours                       ovos_audio
    05e94905b867   smartgic/ovos-core:alpha                     "/bin/bash /usr/loca…"   18 hours ago   Up 8 hours                       ovos_core
    d256c2e7b6f3   smartgic/ovos-phal:alpha                     "/bin/bash /usr/loca…"   18 hours ago   Up 8 hours                       ovos_phal
    a4db13a597a4   smartgic/ovos-phal-admin:alpha               "/bin/bash /usr/loca…"   25 hours ago   Up 8 hours                       ovos_phal_admin
    d157740c9965   smartgic/ovos-messagebus:alpha               "/bin/bash -c ovos-m…"   25 hours ago   Up 8 hours (healthy)             ovos_messagebus
    6e3536dcfae5   smartgic/ovos-cli:alpha                      "sleep infinity"         25 hours ago   Up 8 hours                       ovos_cli
    ```