# OpenAI Gym [![CircleCI](https://circleci.com/gh/kengz/openai_gym.svg?style=shield)](https://circleci.com/gh/kengz/openai_gym) [![Codacy Badge](https://api.codacy.com/project/badge/Grade/a0e6bbbb6c4845ccaab2db9aecfecbb0)](https://www.codacy.com/app/kengzwl/openai_gym?utm_source=github.com&amp;utm_medium=referral&amp;utm_content=kengz/openai_gym&amp;utm_campaign=Badge_Grade)

[OpenAI Gym Doc](https://gym.openai.com/docs) | [OpenAI Gym Github](https://github.com/openai/gym) | [RL intro](https://gym.openai.com/docs/rl) | [RL Tutorial video Part 1](https://youtu.be/qBhLoeijgtA) | [Part 2](https://youtu.be/wNSlZJGdodE)

(Under work) An experimentation system for Reinforcement Learning using OpenAI and Keras.


## Installation

### Basic

```shell
git clone https://github.com/kengz/openai_gym.git
cd openai_gym
bin/setup
```

Then, setup your `~/.keras/keras.json`. See example files in `rl/asset/keras.json`. We recommend Tensorflow for experimentation and multi-GPU, since it's much nicer to work with. Use Theano when you're training a single finalized model since it's faster.

The binary at `bin/setup` installs all the needed dependencies, which includes the basic OpenAI gym, Tensorflow (for dev), Theano(for faster production), Keras.

*Note the Tensorflow is defaulted to CPU Mac or GPU Linux. [If you're on a different platform, choose the correct binary to install from TF.](https://www.tensorflow.org/get_started/os_setup#pip_installation)*

```shell
# default option
TF_BINARY_URL=https://storage.googleapis.com/tensorflow/mac/cpu/tensorflow-0.12.1-py3-none-any.whl
# install from the TF_BINARY_URL
sudo pip3 install -U $TF_BINARY_URL
```

### Full OpenAI Gym Environments

To run more than just the classic control gym env, we need to install the OpenAI gym fully. We refer to the [Install Everything](https://github.com/openai/gym#installing-everything) of the repo (which is still broken at the time of writing).

```shell
brew install cmake boost boost-python sdl2 swig wget
git clone https://github.com/openai/gym.git
cd gym
pip3 install -e '.[all]'
```

Try to run a Lunar Lander env, it will break (unless they fix it):
```python
import gym
env = gym.make('LunarLander-v2')
env.reset()
env.render()
```

If it fails, debug as follow (and repeat once more if it fails again, glorious python):

```shell
pip3 uninstall Box2D box2d-py
git clone https://github.com/pybox2d/pybox2d
cd pybox2d/
python3 setup.py clean
python3 setup.py build
python3 setup.py install
```

To run Atari envs three additional dependencies are required

```shell
pip3 install atari_py
pip3 install Pillow
pip3 install PyOpenGL
```

Then check that it works with
```python
import gym
env = gym.make('SpaceInvaders-v0')
env.reset()
env.render()
```

## Usage

### Data files auto-sync (optional)

For auto-syncing `data/` files, we use Gulp. This sets up a watcher for automatically copying data files via Dropbox. Set up a shared folder in your Dropbox and sync to desktop at the path `~/Dropbox/openai_lab/data`.

```shell
npm install
npm install --global gulp-cli
# run the file watcher
gulp
```

### Run experiments locally

Configure the `"start"` scripts in `package.json` for easily running the same experiments over and over again.

```shell
# easy run command
npm start
# to clear data/
npm run clear
```

To customize your run commands, use plain python:

```shell
python3 main.py -bgp -s lunar_dqn -t 5 | tee -a ./data/terminal.log
```

The extra flags are:

- `-d`: log debug info. Default: `False`
- `-b`: blind mode, do not render graphics. Default: `False`
- `-s <sess_name>`: specify which of `rl/asset/sess_spec.json` to run. Default: `-s dev_dqn`
- `-t <times>`: the number of sessions to run per experiment. Default: `1`
- `-e <experiments>`: the max number of experiments: hyperopt max_evals to run. Default: `10`
- `-p`: run param selection. Default: `False`
- `-l`: run `line_search` instead of Cartesian product in param selection. Default: `False`
- `-g`: plot graphs live. Default: `False`


### Run experiments remotely

Log in via ssh, start a screen, run, then detach screen.

```shell
screen
# enter the screen
npm run remote
# or full python command goes like
xvfb-run -a -s "-screen 0 1400x900x24" -- python3 main.py -bgp -s lunar_dqn -t 5 | tee -a ./data/terminal.log
# use Cmd+A+D to detach from screen, then Cmd+D to disconnect ssh
# use screen -r to resume screen next time
```


## Development

This is still under active development, and documentation is sparse. The main code lives inside `rl/`.

The design of the code is clean enough to simply infer how things work by example.

- `data/`: contains all the graphs per experiment sessions, JSON data file per experiment, and csv metrics dataframe per run of multiple experiments
- `rl/agent/`: custom agents. Refer to `base_agent.py` and `dqn.py` to build your own
- `rl/asset/`: specify new problems and sess_specs to run experiments for.
- `rl/memory/`: RL agent memory classes
- `rl/policy/`: RL agent policy classes
- `rl/preprocessor/`: RL agent preprocessor (state and memory) classes
- `rl/experiment.py`: the main high level experiment logic.
- `rl/hyperoptimizer.py`: Hyperparameter optimizer for the Experiments
- `rl/util.py`: Generic util

Each run is by specifying a `sess_name` or `sess_id`. This runs experiments sharing the same `prefix_id`. Each experiment runs multiple sessions to take the average metrics and plot graphs. At last the experiments are aggregated into a metrics dataframe, sorted by the best experiments. All these data and graphs are saved into a new folder in `data/` named with the `prefix_id`.


## Roadmap

Check the latest under the [Github Projects](https://github.com/kengz/openai_gym/projects)

## Authors

- Wah Loon Keng
- Laura Graesser
