# <span style="color:darkblue">Setup<span>

This workshop needs a specific setup based on docker. 
Please follow the docker installation steps based on your OS (windows / linux / mac) to enjoy this workshop content! 

```{image} ./figures/docker_logo.png
:alt: docker
:width: 300px
:align: center
```

You can install Docker Desktop OR Docker Engine depending on your preferences.
If this is your first experience with docker or you're unsure, Docker Desktop is recommended.

----------------
## <span style="color:darkblue">Docker Desktop<span>
----------------

```{warning}
WARNING: carefully follow the installation steps and prerequisites mentioned in the documentation, and do not skip any of them.
```

### <span style="color:darkblue">Windows<span>

Install Docker Desktop on Windows:
https://docs.docker.com/desktop/install/windows-install/


### <span style="color:darkblue">Linux<span>
Install Docker Desktop on Linux:
https://docs.docker.com/desktop/install/linux-install/


### <span style="color:darkblue">Mac OS<span>
Install Docker Desktop on Mac:
https://docs.docker.com/desktop/install/mac-install/ 



----------------
## <span style="color:darkblue">Docker Engine<span>
----------------

If your OS is linux, you can also use a regular ```docker engine``` setup and combine it with ```docker compose``` installed as plugin:
- docker engine: https://docs.docker.com/engine/install/
- docker compose plugin [deprecated but still working]: https://docs.docker.com/compose/install/linux/#install-using-the-repository


As docker compose V1 stopped receiving updates from July ZO23, it is recommended to use docker compose V2 or higher. (https://docs.docker.com/compose/)

To check your installed _docker compose_ version, you can run the following command in your terminal:
```bash
docker compose version
```

----------------
## <span style="color:darkblue">Clone or download the gitlab repository<span>
----------------

This workshop is based on a public gitlab repository:
https://gitlab.com/bdeswysen/sotm_workshop .


If you have git or git bash (Windows) installed, feel free to clone the workshop repository:

```bash
git clone https://gitlab.com/bdeswysen/sotm_workshop.git
```

Otherwise, you can simply download it as zip file here:
https://gitlab.com/bdeswysen/sotm_workshop/-/archive/main/sotm_workshop-main.zip


