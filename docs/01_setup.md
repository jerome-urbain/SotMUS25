# <span style="color:#66C3FF">Setup<span>

This workshop needs a specific setup based on docker. 
Please follow the docker installation steps based on your OS (windows / linux / mac) to enjoy this workshop content!

<div align="center">
<img src="../figures/docker_logo.png" alt="docker" width="50%">
</div>

```Warning
If you're running this locally and the images don't display, you may need to convert ../figures into ./figures in all the .md files.
```

You can install Docker Desktop OR Docker Engine depending on your preferences.
If this is your first experience with docker or you're unsure, Docker Desktop is recommended.

----------------
## <span style="color:#66C3FF">Docker Desktop<span>
----------------

```{warning}
WARNING: carefully follow the installation steps and prerequisites mentioned in the documentation, and do not skip any of them.
```

### <span style="color:#66C3FF">Windows<span>

Install Docker Desktop on Windows:
<a href="https://docs.docker.com/desktop/install/windows-install">https://docs.docker.com/desktop/install/windows-install/</a>


### <span style="color:#66C3FF">Linux<span>
Install Docker Desktop on Linux:
<a href="https://docs.docker.com/desktop/install/linux-install/">https://docs.docker.com/desktop/install/linux-install/</a>



### <span style="color:#66C3FF">Mac OS<span>
Install Docker Desktop on Mac:
<a href="https://docs.docker.com/desktop/install/mac-install/">https://docs.docker.com/desktop/install/mac-install/</a>




----------------
## <span style="color:#66C3FF">Docker Engine<span>
----------------

If your OS is linux, you can also use a regular ```docker engine``` setup and combine it with ```docker compose``` installed as plugin:
- docker engine: <a href="https://docs.docker.com/engine/install/">https://docs.docker.com/engine/install/</a>
- docker compose plugin [deprecated but still working]: <a href="https://docs.docker.com/compose/install/linux/#install-using-the-repository">https://docs.docker.com/compose/install/linux/#install-using-the-repository</a>


As docker compose V1 stopped receiving updates from July 2023, it is recommended to use docker compose V2 or higher. (<a href="https://docs.docker.com/compose/">https://docs.docker.com/compose/</a>)

To check your installed _docker compose_ version, you can run the following command in your terminal:
```bash
docker compose version
```

----------------
## <span style="color:#66C3FF">Clone or download the github repository<span>
----------------

This workshop is based on a public github repository: <a href="https://github.com/jerome-urbain/SotMUS25">https://github.com/jerome-urbain/SotMUS25</a>
.


If you have git or git bash (Windows) installed, feel free to clone the workshop repository:

```bash
git clone https://github.com/jerome-urbain/SotMUS25.git
```

Otherwise, you can simply download it as zip file here:<a href="https://github.com/jerome-urbain/SotMUS25/archive/refs/heads/main.zip">https://github.com/jerome-urbain/SotMUS25/archive/refs/heads/main.zip</a>



