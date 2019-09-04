# SLM-Env

Build Unity environment binaries for [SLM-Lab](https://github.com/kengz/SLM-Lab) and release on npm for easy distribution.

To use a prebuilt environment, just add its npm package, e.g. `yarn add slm-lab-unitywalker-v0`.

## Installation

Building a binary requires 3 things:

1. the Unity editor, installed via [Unity Hub](https://unity3d.com/get-unity/download). Go to `Unity Hub > Installs > Editor > Add Modules > Linux Build Support` to enable Linux builds.
2. [ml-agents repo](https://github.com/Unity-Technologies/ml-agents) with the environment's Unity assets:
  ```bash
  git clone https://github.com/Unity-Technologies/ml-agents.git
  ```
3. this repo:
  ```bash
  git clone https://github.com/kengz/SLM-Env.git
  ```

## Build a Unity Environment binary

The goal is to build MacOSX and Ubuntu binaries that can be used in `ml-agents`'s gym API. Currently this also means restriction to using only non-vector environments.

In this example, we will use the Walker environment. We also recommend first going through the Unity Hub tutorial to get a basic knowledge about the editor.

- [Build reference](https://github.com/Unity-Technologies/ml-agents/blob/master/docs/Learning-Environment-Executable.md)
- [Environment reference](https://github.com/Unity-Technologies/ml-agents/blob/master/docs/Learning-Environment-Examples.md)

1. Open the `ml-agents/UnitySDK` folder in the Unity editor.

2. In the Assets tab, find Walker under `ML-Agents > Examples > Walker > Scenes > Walker`. Hit the play button to preview it.

3. Make any necessary asset changes:
  1. to enable programmatic control, go to `WalkerAcademy` and check `control` in the Inspector tab.
  2. open the asset `Walker > Brains > WalkerLearning` and in the Inspector tab, change `Vector Observation > Stacked Vectors` to 1. Also, click on Model and delete it so we don't include the pretrained TF weights.
  3. Go to `Window > Rendering > LightingSettings` and uncheck `Realtime Global Illumination` and `Baked Global Illumination`. This is to prevent Enlighten from being used and spawning too many threads on Linux.

4. Now we're ready to build the binaries. Go to `File > Build Settings`:
  1. click `Add Open Scenes` and add your scene
  2. click `Player Settings` to show the Inspector tab. Check `Run in Background`, set `Display Resolution Dialog` to 'Disabled'. Optionally, set `Fullscreen Mode` to 'Windowed'.
  3. build one for Mac OS X. Hit `Build and Run` to render immediately after building. Choose the directory `SLM-Env/build/` and use the name `UnityWalker-v0`.
  4. build one for Linux. Hit `Build`, and use the same directory and name.

5. Test the binary. First ensure you have the `gym_unity` pip packages installed from ml-agents. Use the following script to run an example control loop:
  ```python
  from gym_unity.envs import UnityEnv

  env = UnityEnv('/Users/YOURNAME/SLM-Env/build/UnityWalker-v0', 0, multiagent=True)

  state = env.reset()
  for i in range(500):
      action = env.action_space.sample()
      state, reward, done, info = env.step(action)
  ```


## Usage

- git commit the binaries in `build/`, then push it.
- clone this repo under SLM Lab: `git clone https://github.com/kengz/SLM-Env.git ./slm_lab/env/SLM-Env`
