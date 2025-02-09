## Deploy LLM Model with WebUI Interface

### Download and Install Ollama

#### Online

```sh
curl -fsSL https://ollama.com/install.sh | sh
```

#### Offline

1. Download the Ollama installation package from the [releases page](https://github.com/ollama/ollama/releases).

```sh
ARCH=$(uname -m)
case "$ARCH" in
    x86_64) ARCH="amd64" ;;
    aarch64|arm64) ARCH="arm64" ;;
    *) error "Unsupported architecture: $ARCH" ;;
esac
curl --fail --show-error --location --progress-bar "https://ollama.com/download/ollama-linux-${ARCH}.tgz"
```

2. Install the downloaded file via script.

Fetch the installation script.

```sh
wget https://ollama.com/install.sh -O ollama_install.sh
chmod +x ollama_install.sh
```

Modify the script by changing the download command:

```sh
# curl --fail --show-error --location --progress-bar \
#     "https://ollama.com/download/ollama-linux-${ARCH}.tgz${VER_PARAM}" | \
#     $SUDO tar -xzf - -C "$OLLAMA_INSTALL_DIR"
$SUDO tar -xzf "./ollama-linux-${ARCH}.tgz" -C "$OLLAMA_INSTALL_DIR" # for local installation
```

Then run the installation script:

```sh
./ollama_install.sh
```

#### Pull Model and Run

Pull the model.

```sh
ollama pull deepseek-r1:8b
```

If you want to run the model and use it in the command line, run the following command:

```sh
ollama run deepseek-r1:8b
```

If you want to use local models, you can run the following command:

```sh
ollama create --model <model_name> --path <model_path>
```

#### Config Ollama

Edit the `/etc/systemd/system/ollama.service` file and add the following lines:

```sh
Environment="OLLAMA_MODELS=/data/lt/1/.ollama/" # 设置模型下载的路径
Environment="OLLAMA_HOST=0.0.0.0"   # 所有网络可访问
Environment="OLLAMA_ORIGINS=*"      # 所有网络可访问
```

Then restart the service:

```sh
sudo systemctl daemon-reload
sudo systemctl restart ollama
sudo systemctl status ollama
```


## Install and Run Open-WebUI

### Using Docker

If Open-WebUI and Ollama are in the same machine：

```sh
sudo docker run -d -p 3000:8080 --gpus all --add-host=host.docker.internal:host-gateway -v open-webui:/app/backend/data --name open-webui --restart always ghcr.io/open-webui/open-webui:main
```

If Open-WebUI and Ollama are in different machines：

```sh
sudo docker run -d -p 3000:8080 -e OLLAMA_BASE_URL=https://example.com -v open-webui:/app/backend/data --name open-webui --restart always ghcr.io/open-webui/open-webui:main
```

You can access Open-WebUI at `http://localhost:3000`.

### Using Python

Using Python 3.11 and run the following command:

```sh
pip install open-webui
open-webui serve
```

After that, you can access Open-WebUI at `http://localhost:8080`.