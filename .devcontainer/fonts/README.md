# Installing Nerd Fonts

This folder will contain patched Fonts aka Nerd Fonts

They provide the ability to show ligatures and symbols in the console

Any fonts here will be copied in via the DockerFile

## Dockerfile Command

```Docker
COPY .devcontainer/fonts/*.ttf /usr/share/fonts/truetype/
```

## Font Config

```bash
sudo apt update && sudo apt install fontconfig -y
```