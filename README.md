# SLM-Env

Build Unity environment binaries for [SLM-Lab](https://github.com/kengz/SLM-Lab) and release on npm for easy distribution.

To use a prebuilt environment, just add its npm package, e.g. `yarn add slm-lab-3dball`.

## Installation

Building a binary requires 4 things:

1. [Node.js with `npm`](https://nodejs.org/en/download/package-manager/)
2. the Unity editor, installed via [Unity Hub](https://unity3d.com/get-unity/download). Go to `Unity Hub > Installs > Editor > Add Modules > Linux Build Support` to enable Linux builds.
3. [ml-agents repo](https://github.com/Unity-Technologies/ml-agents) with the environment's Unity assets:
  ```bash
  git clone https://github.com/Unity-Technologies/ml-agents.git
  ```
4. this repo:
  ```bash
  git clone https://github.com/kengz/SLM-Env.git
  ```

## Build a Unity Environment binary

The goal is to build MacOSX and Ubuntu binaries that can be used in `ml-agents`'s gym API. Currently this also means restriction to using only non-vector environments.

In this example, we will use the Walker environment. We also recommend first going through the Unity Hub tutorial to get a basic knowledge about the editor. [Reference from here](https://github.com/Unity-Technologies/ml-agents/blob/master/docs/Learning-Environment-Executable.md).

1. Open the `ml-agents/UnitySDK` folder in the Unity editor.

2. In the Assets tab, find Walker under `ML-Agents > Examples > Walker > Scenes > Walker`. Hit the play button to preview it.

3. Make any necessary asset changes:
  1. to enable programmatic control, go to `WalkerAcademy` and check `control` in the Inspector tab.
  2. since we're not supporting vector environments, remove the extra walker clones but selecting all but the first `WalkerPair` game objects unchecking them in the Inspector tab.
  3. next, open the asset `Walker > Brains > WalkerLearning` and in the Inspector tab, change `Vector Observation > Stacked Vectors` to 1. Also, click on Model and delete it so we don't include the pretrained TF weights.

Go to `Edit > Project Settings > Player > Resolution and Presentation`. Ensure `Run in Background (checked)` and `Display Resolution Dialog (Disabled)`.

4. Now we're ready to build the binaries. Go to `File > Build Settings`:
  1. click `Add Open Scenes` and add your scene
  2. click `Player Settings` to show the Inspector tab. Check `Run in Background`, set `Display Resolution Dialog` to 'Disabled'. Optionally, set `Fullscreen Mode` to 'Windowed'.
  3. build one for Mac OS X. Hit `Build and Run` to render immediately after building. Choose the directory `SLM-Env/bin/` and use the name `unitywalker-v0`.
  4. build one for Linux. Hit `Build`, and use the same directory and name.

5. Test the binary. First ensure you have the `mlagents_envs` (version `0.9.2`) and `gym_unity` pip packages installed from ml-agents. Use the following script to run an example control loop:
  ```python
  from gym_unity.envs import UnityEnv

  env = UnityEnv('/Users/YOURNAME/SLM-Env/bin/unitywalker-v0', 0)

  state = env.reset()
  for i in range(500):
      action = env.action_space.sample()
      state, reward, done, info = env.step(action)
  ```

6. Git-commit the `bin` folder in the SLM-Env repo.

The binary is now ready. Next, release it to `npm`.


## Release

>Note: use kebab-case naming convention with prefix `slm-env` and OpenAI gym convention, so `slm-env-unitywalker-v0`

1. Open up `package.json` and update:
  - replace `envname` as appropriate: `"name": "slm-env-unitywalker-v0",`
  - update version

2. Copy both the MacOSX and Linux binary files from `bin/` to `build/`

3. Release to `npm` (make sure you are logged in first, by `npm login`):
  ```bash
  npm publish
  ```

  Since the binaries are huge, `npm` will throw an error near the end of it. Just ignore that.
  ```shell
  npm ERR! registry error parsing json
  npm ERR! publish Failed PUT 403
  npm ERR! code E403
  npm ERR! You cannot publish over the previously published version 1.0.0. : slm-env-unitywalker-v0
  ```
  It should be available on [npmjs.com](https://www.npmjs.com/), just search for your package `slm-env-unitywalker-v0`.

4. Add the release to `SLM-Lab` for usage: `yarn add slm-env-3dball`
