# GPT Academic (in autodl machine)

> Ref: <https://github.com/binary-husky/gpt_academic>

```bash
source /etc/network_turbo
git clone --depth=1 git@github.com:binary-husky/gpt_academic.git
cd gpt_academic
cp config.py config_private.py
vim config_private.py
conda init
source ~/.bashrc
conda create -n gptac_venv python=3.11
conda activate gptac_venv
python -m pip install -r requirements.txt
python main.py
```
