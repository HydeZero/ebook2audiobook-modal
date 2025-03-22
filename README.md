# 📚 ebook2audiobook-modal
CPU/GPU Converter from eBooks to audiobooks with chapters and metadata<br/>
using XTTSv2, Bark, Vits, Fairseq, YourTTS and more, designed for deployment on Modal services. Supports voice cloning and +1110 languages!
> [!IMPORTANT]
**This tool is intended for use with non-DRM, legally acquired eBooks only.** <br>
The authors are not responsible for any misuse of this software or any resulting legal consequences. <br>
Use this tool responsibly and in accordance with all applicable laws.

[![Discord](https://dcbadge.limes.pink/api/server/https://discord.gg/63Tv3F65k6)](https://discord.gg/63Tv3F65k6)

### Support the original ebook2audiobook developers!
[![Ko-Fi](https://img.shields.io/badge/Ko--fi-F16061?style=for-the-badge&logo=ko-fi&logoColor=white)](https://ko-fi.com/athomasson2) 

## Usage

> [!IMPORTANT]
This, by default, uses a H100 GPU, which, as of March 22nd, 2025, costs $4 an hour. Modal provides $30 free monthly as of the same date. So, assuming it uses 8 GiB of RAM and 1 CPU core, you should expect a runtime of 7 hours. Please make sure you have enough compute avaliable before running this.

### Requirements:
* `modal` installed and set up.
* A stable internet connection to maintain connection to the container.

### Setting Up


Create this folder structure somewhere:

```
ebook/
├── entrypoint.py
└── command.sh
```

Copy this into `entrypoint.py`:

```python
import modal

app = modal.App("ebook2audiobook")

@app.function(gpu="H100", timeout=86400) # feel free to change the gpu
def run():
    import time
    from subprocess import Popen, PIPE
    import os
    os.chdir("./ebook")
    Popen(["chmod", "+x", "command.sh"])
    time.sleep(5) # give linux enough time to chmod the file
    with Popen(['./command.sh'], stdout=PIPE) as p:
        while True:
            text = p.stdout.read1().decode("utf-8")
            print(text, end='', flush=True)
```

This ensures you can see any output, including the share link needed to access it.

Now, copy this into command.sh:

```bash
#!/bin/bash
apt-get update
apt-get install git -y
apt-get install sudo curl
git clone https://github.com/HydeZero/ebook2audiobook-modal.git
cd ebook2audiobook-modal
chmod +x ebook2audiobook.sh
./ebook2audiobook.sh --share
```

If all goes well, you can exit the directory, run `modal run -m ebook.entrypoint`, and go to the share url that will be displayed.
